<p align="center">
  <img src="data/logo.svg" alt="miniserve - a CLI tool to serve files and dirs over HTTP"><br>
</p>

# miniserve - a CLI tool to serve files and dirs over HTTP

[![CI](https://github.com/svenstaro/miniserve/workflows/CI/badge.svg)](https://github.com/svenstaro/miniserve/actions)
[![Docker Cloud Build Status](https://img.shields.io/docker/cloud/build/svenstaro/miniserve)](https://cloud.docker.com/repository/docker/svenstaro/miniserve/)
[![AUR](https://img.shields.io/aur/version/miniserve.svg)](https://aur.archlinux.org/packages/miniserve/)
[![Crates.io](https://img.shields.io/crates/v/miniserve.svg)](https://crates.io/crates/miniserve)
[![license](http://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/svenstaro/miniserve/blob/master/LICENSE)
[![Stars](https://img.shields.io/github/stars/svenstaro/miniserve.svg)](https://github.com/svenstaro/miniserve/stargazers)
[![Downloads](https://img.shields.io/github/downloads/svenstaro/miniserve/total.svg)](https://github.com/svenstaro/miniserve/releases)
[![Lines of Code](https://tokei.rs/b1/github/svenstaro/miniserve)](https://github.com/svenstaro/miniserve)

**For when you really just want to serve some files over HTTP right now!**

**miniserve** is a small, self-contained cross-platform CLI tool that allows you to just grab the binary and serve some file(s) via HTTP.
Sometimes this is just a more practical and quick way than doing things properly.

## Screenshot

![Screenshot](screenshot.png)

## How to use

### Serve a directory:

    miniserve linux-distro-collection/

### Serve a single file:

    miniserve linux-distro.iso

### Require username/password:

    miniserve --auth joe:123 unreleased-linux-distros/

### Generate random 6-hexdigit URL:

    miniserve -i 192.168.0.1 --random-route /tmp
    # Serving path /private/tmp at http://192.168.0.1/c789b6

### Bind to multiple interfaces:

    miniserve -i 192.168.0.1 -i 10.13.37.10 -i ::1 /tmp/myshare

## Features

- Easy to use
- Just works: Correct MIME types handling out of the box
- Single binary drop-in with no extra dependencies required
- Authentication support with username and password (and hashed password)
- Mega fast and highly parallel (thanks to [Rust](https://www.rust-lang.org/) and [Actix](https://actix.rs/))
- Folder download (compressed on the fly as `.tar.gz` or `.zip`)
- File uploading
- Pretty themes
- Scan QR code for quick access

## Usage

    miniserve 0.9.1-alpha.0
    Sven-Hendrik Haase <svenstaro@gmail.com>, Boastful Squirrel <boastful.squirrel@gmail.com>
    For when you really just want to serve some files over HTTP right now!

    USAGE:
        miniserve [FLAGS] [OPTIONS] [--] [PATH]

    FLAGS:
        -r, --enable-tar
                Enable tar archive generation

        -z, --enable-zip
                Enable zip archive generation

                WARNING: Zipping large directories can result in out-of-memory exception because zip generation is done in
                memory and cannot be sent on the fly
        -u, --upload-files
                Enable file uploading

        -h, --help
                Prints help information

        -P, --no-symlinks
                Do not follow symbolic links

        -o, --overwrite-files
                Enable overriding existing files during file upload

        -q, --qrcode
                Enable QR code display

            --random-route
                Generate a random 6-hexdigit route

        -V, --version
                Prints version information

        -v, --verbose
                Be verbose, includes emitting access logs


    OPTIONS:
        -a, --auth <auth>...
                Set authentication. Currently supported formats: username:password, username:sha256:hash,
                username:sha512:hash (e.g. joe:123,
                joe:sha256:a665a45920422f9d417e4867efdc4fb8a04a1f3fff1fa07e998e86f7f7a27ae3)
        -c, --color-scheme <color-scheme>
                Default color scheme [default: squirrel]  [possible values: squirrel, archlinux, zenburn, monokai]

        -d, --color-scheme-dark <color-scheme-dark>
                Default color scheme [default: archlinux]  [possible values: squirrel, archlinux, zenburn, monokai]

            --index <index_file>
                The name of a directory index file to serve, like "index.html"

                Normally, when miniserve serves a directory, it creates a listing for that directory. However, if a
                directory contains this file, miniserve will serve that file instead.
        -i, --interfaces <interfaces>...
                Interface to listen on

        -p, --port <port>
                Port to use [default: 8080]

        -t, --title <title>
                Shown instead of host in page title and heading


    ARGS:
        <PATH>
                Which path to serve

## How to install

<a href="https://repology.org/project/miniserve/versions"><img align="right" src="https://repology.org/badge/vertical-allrepos/miniserve.svg" alt="Packaging status"></a>

**On Linux**: Download `miniserve-linux` from [the releases page](https://github.com/svenstaro/miniserve/releases) and run

    chmod +x miniserve-linux
    ./miniserve-linux

**On OSX**: Download `miniserve-osx` from [the releases page](https://github.com/svenstaro/miniserve/releases) and run

    chmod +x miniserve-osx
    ./miniserve-osx

Alternatively install with [Homebrew](https://brew.sh/).

    brew install miniserve
    miniserve

**On Windows**: Download `miniserve-win.exe` from [the releases page](https://github.com/svenstaro/miniserve/releases) and run

    miniserve-win.exe

**With Cargo**: You will need the _nightly_ version of Rust to compile the project. Then you can run

    cargo install miniserve
    miniserve

**With Docker:** If you prefer using Docker for this, run

    docker run -v /tmp:/tmp -p 8080:8080 --rm -it svenstaro/miniserve /tmp

## Binding behavior

For convenience reasons, miniserve will try to bind on all interfaces by default (if no `-i` is provided).
It will also do that if explicitly provided with `-i 0.0.0.0` or `-i ::`.
In all of the aforementioned cases, it will bind on both IPv4 and IPv6.
If provided with an explicit non-default interface, it will ONLY bind to that interface.
You can provide `-i` multiple times to bind to multiple interfaces at the same time.

## Why use this over alternatives?

- darkhttpd: Not easily available on Windows and it's not as easy as download and go.
- Python built-in webserver: Need to have Python installed, it's low performance, and also doesn't do correct MIME type handling in some cases.
- netcat: Not as convenient to use and sending directories is [somewhat involved](https://nakkaya.com/2009/04/15/using-netcat-for-file-transfers/).

## Releasing

This is mostly a note for me on how to release this thing:

- `cargo release --dry-run`
- `cargo release`
- Releases will automatically be deployed by Github Actions.
- Docker images will automatically be built by Docker Hub.
- Update AUR package.
