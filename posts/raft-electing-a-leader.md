---
layout: post
date: 2016-08-01 17:49:00 ET
title: "Raft: Electing a leader"
---

# Raft: Electing a Leader

The distributed key value store I’m building at the Recurse Center uses the Raft consensus algorithm. Here’s how nodes elect a leader:

### Timeouts

Raft has two timeouts, the heartbeat timeout and the election timeout, randomized between 150 and 300 milliseconds. The heartbeat timeout is used by the leader to tell the other nodes that it is still running. When nodes get a request from the leader, they [reset](https://medium.com/@arpith/resetting-a-ticker-in-go-63858a2c17ec#.c73ty71bl) their election timeout.

### Elections

If a node’s election timeout goes off before receiving a request from the leader, it promotes itself to candidate and [starts a new election term](https://github.com/arpith/mmapd/blob/31306c84ea09fba57fb3f24ca816d341fe26cdbf/raft/voting.go). It votes for itself and sends vote requests to the other nodes.

    func (s *server) startElection() {
        s.state = "candidate"
        s.term += 1
        s.votedFor = s.id
        voteCount := 1
        respChan := make(chan voteResponse)
        for receiverIndex, receiverId := range s.config {
            if receiverId != s.id {
                go s.sendRequestForVote(receiverIndex, respChan)
            }
        }

### Voting

The other nodes vote for the first request they receive and reject subsequent requests. They also check to make sure that the candidate’s log has at least as many entries as theirs.

    func (s *server) handleRequestForVote(v voteRequest) {
        req := v.Req
        returnChan := v.ReturnChan
        if req.Term < s.term {
            resp := &requestForVoteResponse{s.term, false}
            returnChan <- *resp
        } else {
            if req.Term > s.term {
                s.votedFor = ""
                s.stepDown("Got vote request with term > current term")
            }
            cond1 := s.votedFor == ""
            cond2 := s.votedFor == req.CandidateID
            cond3 := req.LastLogIndex >= len(s.db.Log.Entries)-1
            if (cond1 || cond2) && cond3 {
                s.votedFor = req.CandidateID
                s.term = req.Term
                resp := &requestForVoteResponse{s.term, true} 
                returnChan <- *resp
            }
        }
    }

If the candidate receives votes from the majority of the nodes, it promotes itself to leader and begins sending out heartbeats.

    if len(s.config) > 1 {
        for {
            vote := <-respChan
            responseCount++
            if vote.Resp.Term > s.term {
                s.stepDown("Vote response has term > current term")
                break
            }
            if vote.Resp.HasGrantedVote {
                voteCount++
            }
            if voteCount > (len(s.config)-1)/2 {
                s.becomeLeader()
                break
            }
            if responseCount == len(s.config)-2 {
                break
            }
        }
    }

This is also the strategy to elect the initial leader! Building a distributed system? [Get](http://github.com/arpith) [in](http://twitter.com/arpith) [touch](mailto:arpith@feedreader.co)!
