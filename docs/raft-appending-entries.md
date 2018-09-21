
# Raft: Appending Entries

I’m building a distributed key value store at the Recurse Center, and it uses the Raft protocol to replicate its log across nodes. Here’s how you append an entry to the log in Raft!

### Client Request

When the leader gets a request from a client it [appends the entry](https://github.com/arpith/mmapd/blob/c936e61b599b835e3a859fa86fab184af5728ea8/raft/appending.go) to its log and sends out requests to its followers to append this entry. It also increments its nextIndex and matchIndex values.

    func (s *server) appendEntry(command string, key string, value string, isCommitted chan bool) {
        entry := &db.Entry{command, key, value, s.term}
        index := -1
        if command != "" {
            s.db.Log.AppendEntry(*entry)
            index = len(s.db.Log.Entries) - 1
        }
        respChan := make(chan followerResponse)
        for i := 0; i < len(s.config); i++ {
            if s.config[i] != s.id {
                go s.sendAppendEntryRequest(i, index, respChan)
            } else {
                if command != "" {
                    //Update nextIndex and matchIndex for self
                    s.nextIndex[i]++
                    s.matchIndex[i]++
                }
            }
        }

### Append Entry Request

The leader sends its term, and the index and term of the previous entry in its log. The follower will reject the request if the leaders term is lower than the follower’s current term — there is a newer leader! The request will also be rejected if the previous entry’s term doesn’t match with the term stored in the follower’s log.

    func (s *server) handleAppendEntryRequest(a appendRequest) {
        returnChan := a.ReturnChan
        req := a.Req
        if req.Term < s.term {
            resp := &appendEntryResponse{s.term, false}
            returnChan <- *resp
        } else if req.PrevLogIndex > -1 &&
            len(s.db.Log.Entries) > req.PrevLogIndex &&
            s.db.Log.Entries[req.PrevLogIndex].Term != req.PrevLogTerm {
            resp := &appendEntryResponse{s.term, false}
            returnChan <- *resp
        } 

If the request isn’t rejected, the receiver then checks if it needs to step down — for instance, if it is a leader and the new leader has a term greater than the term it has stored.

    else {
        if req.Term > s.term {
            s.term = req.Term
            s.stepDown("append entries RPC has term > current term")
        }

The receiver then deletes any entry in its log that conflicts with the entry sent by the leader, and all the ones after it, and then appends the entry!

    if len(s.db.Log.Entries) > req.PrevLogIndex+2 {
        if s.db.Log.Entries[req.PrevLogIndex+1].Term != req.Term {
            // If existing entry conflicts with new entry
            // Delete entry and all that follow it
            s.db.Log.SetEntries(s.db.Log.Entries[:req.PrevLogIndex+1])
        }
        s.db.Log.AppendEntry(req.Entry)
    }

The receiver then sets its commit index to be the minimum of the leader’s commit index and the index of the last new entry (which in this case is the index after the leader’s previous log index).

    if req.LeaderCommit > s.commitIndex {
        // Set commit index to the min of the leader's commit index 
        // and index of last new entry
        if req.Entry.Command == "" || 
            req.LeaderCommit < req.PrevLogIndex+1 {    
            s.commitEntries(req.LeaderCommit)
       } else {
            s.commitEntries(req.PrevLogIndex + 1)
       }
    }

Finally, he follower responds with its current term and success set to true.

    resp := &appendEntryResponse{s.term, true}
    returnChan <- *resp

### Updating Commit Index

After sending the append entry requests to the followers, the leader checks if there is an entry in the log with index greater than the current commit index (and term equal to the current term) that a majority of followers have successfully replicated. The leader then updates its commit index to be the index of this entry.

    for {
        _ = <-respChan
        responseCount++
        for N := s.commitIndex + 1; N < len(s.db.Log.Entries); N++ {
            //Check if there exists an N > commitIndex
            count := 0
            for i := 0; i < len(s.matchIndex); i++ {
                if s.matchIndex[i] >= N {
                    count++
                }
            }
            // Check if a majority of matchIndex[i] >= N
            cond1 := count > len(s.matchIndex)/2
            // Check if log[N].term == currentTerm
            cond2 := s.db.Log.Entries[N].Term == s.term
            if cond1 && cond2 {
                // Set commitIndex to N
                s.commitIndex = N
            } else {
                break
            }
        }
        if s.commitIndex == index {
            isCommitted <- true
            return
        }
        if responseCount == len(s.config)-1 {
            fmt.Println("Got all responses!")
        }
    }

### Responding to the client

If the entry was successfully committed, the leader then stores the entry in the database and [responds to the client](https://github.com/arpith/mmapd/blob/c936e61b599b835e3a859fa86fab184af5728ea8/raft/clientRequests.go).

    func (s *server) handleWriteRequest(req writeRequest) {
        key := req.key
        value := req.value
        command := "SET"
        c := make(chan bool)
        go s.appendEntry(command, key, value, c)
        isCommitted := <-c
        close(c)
        if isCommitted {
            m := db.WriteChanMessage{key, value, req.returnChan}
            s.db.WriteChan <- m
        } else {
            m := &db.ReturnChanMessage{errors.New("Couldn't Commit"), ""}
            req.returnChan <- *m
        }
    }

That’s it! Have thoughts, comments or feedback? [Let](mailto:arpith@feedreader.co) [me](http://github.com/arpith) [know](http://twitter.com/arpith)!
