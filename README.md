[![Kwalitee status](https://cpants.cpanauthors.org/dist/CPANfile-Parse-PPI.png)](https://cpants.cpanauthors.org/dist/CPANfile-Parse-PPI)
[![GitHub issues](https://img.shields.io/github/issues/perlservices/CPANfile-Parse-PPI.svg)](https://github.com/perlservices/CPANfile-Parse-PPI/issues)
[![CPAN Cover Status](https://cpancoverbadge.perl-services.de/CPANfile-Parse-PPI-0.06)](https://cpancoverbadge.perl-services.de/CPANfile-Parse-PPI-0.06)
[![Cpan license](https://img.shields.io/cpan/l/CPANfile-Parse-PPI.svg)](https://metacpan.org/release/CPANfile-Parse-PPI)

# NAME

CPANfile::Parse::PPI - Parse _cpanfile_s with PPI

# VERSION

version 0.06

# SYNOPSIS

```perl
use v5.24;
use CPANfile::Parse::PPI;

my $path     = '/path/to/cpanfile';
my $cpanfile = CPANfile::Parse::PPI->new( $path );

# or
# my $cpanfile = CPANfile::Parse::PPI->new( \$content );

for my $module ( $cpanfile->modules->@* ) {
    my $stage   = $module->{stage}   ? " on $module->{stage}"            : '';
    my $feature = $module->{feature} ? " for feature $module->{feature}" : '';

    say sprintf "%s is %s",
         $module->{name}, $module->{type}, $stage, $feature;
}
```

# METHODS

## new

```perl
my $path     = '/path/to/cpanfile';
my $cpanfile = CPANfile::Parse::PPI->new( $path );

# or
my $content  = <<'CPANFILE';
requires "CPANfile::Parse::PPI" => 3.6;';
on build => sub {
    recommends "Dist::Zilla" => 4.0;
    requires "Test2" => 2.311;
}
feature 'sqlite', "SQLite Support" => sub {
    requires DBD::SQLite
}
CPANFILE

my $cpanfile = CPANfile::Parse::PPI->new( \$content );
```

# ATTRIBUTES

## meta

Returns information about mirrors and OS name - if given in the cpanfile

## modules

Returns a list of modules mentioned in the cpanfile ("perl" is skipped).
Each element is a hashref with these keys:

- name
- version
- type
- stage
- feature

```perl
use CPANfile::Parse::PPI;
use Data::Printer;

my $required = 'requires "CPANfile::Parse::PPI" => 3.6;';
my $cpanfile = CPANfile::Parse::PPI->new( \$required );

my $modules = $cpanfile->modules;
p $modules;

__DATA__
[
    [0] {
        name      "CPANfile::Parse::PPI",
        stage     "",
        type      "requires",
        version   3.6
    }
]
```

# LIMITATIONS

As this is a static parser, this module cannot handle dynamic
code like

```perl
for my $module (qw/
    IO::All
    Zydeco::Lite::App
/) {
    requires $module, '0';
}
```

This module warns when the required "module" doesn't look like
a package name.

You can make it die when you pass `-strict` to the module
when you load it:

```perl
use CPANfile::Parse::PPI -strict;
use Data::Printer;

my $required = do { local $/; <DATA> };
my $cpanfile = CPANfile::Parse::PPI->new( \$required );

my $modules = $cpanfile->modules;

__DATA__
for my $module (qw/
    IO::All
    Zydeco::Lite::App
/) {
    requires $module, '0';
}
```



# Development

The distribution is contained in a Git repository, so simply clone the
repository

```
$ git clone git://github.com/perlservices/CPANfile-Parse-PPI.git
```

and change into the newly-created directory.

```
$ cd CPANfile-Parse-PPI
```

The project uses [`Dist::Zilla`](https://metacpan.org/pod/Dist::Zilla) to
build the distribution, hence this will need to be installed before
continuing:

```
$ cpanm Dist::Zilla
```

To install the required prequisite packages, run the following set of
commands:

```
$ dzil authordeps --missing | cpanm
$ dzil listdeps --author --missing | cpanm
```

The distribution can be tested like so:

```
$ dzil test
```

To run the full set of tests (including author and release-process tests),
add the `--author` and `--release` options:

```
$ dzil test --author --release
```

# AUTHOR

Renee Baecker <reneeb@cpan.org>

# COPYRIGHT AND LICENSE

This software is Copyright (c) 2020 by Renee Baecker.

This is free software, licensed under:

```
The Artistic License 2.0 (GPL Compatible)
```
