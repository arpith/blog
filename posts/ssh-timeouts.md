---
layout: post
date: 2019-03-03 12:21:00 PT
title: "Fun with SSH Timeouts"
---
# Fun with SSH Timeouts
This week at work I was looking at an issue where a tool we had written to run a command on multiple servers would get stuck on a server that was under some load. The way we had written it was to run the command using ssh, like `ssh host command`, on different threads (one per server).

My first thought was that ssh should have a way to set a timeout on idle connections, and indeed it does - `ssh -o serveraliveinterval=15` sends a message every 15 seconds to the server requesting a response. Interestingly, this option was already being set by the tool. So I explored `ServerAliveMaxCount` which sets how long the client will wait before disconnecting if the server is unresponsive. Setting it to 0 would disconnect the ssh connection after 15 seconds, but setting it to anything else (for example, the default which is 3) would keep the connection open.

In order to to make sure the options were working as described in the man page, I ran `ssh -o serveraliveinterval=15 $SERVER "iptables -A OUTPUT -d $SSH_CLIENT -j DROP && sleep 180 && iptables -A OUTPUT -d $SSH_CLIENT -j ACCEPT"` on a functioning server to prevent it from sending packets back to my laptop for some time. This made the connection drop after 45 seconds, which was great!

So I discussed the issue with a coworker and the specific case was that the server was swapping, so the command couldn't actually run. However, we believe that in this case the ssh server was still able to respond to the keep alive messages from the client, which was why the connection wasn't timing out.

We finally decided that a combination of an optional hard timeout and a fix to the tool to allow killing the processes that were stuck would be a reasonable fix to this problem. I ended up implementing the timeout with a thread that would sleep for the provided time and then just kill the ssh process, and he implemented the fix that passed on Ctrl-C's.

I liked this issue because it helped me understand what our tool was doing, learn about SSH timeouts and fix something that impacted engineers outside our team.
