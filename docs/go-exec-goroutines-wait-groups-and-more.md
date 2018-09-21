---
layout: post
date: 2015-02-13 06:20:00 GMT
title: "Go: exec, goroutines, wait groups and more"
---
# Go: exec, goroutines, wait groups and more

I wrote a [small program in go](https://github.com/arpith/slides2video/blob/master/main.go) to [export slides to video](https://github.com/arpith/slides2video/) using [ffmpeg](https://www.ffmpeg.org). It [creates a video for each slide](http://trac.ffmpeg.org/wiki/Create%20a%20video%20slideshow%20from%20images) then [concatenates them](http://trac.ffmpeg.org/wiki/Concatenate) and finally [adds the audio](http://stackoverflow.com/questions/11779490/ffmpeg-how-to-add-new-audio-not-mixing-in-video).

For the first step, I didn't need to create the (single image) videos one after the other, so I used [goroutines](https://gobyexample.com/goroutines) to run them in separate threads.

    wg.Add(1)
    go img2video(imgName, imgDuration, outputName)

Running external commands is [reasonably straight-forward](http://golang.org/pkg/os/exec/#example_Cmd_Start), and so is [accessing error messages](http://stackoverflow.com/questions/18159704/how-to-debug-exit-status-1-error-when-running-exec-command-in-golang)

    func img2video(imgName string, imgDuration string, outputName string) {
        defer wg.Done()
        var stderr bytes.Buffer
        cmd := exec.Command("ffmpeg", "-loop", "1", "-i", imgName, "-t", imgDuration, "-pix_fmt", "yuv420p", outputName)
        cmd.Stderr = &stderr
        err := cmd.Start()
        if err != nil {
            log.Fatal(err)
        } else {
            log.Printf("Creating "+outputName)
        }
        err = cmd.Wait()
        if err != nil {
    		    log.Printf(fmt.Sprint(err) + ": " + stderr.String())
    		    log.Fatal(err)
        } else {
            log.Printf("Created "+outputName)
        }
    }
    
Note that [`defer` postpones the `wg.Done()` call](http://stackoverflow.com/questions/26927479/go-language-fatal-error-all-goroutines-are-asleep-deadlock) to when the function finishes, and `wg` is a [WaitGroup](http://golang.org/pkg/sync/#example_WaitGroup). 

    wg.Wait()
    done := make(chan bool, 1)
    go concatVideos(len(lines), videoListFilename, silentFilename, done)

This way we can wait for all the (single image) videos to be created before we concatenate them using ffmpeg.

    func concatVideos(numberOfVideos int, listFilename string, outputName string, done chan bool) {
        var stderr bytes.Buffer
        cmd := exec.Command("ffmpeg", "-f", "concat", "-i", listFilename, "-c", "copy", outputName)
        cmd.Stderr = &stderr
        err := cmd.Start()
        if err != nil {
            log.Fatal(err)
        } else {
            log.Printf("Creating " + outputName + " using " + listFilename)
        }
        err = cmd.Wait()
        if err != nil {
            log.Printf(fmt.Sprint(err) + ": " + stderr.String())
            log.Fatal(err)
        } else {
            log.Printf("Created " + outputName)
        }
        done <- true
    }
    
[The `done` channel](https://gobyexample.com/channel-synchronization) is to know when ffmpeg has finished concatenating the files, so that we can add audio (again using ffmpeg).

    <-done
    go addAudio(silentFilename, *audioFilenamePtr, *outputFilenamePtr, done)
    
Note that we can [delete the single image videos](http://golang.org/pkg/os/#Remove) before waiting for the addAudio goroutine to pass a `true` value on the `done` channel.

    for i, line := range lines {
        if line != "" {
            outputFilename := "out" + strconv.Itoa(i+1) + ".mp4"
            err = os.Remove(outputFilename)
            if err != nil {
                log.Fatal(err)
            } else {
                log.Printf("Deleted " + outputFilename)
            }
        }
    }
    <-done
    
 Finally, [converting an int to string requires `strconv.Itoa()`](http://stackoverflow.com/questions/10105935/how-to-convert-a-int-value-to-string-in-go).