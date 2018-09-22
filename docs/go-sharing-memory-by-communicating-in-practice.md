---
layout: post
date: 2016-07-09 23:22:00 ET
title: "Go: Sharing memory by communicating in practice"
---

# Go: Sharing Memory By Communicating In Practice

As I mentioned in my last post, I’m writing a distributed in-memory database in Go. As a first step towards this, I wrote a server that listens to set/get requests and stores the data in a map. In Go, each incoming request is handled in its own goroutine. However, a map is not safe for concurrent use. Here’s how I approached the problem:

![](https://cdn-images-1.medium.com/max/8192/1*S41bNcPfWq8ilS0IvQTV_Q.jpeg)

[Maps can be protected using locks](https://blog.golang.org/go-maps-in-action), but inspired by [this post on sharing memory by communicating](https://blog.golang.org/share-memory-by-communicating), I wondered if there was way to use channels instead.

    func (db *db) writer() {
        for {
            req := <-db.writeChan
            db.dataMap[req["key"]] = req["value"]
            b, err := json.Marshal(db.dataMap)
            if err != nil {
                fmt.Println("Error marshalling db: ", err)
            }
            if len(b) > len(db.data) {
                db.extend(len(b))
            }
            copy(db.data, b)
        }
    }
     
    func (db *db) handler(w http.ResponseWriter, r *http.Request, ps httprouter.Params) {
        switch r.Method {
        case "GET":
            key := ps.ByName("key")
            value := db.dataMap[key]
            fmt.Fprintln(w, value)
        case "POST":
            m := make(map[string]string)
            m["key"] = ps.ByName("key")
            m["value"] = r.FormValue("value")
            db.writeChan <- m
            fmt.Fprintln(w, r.FormValue("value"))
        }
    }

When a POST request is received, the key and value are passed to the writer goroutine via a channel. This goroutine is in charge of writing to the underlying map, and handles requests one at a time. However, I haven’t figured out how to handle GET’s — the map is being read by multiple goroutines.

Do you know of a better way to do this? [Let](http://twitter.com/arpith) [me](http://arpith.co) [know](mailto:%20arpith@feedreader.co)!

Bonus fun fact: I ran into [this bug](http://stackoverflow.com/questions/26228163/localhost-no-such-host-after-250-connections-in-go-when-using-responsewriter).

Special thanks to [Nifty Nei](undefined), [Irina Gossman](https://github.com/margold), [Daniel Cadden](https://twitter.com/shikkic) and Kamron Saniee!
