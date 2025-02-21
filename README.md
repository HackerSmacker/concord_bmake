<div align="center">
  <br />
  <p>
    <a href="https://github.com/cogmasters/concord.git"><img src="https://raw.githubusercontent.com/Cogmasters/concord/bd1436a84af21384d93d92aed32b4c7828d0d793/docs/static/logo.svg" width="250" alt="Concord" /></a>
  </p>
  <br />
  <p>
    <br> <a href="https://discord.gg/Y7Xa6MA82v"><img src="https://img.shields.io/discord/928763123362578552?color=5865F2&logo=discord&logoColor=white" alt="Discord server" /></a> </br>
  </p>
</div>

# Concord - C Discord API library

## About

Concord is an asynchronous C99 Discord API library. It has minimal external dependencies, and a low-level translation of the Discord official documentation to C code.

### Minimal example

```c
#include <string.h>
#include <concord/discord.h>

void on_ready(struct discord *client) {
  const struct discord_user *bot = discord_get_self(client);
  log_info("Logged in as %s!", bot->username);
}

void on_message(struct discord *client, const struct discord_message *msg) {
  if (strcmp(msg->content, "ping") != 0)
    return; /* ignore messages that aren't 'ping' */

  struct discord_create_message params = { .content = "pong" };
  discord_create_message(client, msg->channel_id, &params, NULL);
}

int main(void) {
  struct discord *client = discord_init(BOT_TOKEN);
  discord_set_on_ready(client, &on_ready);
  discord_set_on_message_create(client, &on_message);
  discord_run(client);
}
```
*This is a minimalistic example, refer to [`examples/`](examples/) for a better overview.*

## Supported operating systems (minimum requirements)
* GNU/Linux 4.x
* FreeBSD 12
* NetBSD 8.1
* Windows 7 (Cygwin)
* GNU/Hurd 0.9
* Mac OS X 10.9

## Build Instructions

### On Windows

* Install **Cygwin**
* **Make sure that you installed libcurl, gcc, make, and git when you ran the Cygwin installer!**
* You will want to check the Windows tutorial [here](docs/WINDOWS.md)!
* Mingw64 and Msys2 are currently NOT supported. Please see [this](docs/MSYS2_MINGW64.md) for more information.
* Once installed, compile it normally like you would on UNIX/Linux/OS X/BSD.
* Note: you will likely need to include `-L/usr/local/lib -I/usr/local/include` on your `gcc` command, or in your `CFLAGS` variable in your Makefile for your bot.

### On Linux, BSD, and Mac OS X

The only dependency is `curl-7.56.1` or higher. If you are compiling libcurl from source, you will need to build it with SSL support.

#### Ubuntu and Debian

```console
$ sudo apt install -y build-essential libcurl4-openssl-dev
```

#### Void Linux

```console
$ sudo xbps-install -S libcurl-devel
```

#### Alpine

```console
$ sudo apk add curl-dev
```

#### FreeBSD

```console
$ pkg install curl
```

#### OS X
* Note: you will need to install Xcode, or at a minimum, the command-line tools with `xcode-select --install`.
```console
$ brew install curl (Homebrew)
$ port install curl (MacPorts)
```

### Setting up your environment

#### Clone Concord into your workspace

```console
$ git clone https://github.com/cogmasters/concord.git && cd concord
```

#### Compile Concord 

```console
$ make
```

#### Special notes for non-Linux systems

You might run into trouble with the compiler and linker not finding your Libcurl headers. You can do something like this:
```console
$ CFLAGS=-I<some_path> LDFLAGS=-L<some_path> make
```
For instance, on a FreeBSD system:
```console
$ CFLAGS=-I/usr/local/include LDFLAGS=-L/usr/local/lib make
```
On OS X using MacPorts:
```console
$ CFLAGS=-I/opt/local/include LDFLAGS=-L/opt/local/lib make
```
On OS X using a self-compiled libcurl:
```console
$ CFLAGS=-I/usr/local/include LDFLAGS=-L/usr/local/include make
```
On Windows with Cygwin, you might need to pass both arguments to use POSIX threading:
```console
$ CFLAGS="-pthread -lpthread" make
```

#### Special compilation flags

The following section outlines flags that can be attached to the Makefile if you wish to override the default compilation behavior with additional functionalities. Example:

```console
$ CFLAGS="-DCCORD_SIGINTCATCH -DCCORD_VOICE" make
```

* `-DCCORD_SIGINTCATCH`
    * By default Concord will not shutdown gracefully when a SIGINT is received (i.e. <kbd>Ctrl</kbd>+<kbd>c</kbd>), enable this flag if you wish it to be handled for you.
