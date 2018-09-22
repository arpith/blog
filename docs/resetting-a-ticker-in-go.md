---
layout: post
date: 2016-07-14 13:40:00 ET
title: "Resetting a ticker in Go"
---

# Resetting a ticker in Go

Raft is the distributed consensus algorithm that I’m using for the database I’m building at the Recurse Center. The algorithm has two timeouts — a heartbeat timeout and an election timeout. When a server receives a heartbeat from the leader, it resets its election timeout. If it doesn’t receive a heartbeat before its election timeout runs out, it tries to elect itself as leader!

As a first step towards this, I need to write code that resets a timeout (say 2 seconds) every time another timeout (say 5 seconds) expires. I would expect to see output that looked like:

    Elapsed: 0
    Elapsed: 2 Ticker A
    Elapsed: 4 Ticker A
    Elapsed: 5 Ticker B - Going to reset Ticker A!
    Elapsed: 7 Ticker A
    Elapsed: 9 Ticker A

If I hadn’t reset the other ticker, of course, I would expect to see:

    Elapsed: 0 
    Elapsed: 2 Ticker A
    Elapsed: 4 Ticker A
    Elapsed: 5 Ticker B
    Elapsed: 6 Ticker A
    Elapsed: 8 Ticker A

Here’s how I wrote this in Go, using [ticking channels](https://golang.org/pkg/time/#Tick). I set up a goroutine that listens on two channels, tickerA and tickerB. Every time tickerB goes off, it resets tickerA.

    type server struct {
        doneChan chan bool
        tickerA  ticker
        tickerB  ticker
    }

    func (s *server) listener() {
        start := time.Now()
        for {
            select {
            case <-s.tickerA.ticker.C:
                elapsed := time.Since(start)
                fmt.Println("Elapsed: ", elapsed, " Ticker A")
            case <-s.tickerB.ticker.C:
                s.tickerA.resetTicker()
                elapsed := time.Since(start)
                fmt.Println("Elapsed: ", elapsed, " Ticker B - Going to reset ticker A")
            }
        }
        s.doneChan <- true
    }

    func main() {
        doneChan := make(chan bool)
        tickerA := createTicker(2 * time.Second)
        tickerB := createTicker(5 * time.Second)
        s := &server{doneChan, *tickerA, *tickerB}
        go s.listener()
        <-doneChan
    }

And my tickers and the methods to create and reset them look like:

    type ticker struct {
        period time.Duration
        ticker time.Ticker
    }

    func createTicker(period time.Duration) *ticker {
        return &ticker{period, *time.NewTicker(period)}
    }

    func (t *ticker) resetTicker() {
        t.ticker = *time.NewTicker(t.period)
    }

The actual output is:

    $ go run tick.go

    Elapsed:  2.00074535s  Ticker A

    Elapsed:  4.002086857s  Ticker A

    Elapsed:  5.004973744s  Ticker B - Going to reset ticker A

    Elapsed:  7.008515813s  Ticker A

    Elapsed:  9.009572001s  Ticker A

That’s about it! [Check it out](https://play.golang.org/p/q5CthVZzW6)!

Have a better way to do this? Just want to say hi? [I’m](http://arpith.co) [on Twitter](http://twitter.com/arpith)!
