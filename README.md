# multi-signer-controler
Control of a DNSSEC multi-signer group

## Notes

- Pick-up time for CDS/CDNSKEY on .ch may be up to one day / 24 hours

# Configuration

This is a list of current things that can be configured with `conf-set`:
- `tsigkey-<name> <value>`: Add a TSIG key with the <name> and it's secret <value>
- `desectoken-<name> <value>`: Add a deSEC.io token with the <name> and it's secret <value>
- `signer-csk:<name> yes`: Indicate that the signer <name> is using a CSK
- `signer-type:<name> <type>`: Set which <type> of signer <name> is, valid types are: nsupdate, desec
- `signer-tsigkey:<name> <keyname>`: Set which TSIG key <keyname> that signer <name> should use
- `signer-desec:<name> <tokenname>`: Set which deSEC token <tokenname> that signer <name> should use

# Runtime

*multi-signer-controler* requires `-conf` to be specified at runtime, you can
see all runtime options by using `-help`.

## Running a daemon

*multi-signer-controler* can be run as a daemon with `daemon` command, once
started another *multi-signer-controler* can communicate with that daemon
by using `-remote`.

# Commands

All commands help and required parameters can be view using the `help`
command (without dash).

# Example: How to run

First we set a short-cut for the base of the command for this example:

```
CMD="./multi-signer-controler -conf example.conf"
```

Now we configure the TSIG and deSEC.io secrets we will use for each signer:

```
$CMD conf-set tsigkey-tsig asecret==
$CMD conf-set desectoken-desec anothersecret
```

Then we create a new signer group:

```
$CMD group-add example.com.
```

Add the first signer and set it to use TSIG:

```
$CMD signer-add example.com. A 127.0.0.2
$CMD conf-set signer-tsigkey:A tsig
```

Add the second signer and set it to use deSEC.io as a CSK:

```
$CMD signer-add example.com. B 127.0.0.3
$CMD conf-set signer-desec:B desec
$CMD conf-set signer-csk:B yes
```

Now we can check the status of the signer group:

```
$CMD status example.com.
```

And sync the DNSKEYs between the signers (Note: this step is still WIP):

```
$CMD sync-dnskey example.com.
```

# local go

```
mkdir -p go/1.15.8; wget -O - https://storage.googleapis.com/golang/go1.15.8.linux-amd64.tar.gz | tar -C go/1.15.8 -zxv
export GOROOT="$HOME/go/1.15.8/go" GOPATH="$HOME/go"
export PATH="$PATH:$GOROOT"
```

# build

```
make
./multi-signer-controler
```

# Known Issues

- TSIG keys hardcoded to HMAC-SHA256
