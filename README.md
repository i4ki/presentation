%title: nash - Namespace aware shell
%author: Tiago Natel de Moura
%date: 2016-06-18

-> Agenda <-
=========

* What's Nash?
* Motivation
* SysAdmin / DevOps
* Containers
* Plan9
* Namespaces
* rc
* Nash

***

-> What's Nash? <-
===========

Nash is a shell created for modern operating systems.

* Small (< 7k loc)
* Safe
* Modern

***

-> Motivation <-
=============

Created to solve the following problems:

* Low level namespace manipulation;
* Sane shell for acceptance/stress testing;
* Clean unit tests involving network file systems
  - Mount namespace

***

-> Motivation <-
=============

Created to solve the following problems:

* Low level namespace manipulation;
* Sane shell for acceptance/stress testing;
* Clean unit tests involving network file systems
  - Mount namespace
* *Personal interest*

***

-> SysAdmin vs DevOps <-

***

-> Containers <-

A bunch of technologies.

* Namespaces
* Union filesystem
* Cgroups
* Sandboxing

***

-> The shift <-

* Automated deploys are not new stuff;
* Deb / rpg / deb / etc

***

-> The shift <-

* But why this trend now?

***

-> The shift <-

* But why this trend now?

*Linux namespaces*
*Linux cgroups*
*Linux overlayfs/unionfs/ufs*

***

-> The origin <-

*Plan9 from Bell Labs* - 1995

Plan 9 from Bell Labs is a distributed operating system, originally
developed by the Computing Sciences Research Center at Bell Labs
between the mid-1980s and 2002. It takes some of the principles of
Unix, developed in the same research group, but extends these to a
networked environment with graphics terminals.

***

-> Plan9 <-

What Unix Problems Were Too Deep to Fix?
This is best described by Dave Presotto's 9fans post (from 7 May 2003):

Before Plan 9, we were running lots of Unices held together by a few
networks, a remote file system (different uid's on each Unix), and a
bunch of remote execution commands. We hated it since it was much
harder to manage and use than our old single multiuser machine. We
wanted an environment that not only put together a lot of boxes and
made them look like one but which also would make use of the new
technologies that were appearing (SMP's, heterogeneous architectures,
juke boxes, ...).

***

-> The solution <-

* Single file system protocol (file abstraction)
* Namespace
* Union fs

***

-> rc <-

Plan9 default shell

* Very simple shell inspired by sh (borne shell)
* rfork keyword for namespace manipulation
* Uses some plan9 binaries for namespace setup (bind, mount, etc)

***

-> rc <-

Creating a network namespace.

~~~ {.numberLines}
rfork N; # Create an empty namespace
bind #i /net
~~~~~~~~~~~~~~~~~~

***

-> rc <-

Using a gateway

~~~ {.numberLines}
rfork n; # Copy the parent namespace
mount -a tcp!192.168.10.55!6666 /net
~~~~~~~~~~~~~~~~~~

***

Every Plan9 user runs on a new namespace.

~~~ {.numberLines}
rfork n; # Copy the parent namespace
bind #g /home/i4k
bind -a /home/i4k/bin /bin
bind -a /home/i4k/gopath/bin /bin

mount -a tcp!192.168.10.55!6666 /net
~~~~~~~~~~~~~~~~~~

***

-> Plan9 rfork <-

* Copy the parent namespace (n)
* New empty namespace (N)
* Copy the parent environment variables (e)
* New empty environment variables (E)

etc

***

-> Nash <-

A similar approach for linux, but with some limitations.

- Plan9 namespaces are managed by filesystem
- Linux namespaces are created by syscalls

***

Project: github.com/NeowayLabs/nash

Installation:

~~~ {.numberLines}
go get -v github.com/NeowayLabs/nash/cmd/nash
~~~~~~~~~~~~~~~~~~

***

-> Nash <-

Nash command execution is much like sh, bash, fish, etc

~~~ {.numberLines}
λ> ls
λ> ls | grep .go
~~~~~~~~~~~~~~~~~~

***

-> Example <-
=============

Counting the number of lines in nash repository:

~~~ {.numberLines}
fn nash_loc() {
    IFS = ("\n")
    files <= find . -type f | grep ".go$" |
             grep -v "_test.go$"

    counter = "0"

    for file in $files {
        nlines <= cat $file | wc -l | xargs echo -n
        counter <= expr $counter "+" $nlines
    }

    return $counter
}
~~~~~~~~~~~~~~~~~~

***

-> Redirecting output <-

~~~ {.numberLines}
λ> find . > file.txt
λ> service >[1] out.log >[2] err.log
λ> service > tcp://ip.of.syslog:6666
~~~~~~~~~~~~~~~~~~

***

-> Linux rfork flags <-

* New user namespace (u)
* New mount namespace (m)
* New pid namespace (p)
* New IPC namespace (i)
* New UTS namespace (s)
* New net namespace (n)

***

-> Creating namespaces <-

~~~ {.numberLines}
rfork up {
    id
}
~~~~~~~~~~~~~~~~~~

***

-> Finish <-

*Thanks*

https://github.com/NeowayLabs/nash
