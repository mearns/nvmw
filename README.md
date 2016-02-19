# nvmw

**`nvmw` - the nvm wrapper.**

Ensure that a specified version of [`nvm`](https://github.com/creationix/nvm)
is installed, and then use it to
ensure a specific version of [`nodejs`](https://nodejs.org/) is installed.
Then execute the given
arguments with the desired node version selected (i.e., following an `nvm
use`).

Specify the node version to install in a `.nvmrc` file, as usual with `nvm`, or
alternatively with the `NODE_VERSION` environment variable (which supercedes
any `.nvmrc` file).

You can also specify which version of `nvm` to use, with the `NVM_VERSION` env
var. Otherwise, a default value is used (hard-coded below). This should be a specific
version tagged version, excluding the leading 'v'.

For status messages, set the `NVMW_DEBUG` variable to "1".

Everything is stored in the `.nvmw` directory in the same directory as this
script is located (NOT necessarily the working directory). Versions of the
`nvm.sh` script are stored under there, and, when invoked through the nvm-wrapper,
is configured to use `.nvmw/nvm` as the NVM directory; all versions of
`node` and related tools (e.g., `npm`), plus all "globally" installed npm modules
are stored there (assuming you use the nvm-wrapper to install them, like
`./nvmw npm install MODULE_NAME -g`). If you want, you can change the location
where nvmw stores things by setting the `NVMW_DIR` env var. For instance, you
could set it to a system wide location so that nvmw and nvm cache everything
in one location.

## Installation

Just drop `nvmw` into your project's root directory. That's it. Oh, and you need either
curl or wget installed. Other than that, it should work in pretty much any bash shell
(possibly others).

E.g.:

```bash
wget https://raw.githubusercontent.com/mearns/nvmw/v1.0.0/nvmw
```

## Demo

```bash
$ pwd
/tmp
$
$ # Make sure no previous nvmw things are in the way,
$ # to get consistent output.
$ rm -rf .nvmw
$ rm -f .nvmrc
$
$ # Specify which version of node we want to use, in the .nvmrc
$ echo "4.3.1" > .nvmrc
$
$ # Invoke node, using the wrapper.
$ # Neither nvm nor node is installed here yet, so they will both be installed first.
$ ./nvmw node --version
######################################################################## 100.0%
Found '/tmp/.nvmrc' with version <4.3.1>
Downloading https://nodejs.org/dist/v4.3.1/node-v4.3.1-linux-x64.tar.xz...
######################################################################## 100.0%
Now using node v4.3.1 (npm v2.14.12)
Creating default alias: default -> 4.3.1 (-> v4.3.1)
v4.3.1
$
$ # The version is installed now, it'll be quicker this time.
$ ./nvmw node --version
v4.3.1
$
$ # Override the version with the NODE_VERSION env var.
$ # The new version of node will be installed, but nvm is already installed.
$ NODE_VERSION=5.6.0 ./nvmw node --version
######################################################################## 100.0%
Now using node v5.6.0 (npm v3.6.0)
v5.6.0
$
$ # Back to using nvmrc
$ ./nvmw node --version
v4.3.1
$
$ # And back to overridden version
$ NODE_VERSION=5.6.0 ./nvmw node --version
v5.6.0
$
$ # Install npm modules.
$ sudo ./nvmw npm install pm2 -g
npm WARN optional dep failed, continuing fsevents@1.0.7
/tmp/.nvmw/nvm/versions/node/v4.3.1/bin/pm2 -> /tmp/.nvmw/nvm/versions/node/v4.3.1/lib/node_modules/pm2/bin/pm2
/tmp/.nvmw/nvm/versions/node/v4.3.1/bin/pm2-dev -> /tmp/.nvmw/nvm/versions/node/v4.3.1/lib/node_modules/pm2/bin/pm2-dev
pm2@1.0.1 /tmp/.nvmw/nvm/versions/node/v4.3.1/lib/node_modules/pm2
├── ikt@0.0.0
├── eventemitter2@0.4.14
├── async@1.5.2
├── pidusage@1.0.1
├── semver@5.1.0
├── commander@2.9.0 (graceful-readlink@1.0.1)
├── coffee-script@1.10.0
├── debug@2.2.0 (ms@0.7.1)
├── pm2-deploy@0.2.1 (async@1.4.2, tv4@1.0.18)
├── pm2-multimeter@0.1.2 (charm@0.1.2)
├── vizion@0.2.12 (async@0.9.0)
├── cli-table@0.3.1 (colors@1.0.3)
├── pm2-axon@2.0.9 (amp-message@0.1.2, escape-regexp@0.0.1, amp@0.3.1, configurable@0.0.1)
├── chalk@1.1.1 (escape-string-regexp@1.0.4, supports-color@2.0.0, ansi-styles@2.1.0, strip-ansi@3.0.0, has-ansi@2.0.0)
├── shelljs@0.6.0
├── pmx@0.5.9 (json-stringify-safe@5.0.1)
├── mkdirp@0.5.1 (minimist@0.0.8)
├── pm2-axon-rpc@0.3.6 (json-stringify-safe@5.0.1, commander@1.0.5)
├── nssocket@0.6.0 (lazy@1.0.11)
├── cron@1.1.0 (moment-timezone@0.3.1)
├── moment@2.11.2
└── chokidar@1.4.2 (path-is-absolute@1.0.0, inherits@2.0.1, async-each@0.1.6, glob-parent@2.0.0, is-binary-path@1.0.1, is-glob@2.0.1, readdirp@2.0.0, anymatch@1.3.0)
$
$ # Now pm2 is installed, but only for the local version 4.3.0 of node.
$ ./nvmw pm2 --version
1.0.1
$
$ # It isn't installed for other versions of node.
$ NODE_VERSION=5.6.0 ./nvmw pm2 --version
./nvmw: line 198: pm2: command not found
$
$ ./nvmw which pm2
/tmp/.nvmw/nvm/versions/node/v4.3.1/bin/pm2
```

## Limits

You shouldn't generally use the nvm wrapper to execute nvm. It may work in
particular cases, but, in particular, if you're overriding the node version
with the `NODE_VERSION` env var, nvm itself does not consider this.

