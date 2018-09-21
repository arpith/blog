
# Reading a Go map safely

My last post explained how I wrote to a Go map from multiple goroutines (safely!). To recap, I setup a goroutine to handle the writes, and passed the write requests (the key and the value) to it over a channel from the goroutines that handled the http requests. Here’s how I handle concurrent reads!

The goroutine that handled writes to the map now also listens (and is now called a listener instead!) on a read channel, through which the http handlers can pass a key as well as a channel that *they *will now listen on. The [listener](https://github.com/arpith/mmapd/blob/4ffe8ea74a1bf366afb33f3818263d104d51fc22/db.go) now looks like:

    func (db *db) listener() {
        for {
            select {
            case writeReq := <-db.writeChan:
                key := writeReq["key"]
                value := writeReq["value"]
                db.dataMap[key] = value
                b, err := json.Marshal(db.dataMap)
                if err != nil {
                    fmt.Println("Error marshalling db: ", err)
                }
                if len(b) > len(db.data) {
                    db.extend(len(b))
                }
                copy(db.data, b)
            case readReq := <-db.readChan:
                key := readReq.key
                returnChan := readReq.returnChan
                if value, ok := db.dataMap[key]; ok {
                    returnChan <- value
                } else {
                    returnChan <- "ERROR: NOT FOUND"
                }
            }
        }
    }

The message sent over the read channel is:

    type readChanMessage struct {
        key        string
        returnChan chan string
    }

And finally, the [http handler](https://github.com/arpith/mmapd/blob/4ffe8ea74a1bf366afb33f3818263d104d51fc22/main.go) ends up looking like:

    func (db *db) handler(w http.ResponseWriter, r *http.Request, ps httprouter.Params) {
        switch r.Method {
        case "GET":
            key := ps.ByName("key")
            c := make(chan string)
            m := readChanMessage{key, c}
            db.readChan <- m
            value := <-c
            close(c)
            if value == "ERROR: NOT FOUND" {
                http.NotFound(w, r)
            } else {
                fmt.Fprint(w, value)
            }
        case "POST":
            m := make(map[string]string)
            m["key"] = ps.ByName("key")
            m["value"] = r.FormValue("value")
            db.writeChan <- m
            fmt.Fprint(w, r.FormValue("value"))
        }
    }

I like this solution because:

1. I can split the http handling from the database reads

1. In particular, I don’t have to put http.NotFound in my database listener

The other option was to pass the http response writer over the read channel instead of using a return channel. [Willa Drengwitz](http://twitter.com/ghthor) (thanks!) pointed out that there is a disadvantage to this— this will block all other reads/writes if the loop ends up waiting to write the http response.

Have thoughts or suggestions? [Let](http://twitter.com/arpith) [me](http://arpith.co) [know](mailto:%20arpith@feedreader.co)!
