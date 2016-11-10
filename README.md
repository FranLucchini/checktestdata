Checktestdata
=============

Checktestdata is a tool to verify the syntactical integrity of test cases in
programming contests like the ACM ICPC.

It allows you to specify a simple grammar for your testdata input files,
according to which the testdata is checked.  Two sample scripts
`hello.ctd` and `fltcmp.ctd` are provided for
[DOMjudge](http://www.domjudge.org/) sample problems *hello* and *fltcmp*.

## Grammar specification

[Checktestdata language specification](doc/format-spec.md)

## Installation


Requirements:

 * recent g++ (>= 4.7)
 * Libboost and the boost::regex extension
 * The GNU GMP libraries (http://gmplib.org/)
 * flexc++/bisonc++ (optional)

If you don't have `flexc++` and/or `bisonc++` available, you may use the release
branch where we've pre-generated the scanner/parser files.

Command line for installing the build dependencies on Debian or
Ubuntu, when using the release branch:
```
apt-get install make g++ libboost-regex-dev libgmp3-dev
```
For Redhat-like distributions try:
```
yum install make g++ boost-devel gmp-devel
```

To compile checktestdata, run:
```
sudo apt get bisonc++
sudo apt get flexc++
sudo apt install automake1.11
./bootstrap
make dist
make
```

Leave out the `make dist` step if you use the pre-generated scanner/parser
files on the release branch.

## Library

The commandline program is built upon the separate library
`libchecktestdata.hpp` (see `checktestdata.cc` as an example for how to use this
library) that can be used to write the syntax checking part of special compare
scripts. It can easily handle the tedious task of verifying that a team's
submission output is syntactically valid, leaving just the task of semantic
validation to another program.

When you want to support *presentation error* as a verdict, also in variable
output problems, the option whitespace-ok can be useful. This allows any
non-empty sequence of whitespace (no newlines though) where the SPACE command
is used, as well as leading and trailing whitespace on lines (when using the
NEWLINE command). Please note that with this option enabled, whitespace
matching is greedy, so the script code

    INT(1,2) SPACE SPACE INT(1,2)

does not match `1__2` (where the two underscores represent spaces), because the
first `SPACE` command already matches both, so the second cannot match
anything.

## Run a test

The general command is

```
./checktestdata [OPTION]... PROGRAM [TESTDATA]
```
Both `PROGRAM` and `TESTDATA` must be a simple text file with no extension. Also, both files must be in the same folder as `checktestdata`.

Example with `prog1` and `test1`:

<dl>
    <dt><tt>prog1</tt></dt>
</dl>
```
INT(1,100,runs) NEWLINE
REP(runs)
  INT(1,100,c) SPACE INT(1,100,d) SPACE INT(0,500,v) NEWLINE
  REP(v)
    REGEX("(C|D)") INT(1,100) SPACE
    REGEX("(C|D)") INT(1,100) NEWLINE
  END
END
```

<dl>
    <dt><tt>test1</tt></dt>
</dl>
```
 1
1 100 3
C1 C3
D1 C100
C42 D1

```
Then the command is:
```
./checktestdata prog1 test1
```

Output should be:
```
^ 1 
1 100 3
C1 C3
D1 C100
C42 D1
 

ERROR: line 1 character 1 of testdata doesn't match INT(1,100,runs)

```
Now, if we remove the space in `test1` the output should be:
```
testdata ok!
```

## Copyright & Licencing

Checktestdata is Copyright &copy; 2008 - 2015 by the checktestdata developers and
all respective contributors. The current checktestdata developers are Jeroen
Bransen, Jaap Eldering, Jan Kuipers, and Tobias Werth; see the git commits for
a complete list of contributors.

Checktestdata, including its documentation, is free software; you can
redistribute it and/or modify it under the terms of the two-clause
BSD license. See the file [COPYING](COPYING).

The M4 autoconf macros are licenced under all-permissive and GPL3+
licences; see the respective files under m4/ for details.

## Contact

The developers can be reached through the mailinglist
`domjudge-devel@lists.a-eskwadraat.nl`. You need to be subscribed before
you can post. Information, subscription and archives are available at:
https://lists.a-eskwadraat.nl/mailman/listinfo/domjudge-devel

Some developers and users of checktestdata linger on the IRC channel
dedicated to DOMjudge on the Freenode network:
server `irc.freenode.net`, channel `#domjudge`