* `-DCCORD_VOICE`
    * Enable experimental Voice Connection handling.
* `-DCCORD_DEBUG_WEBSOCKETS`
    * Enable verbose debugging for WebSockets communication.
* `-DCCORD_DEBUG_ADAPTER`
    * Enable verbose debugging for REST communication.

### Configuring Concord

The following outlines the default fields of `config.json`
```js
{
  "logging": { // logging directives
    "level": "trace",        // trace, debug, info, warn, error, fatal
    "filename": "bot.log",   // the log output file
    "quiet": false,          // change to true to disable logs in console
    "overwrite": true,       // overwrite file if already exists, append otherwise
    "use_color": true,       // display color for log entries
    "http": {
      "enable": true,        // generate http specific logging
      "filename": "http.log" // the HTTP log output file
    },
    "disable_modules": ["WEBSOCKETS", "USER_AGENT"] // disable logging for these modules
  },
  "discord": { // discord directives
    "token": "YOUR-BOT-TOKEN",         // replace with your bot token
    "default_prefix": {                 
      "enable": false,                 // enable default command prefix
      "prefix": "YOUR-COMMANDS-PREFIX" // replace with your prefix
    }
  }
}
```

### Test Copycat-Bot

1. Get your bot token and add it to `config.json`, 
   by assigning it to discord's "token" field. There are 
   well written instructions from 
   [discord-irc](https://github.com/reactiflux/discord-irc/wiki/Creating-a-discord-bot-&-getting-a-token)
   explaining how to get your bot token and adding it to a server.
2. Build example executables:
   ```console
   $ make examples
   ```
3. Run Copycat-Bot:
   ```console
   $ cd examples && ./copycat
   ```

#### Get Copycat-Bot Response

Type a message in any channel the bot is part of and the bot should send an exact copy of it in return.

#### Terminate Copycat-Bot

With <kbd>Ctrl</kbd>+<kbd>c</kbd> or with <kbd>Ctrl</kbd>+<kbd>|</kbd>

## Installing Concord

*(note -- `#` means that you should be running as root)*

```console
# make install
```

This will install the headers and libary files into $PREFIX. You can override this as such:
```console
# PREFIX=/opt/concord make install
```

Note that included headers must be `concord/` prefixed:
```c
#include <concord/discord.h>
```

### Standalone executable

#### GCC 

```console
$ gcc myBot.c -o myBot -pthread -ldiscord -lcurl
```

#### Clang

```console
$ clang myBot.c -o myBot -pthread -ldiscord -lcurl
```

#### UNIX C compilers
##### This includes the following compilers:
* IBM XL C/C++ (AIX, z/OS, possibly IBM i)
* Sun/Oracle Studio (Solaris)
* IRIX MIPSpro C++ (IRIX) -- NOTE: currently not supported
* Possibly others!
```console
$ cc myBot.c -o myBot -ldiscord -lcurl -lpthread
```

Note: some systems such as **Cygwin** require you to do this:
```console
$ gcc myBot.c -o myBot -pthread -lpthread -ldiscord -lcurl
```
(this links against libpthread.a in `/usr/lib`)

## Recommended debuggers

First, make sure your executable is compiled with the `-g` flag to ensure human-readable debugger messages.

### Valgrind

Using valgrind to check for memory leaks:

```console
valgrind --leak-check=full ./myBot
```
For a more comprehensive guide check [Valgrind's Quick Start](https://valgrind.org/docs/manual/quick-start.html).

### GDB

Using GDB to check for runtime errors, such as segmentation faults:

```console
$ gdb ./myBot
```
And then execute your bot from the gdb environment:
```console
(gdb) run
```
If the program has crashed, get a backtrace of the function calls leading to it:
```console
(gdb) bt
```

For a more comprehensive guide check [Beej's Quick Guide to GDB](https://beej.us/guide/bggdb/)

## Support

Problems? Check out our [Discord Server](https://discord.gg/Y7Xa6MA82v).

## Contributing

All kinds of contributions are welcome, all we ask is to abide to our [guidelines](docs/CONTRIBUTING.md)! If you want to help but is unsure where to get started then our [Discord API Roadmap](docs/DISCORD_ROADMAP.md) is a good starting point. Check our [links](#links) for more helpful information.

## Getting Started

- [Documentation](https://cogmasters.github.io/concord/)
- [Discord API Roadmap](docs/DISCORD_ROADMAP.md)

## Useful links

- [Migrating from Orca](https://gist.github.com/lcsmuller/a5f2b205c3871888656b86825db90187)
