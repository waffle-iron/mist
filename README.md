[![Stories in Ready](https://badge.waffle.io/expanse-org/mist.png?label=ready&title=Ready)](https://waffle.io/expanse-org/mist?utm_source=badge)
# Mist Browser

[![Join the chat at https://gitter.im/expanse-org/mist](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/expanse-org/mist?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![Build Status develop branch](https://travis-ci.org/expanse-org/mist.svg?branch=develop)](https://travis-ci.org/expanse-org/mist)
[![Code Climate](https://codeclimate.com/github/expanse-org/mist/badges/gpa.svg)](https://codeclimate.com/github/expanse-org/mist)

The Mist browser is the tool of choice to browse and use Ðapps.

For the Mist API see the [MISTAPI.md](MISTAPI.md).

## Installation

If you want install the app from a pre-built version on the [release page](https://github.com/expanse-org/mist/releases),
you can simply run the executable after download.

For updating simply download the new version and copy it over the old one (keep a backup of the old one if you want to be sure).

#### Config folder
The data folder for Mist is stored in other places:

- Windows `%APPDATA%\Mist`
- macOS `~/Library/Application Support/Mist`
- Linux `~/.config/Mist`


## Development

For development, a Meteor server will to be started to assist with live reload and CSS injection.
Once a Mist version is released the Meteor frontend part is bundled using `meteor-build-client` npm package to create pure static files.

### Dependencies

To run mist in development you need:

- [Node.js](https://nodejs.org) `v6.x` (use the prefered installation method for your OS)
- [Meteor](https://www.meteor.com/install) javascript app framework
- [Yarn](https://yarnpkg.com/) package manager
- [Electron](http://electron.atom.io/) `v1.3.13` cross platform desktop app framework
- [Gulp](http://gulpjs.com/) build and automation system

Install the later ones via:

    $ curl https://install.meteor.com/ | sh
    $ curl -o- -L https://yarnpkg.com/install.sh | bash
    $ yarn global add electron@1.3.13
    $ yarn global add gulp

### Initialisation

Now you're ready to initialize Mist for development:

    $ git clone https://github.com/expanse-org/mist.git
    $ cd mist
    $ yarn

To update Mist in the future, run:

    $ cd mist
    $ git pull
    $ yarn

### Run Mist

For development we start the interface with a Meteor server for auto reload etc.
*Start the interface in a separate terminal window:*

    $ cd mist/interface && meteor --no-release-check

In the original window you can then start Mist with:

    $ cd mist
    $ electron .

*NOTE: client-binaries (e.g. [geth](https://github.com/expanse-org/go-expanse)) specified in [clientBinaries.json](https://github.com/expanse-org/mist/blob/master/clientBinaries.json) will be checked during every startup and downloaded if out-of-date, binaries are stored in the [config folder](#config-folder)*

*NOTE: use `--help` to display available options, e.g. `--loglevel debug` (or `trace`) for verbose output*

### Run the Wallet

Start the wallet app for development, *in a separate terminal window:*

    $ cd mist/interface && meteor --no-release-check

    // and in another terminal

    $ cd my/path/meteor-dapp-wallet/app && meteor --port 3050

In the original window you can then start Mist using wallet mode:

    $ cd mist
    $ electron . --mode wallet


### Connecting to node via HTTP instead of IPC

This is useful if you have a node running on another machine, though note that
it's less secure than using the default IPC method.

```bash
$ electron . --rpc http://localhost:8545
```


### Passing options to Gexp

You can pass command-line options directly to Gexp by prefixing them with `--node-` in
the command-line invocation:

```bash
$ electron . --mode mist --node-rpcport 19343 --node-networkid 2
```

The `--rpc` Mist option is a special case. If you set this to an IPC socket file
path then the `--ipcpath` option automatically gets set, i.e.:

```bash
$ electron . --rpc /my/gexp.ipc
```

...is the same as doing...


```bash
$ electron . --rpc /my/gexp.ipc --node-ipcpath /my/gexp.ipc
```

### Using Mist with a privatenet

To run a private network you will need to set the IPC path, network id and data
folder:

```bash
$ electron . --rpc ~/Library/Expanse/gexp.ipc --node-networkid 1234  --node-datadir ~/Library/Expanse/privatenet
```

_NOTE: since `ipcpath` is also a Mist option you do not need to also include a
`--node-ipcpath` option._

You can also run `gexp` separately yourself with the same options prior to start
Mist normally.


### Deployment


To create a binaries you need to install [`electron-builder` dependencies](https://github.com/electron-userland/electron-builder/wiki/Multi-Platform-Build#macos):

    // tools for the windows binaries
    $ brew install wine --without-x11 mono makensis
    // tools for the Linux binaries
    $ brew install gnu-tar libicns graphicsmagick xz
    // general dependencies
    $ npm install -g meteor-build-client

To generate the binaries simply run:

    $ cd mist
    $ gulp

    // Or to generate the wallet (using the https://github.com/expanse-org/meteor-dapp-wallet -> master)
    $ gulp wallet

This will generate the binaries inside the `dist_mist/release` or `dist_wallet/release` folder.

#### Options

##### platform

Additional you can only build the windows, linux, mac or all binary by using the `platform` option:

    $ gulp update-nodes --platform mac

    // And
    $ gulp mist --platform mac

    // Or
    $ gulp mist --platform mac,win


Options are:

- `mac` (Mac OSX)
- `win` (Windows)
- `linux` (Linux)
- `all` (default)


##### walletSource

With the `walletSource` you can specify the branch to use, default ist `master`:

    $ gulp mist --walletSource develop


Options are:

- `master`
- `develop`
- `local` Will try to build the wallet from [mist/]../meteor-dapp-wallet/app

##### mist-checksums | wallet-checksums


Spits out the SHA256 checksums of distributables.

It expects installer/zip files to be in the generated folders e.g. `dist_mist/release`

    $ gulp mist-checksums

    3f726fff186b85c600ea2459413d0bf5ada2dbc98877764efbefa545f96eb975  ./dist_mist/release/Mist-0.8.1-ia32.exe
    ab4d26d5ebc66e9aba0fa610071266bacbb83faacbb7ed0dd2acb24386190bdb  ./dist_mist/release/Mist-0.8.1.exe
    909b0fb4c7b09b731b2a442c457747e04ffdd9c03b6edc06079ae05a46200d13  ./dist_mist/release/Mist-0.8.1-ia32.deb
    e114d6188963dfdae0489abf4e8923da58b39ff9cdbaad26e803af27c7ce55d1  ./dist_mist/release/Mist-0.8.1.deb
    930787dd2f5ed6931068bff9244bccc01f397f552c48ded0f08e515e276dd080  ./dist_mist/release/Mist-0.8.1.dmg

### Code signing for production

**As of [#972](https://github.com/expanse-org/mist/pull/972) we've updated the build process and thus need to redo code-signing.**


## Testing

First make sure to build Mist with:
`gulp mist --platform [mac,linux]` or `gulp wallet --platform [mac,linux]`.

Then run `gulp test-mist` or `gulp test-wallet`, accordingly.
