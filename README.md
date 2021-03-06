# `envcrypt`

`envcrypt` runs commands in a special process environment loaded from encrypted files.

```
$ envcrypt -h
usage: envcrypt PATH COMMAND [ARGS...]

Set environment variables defined in encrypted file PATH and run COMMAND.

Arguments:
  PATH     path to a gpg-encrypted file that can be read with eg `gpg -d PATH`
  COMMAND  command to be invoked in the context of the environment defined in PATH
```

The contents of `PATH` should be zero or more lines of string keys and values, separated by `=` and encrypted with GPG:

```
$ echo AWS_ACCESS_KEY_ID=XXXXXXX | gpg -a -e -o aws.asc
$ envcrypt aws.asc /bin/sh -c 'echo $AWS_ACCESS_KEY_ID'
XXXXXXXX
```

## Prerequisites

`envcrypt` runs `gpg` in batch mode, so you must be running `gpg-agent` for your private key to be accessible.

### OSX

Make sure your `gpg` and `gpg-agent` executables are version 2 or greater:

```
$ gpg --version | head -1
gpg (GnuPG/MacGPG2) 2.0.28
$ gpg-agent --version | head -1
gpg-agent (GnuPG) 2.0.29
```

If you don't have GPG version 2, install [GPG Tools](https://gpgtools.org/). GPG Tools installs into the `/usr/local/MacGPG2/bin` directory, so add that to your shell's `$PATH` variable. Then, make sure you have a `gpg-agent` running in daemon mode:

```
$ gpg-agent --daemon
```

Finally, configure `gpg` to use the agent:

```
$ echo use-agent >> ~/.gnupg/gpg.conf
```

## Install

This is the easy way; do this unless you definitely need to [build from source](#source). Supported platforms:

- [OSX](https://github.com/whilp/envcrypt/releases/download/v0.3/envcrypt-Darwin-x86_64)

```console
$ curl -sL -O https://github.com/whilp/envcrypt/releases/download/v0.3/envcrypt-Darwin-x86_64
$ curl -sL -O https://github.com/whilp/envcrypt/releases/download/v0.3/envcrypt-Darwin-x86_64.sha256
$ shasum -c envcrypt-Darwin-x86_64.sha256
envcrypt-Darwin-x86_64: OK
$ mv envcrypt-Darwin-x86_64 ~/bin/envcrypt
$ chmod a+x ~/bin/envcrypt
```

Make sure `~/bin` is a directory in your `$PATH`; replace it with another directory if you'd prefer.

## Install from source <a id="source" />

You can also install directly from source (but you probably just want to [install the binary](#install)):

```
go get github.com/whilp/envcrypt
```

## Test

```
make test
```

## License

BSD; see `LICENSE`.
