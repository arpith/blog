---
layout: post
date: 2016-07-09 17:50:00 ET
title: "Adventures with mmap"
---

# Adventures with mmap

This week I started at the Recurse Center, a self directed program where everyone is working at becoming a better programmer. If you’ve been considering it, you should definitely do it! It’s even more awesome than you’ve heard!

The first project I’m working on is a [distributed in-memory datastore](https://github.com/arpith/mmapd). But it’s primarily an excuse to play around with stuff I’ve been reading about and haven’t gotten around to! This is the story of my adventure with [mmap](http://man7.org/linux/man-pages/man2/mmap.2.html).

![](https://cdn-images-1.medium.com/max/7712/1*eeo1O5nx6gNaS0ylmIY-Cw.jpeg)

The [mmap function](https://golang.org/pkg/syscall/#Mmap) in [Go](https://golang.org/)’s [syscall](https://golang.org/pkg/syscall/) package returns a [byte](https://golang.org/pkg/builtin/#byte) [slice](https://blog.golang.org/go-slices-usage-and-internals) with the contents of the file you’re interested in. I passed it a file descriptor, the size of the mapping I wanted (this turned out to be the [capacity](https://tour.golang.org/moretypes/11) of the returned slice), the memory protection needed and the type of mapping.

[It looks like](http://stackoverflow.com/questions/9519648/mmap-fun-map-shared-vs-map-private) the memory protection needs to match the flags passed when you open the file (to obtain the file descriptor).

    func (db *db) mmap(size int) {
        fmt.Println("mmapping: ", size)
        data, err := syscall.Mmap(db.fd, 0, size, syscall.PROT_WRITE|syscall.PROT_READ, syscall.MAP_SHARED)
        if err != nil {
            fmt.Println("Error mmapping: ", err)
        }
        db.data = data
    }

MAP_SHARED is what makes this fun — changes you make to the byte array are carried through to the file. My understanding is that this happens “eventually”, which is why [msync](http://man7.org/linux/man-pages/man2/msync.2.html) exists. From this [code](https://github.com/torvalds/linux/blob/9256d5a308c95a50c6e85d682492ae1f86a70f9b/mm/msync.c) it looks like it calls fput to do the writing.

The code I’m using to resize my database file looks like this:

    func (db *db) resize(size int) {
        fmt.Println("Resizing: ", size)
        err := syscall.Ftruncate(db.fd, int64(size))
        if err != nil {
            fmt.Println("Error resizing: ", err)
        }
    }

    func (db *db) open() {
        fmt.Println("Getting file descriptor")
        f, err := os.OpenFile(db.filename, os.O_CREATE|os.O_RDWR, 0)
        if err != nil {
            fmt.Println("Could not open file: ", err)
        }
        db.fd = int(f.Fd())
        db.file = f
    }

    func (db *db) extend(size int) {
        db.file.Close()
        db.open()
        db.resize(size)
        db.mmap(size)
    }

I’m using [ftruncate](http://linux.die.net/man/2/ftruncate) to extend the file before mmaping it again. Doing a bunch of ftruncates resulted in “invalid argument” errors after the first hundred and fifty or so (I hadn’t been doubling the size).

[On OS X](https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man2/ftruncate.2.html), this is either because the file descriptor references a socket (and not a file) or the file descriptor is not open for writing. So I’m currently opening the file each time I need to extend it, to get a fresh file descriptor. I’m also closing using the old descriptor because I wound up having too many files open.

Things I want to know:

1. Is there a better way to do this!

1. When does the kernel write the changes to disk?

Things I want to do:

1. Reduce the number of times I resize the database file by doubling the size.

1. [Use](https://github.com/edsrzf/mmap-go/blob/935e0e8a636ca4ba70b713f3e38a19e1b77739e8/mmap_unix.go) [msync](http://man7.org/linux/man-pages/man2/msync.2.html) in case the server dies before the kernel gets around to writing the changes to disk.

Some bonus fun stuff:

1. [glibc](https://www.gnu.org/software/libc/)’s [malloc](http://man7.org/linux/man-pages/man3/malloc.3.html) [uses mmap](https://sploitfun.wordpress.com/2015/02/10/understanding-glibc-malloc/)!

1. [LevelDB](https://github.com/google/leveldb) [used to use mmap](https://groups.google.com/forum/#!topic/leveldb/C5Hh__JfdrQ), [until](https://groups.google.com/forum/#!searchin/leveldb/stdio$20write/leveldb/VM5nYLvLVME/Ri8MThbM4I0J) this [broke bitcoin on OS X](http://hackingdistributed.com/2013/11/27/bitcoin-leveldb/).

Special thanks to [James Porter](https://twitter.com/porterjamesj), [Lisa Neigut](https://twitter.com/niftynei) and [Irina Gossman](https://github.com/margold)!
