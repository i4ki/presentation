%title: nash - Namespace aware shell
%author: Tiago Natel de Moura
%date: 2016-06-18

-> Agenda <-
=========

* What's Nash?
* Motivation
* SysAdmin
* DevOps
* Containers
* Plan9
* Namespaces
* rc
* bash
* Nash

***

-> What's Nash? <-
===========

Nash is a shell created for modern operating systems.

* Small (< 7k loc)
* Safe
* Modern

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

-> Motivation <-
=============

Created to solve the following problems:

* Low level namespace manipulation;
* Sane shell for acceptance/stress testing;

***
