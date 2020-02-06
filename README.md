# Alien::MSYS [![Build Status](https://secure.travis-ci.org/Perl5-Alien/Alien-MSYS.png)](http://travis-ci.org/Perl5-Alien/Alien-MSYS) [![Build status](https://ci.appveyor.com/api/projects/status/ubww1yhg385rjyo1/branch/master?svg=true)](https://ci.appveyor.com/project/plicease/Alien-MSYS/branch/master)

Tools required for GNU style configure scripts on Windows

# SYNOPSIS

from Perl:

```perl
use Alien::MSYS;
# runs uname from MSYS
my $uname = msys { `uname` };
```

From Prompt/Makefile

```
C:\> perl -MAlien::MSYS -e msys_run uname
```

# DESCRIPTION

MSYS provides minimal shell and POSIX tools on Windows to enable GNU style configure
scripts to run (the type usually generated by `autoconf`). This module aims to
provide an interface for using MSYS on Windows and act as a no-op on Unix like
operating systems which already have that capability.  If you use this module, I
recommend that you list this as a prerequisite only during MSWin32 installs.

When installing, this distribution will look for an existing `MSYS` using the following
methods in this order:

- environment variable `ALIEN_INSTALL_TYPE` or `ALIEN_MSYS_INSTALL_TYPE`

    If set to `share` a system install will not be attempted.  If set to `system`
    then a share install will not be attempted.

- environment variable `PERL_ALIEN_MSYS_BIN`

    If set, this environment variable should be set to the root of `MSYS` (NOT `MinGW`).
    For example, if you have `MinGW` / `MSYS` installed on `D:` you might use this:

    ```
    C:\> set PERL_ALIEN_MSYS_BIN=D:\MinGW\msys\1.0\bin
    ```

    Keep in mind that this environment variable is consulted during both install and at run-time,
    so it is advisable to set this in the System Properties control panel.

- search `PATH` for `mingw-get.exe`

    Second, [Alien::MSYS](https://metacpan.org/pod/Alien::MSYS) searches the `PATH` environment variable for the `mingw-get.exe`
    program, which is a common method for installing `MinGW` and `MSYS`.  From there
    if it can deduce the location of `MSYS` it will use that.

- try `C:\MinGW\msys\1.0\bin`

    Next, [Alien::MSYS](https://metacpan.org/pod/Alien::MSYS) tries the default install location.

- Use desktop shortcut for `MinGW Installer`

    Finally, [Alien::MSYS](https://metacpan.org/pod/Alien::MSYS) will try to find `MSYS` from the desktop shortcut created
    by the GUI installer for `MinGW`.  This method only works if you already have
    [Win32::Shortcut](https://metacpan.org/pod/Win32::Shortcut) installed, as it is an optional dependency.

If `MSYS` cannot be found using any of these methods, then it will download and install
`MSYS` in this distribution's share directory.

# FUNCTIONS

## msys

```perl
# get the uname from MSYS
my $uname = msys { `uanem` };

# run with GNU make from MSYS instead of
# dmake from Strawberry Perl
msys { system 'make' };
```

This function takes a single argument, a code reference, and runs it with the correctly
set environment so that calls to the system function or the qx quote like operator will
use MSYS instead of the default environment.

## msys\_run

```perl
# pass command through @ARGV
C:\> perl -MAlien::MSYS -e msys_run uname

# pass command through @_
C:\> perl -MAlien::MSYS -e "msys_run 'make'; msys_run 'make install'"
```

This function runs a command with the MSYS environment.  It gets the command and arguments
either as passed to it, or if none are passed the the command is expected to be in
@ARGV.

If the command fails then it will [exit](https://metacpan.org/pod/perlfunc#exit) with a non-zero error code.  This
is useful, in the second example above if either `make` or `make install` fails, then
the whole command will fail, also `make install` will not be attempted unless `make`
succeeds.

## msys\_path

This function returns the full path to the MSYS bin directory.

# CAVEATS

This [Alien](https://metacpan.org/pod/Alien) is big and slow to install.  I am aware this is an annoying problem.
It is also the only reliable way (that I know of) to install packages from source
that use autotools on Strawberry or Visual C++ Perl.  Here are some things that you
can do to avoid this painful cost:

- Use the system library if possible

    The [Alien::Build](https://metacpan.org/pod/Alien::Build) system is usually smart enough to find the system library
    if it is available.  [Alien::MSYS](https://metacpan.org/pod/Alien::MSYS) is usually only necessary for so called
    `share` installs.

- Pre-install MSYS

    As mentioned above if you preinstall MSYS and set the `PERL_ALIEN_MSYS_BIN`
    environment variable, then you will save yourself some time if you use multiple
    installs of Perl like I do.

- Use another build system

    Some projects will provide a makefile that will work with GNU Make and `cmd.exe`
    that you can build without MSYS.  An example of an Alien that takes advantage of
    this is [Alien::libuv](https://metacpan.org/pod/Alien::libuv).

    Some projects provide both autoconf and CMake.  Although using CMake reliably
    requires [Alien::cmake3](https://metacpan.org/pod/Alien::cmake3) for `share` installs, it is much much lighter than [Alien::MSYS](https://metacpan.org/pod/Alien::MSYS).

    Also obviously you can open a ticket, or make a pull request with the project that you
    are alienizing to support build systems that don't suck as much as autoconf.

- Use MSYS2

    Strawberry Perl is convenient for building XS modules without any dependencies
    or just dependencies on the small number of libraries that come bundled with
    Strawberry Perl.  It is very very painful in my opinion if you depend on libraries
    that are not bundled, which is why this Alien exists.  There is an alternative though.

    MSYS2 / MinGW provides a MSWin32 Perl as part of a Linux / open source like package
    that provides probably all of the libraries that you might want to use as dependencies,
    and if it doesn't you can build much easier than with Strawberry + Alien::MSYS.

    There are some notes here:

    [https://project-renard.github.io/doc/development/meeting-log/posts/2016/05/03/windows-build-with-msys2/](https://project-renard.github.io/doc/development/meeting-log/posts/2016/05/03/windows-build-with-msys2/)

    On using the MSYS2 / MinGW / MSWin32 Perl from the MSYS2 project.

# AUTHOR

Graham Ollis <plicease@cpan.org>

# COPYRIGHT AND LICENSE

This software is copyright (c) 2013 by Graham Ollis.

This is free software; you can redistribute it and/or modify it under
the same terms as the Perl 5 programming language system itself.
