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

## Concord 1.0.0

## About

Concord is implemented in plain C99, its symbols are organized to be easily matched to the documentation of the API being covered.
Concord's implementation has minimum external dependencies to make bot deployment deadly simple.

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

## Build Instructions

### On Windows

* Install **Cygwin**
* **Make sure that you installed libcurl, gcc, make, and git when you ran the Cygwin installer!**
* You will want to check the Windows tutorial in the `docs` folder here!

### On Linux, BSD, and Mac OS X

The only dependency is `curl-7.56.1` or higher. If you are compiling libcurl from source, you will need to build it with SSL support.

#### Ubuntu and Debian

```bash
$ sudo apt install -y build-essential libcurl4-openssl-dev
```

#### Void Linux

```bash
$ sudo xbps-install -S libcurl-devel
```

#### Alpine

```bash
$ sudo apk add curl-dev
```

#### FreeBSD

```bash
$ pkg install curl
```

#### OS X
```bash
$ brew install curl (Homebrew)
$ port install curl (MacPorts)
```
### Setting up your environment

#### Clone Concord into your workspace

```bash
$ git clone https://github.com/cogmasters/concord.git && cd concord
```

#### Compile Concord 

```bash
$ make
```

#### Special notes for non-Linux systems
You might run into trouble with the compiler and linker not finding your Curl headers. You can do something like this:
```bash
$ CFLAGS=-I<some_path> LDFLAGS=-L<some_path> make
```
For instance, on a FreeBSD system:
```bash
$ CFLAGS=-I/usr/local/include LDFLAGS=-L/usr/local/lib make
```

On OS X using MacPorts:
```bash
$ CFLAGS=-I/opt/local/include LDFLAGS=-L/opt/local/lib make
```

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
   ```bash
   $ make examples
   ```
3. Run Copycat-Bot:
   ```bash
   $ cd examples && ./copycat
   ```

#### Get Copycat-Bot Response

Type a message in any channel the bot is part of and the bot should send an exact copy of it in return.

#### Terminate Copycat-Bot

With <kbd>Ctrl</kbd>+<kbd>c</kbd> or with <kbd>Control</kbd>+<kbd>|</kbd>

## Installing Concord

```bash
# make install
```
(note -- `#` means that you should be running as root)

Included headers must be `concord/` prefixed:
```c
#include <concord/discord.h>
```

This will install the headers and libary files into $PREFIX. You can override this as such:
```bash
# PREFIX=/opt/concord make install
```

### Standalone executable

#### GCC 

```bash
$ gcc myBot.c -o myBot -pthread -ldiscord -lcurl
```

#### Clang

```bash
$ clang myBot.c -o myBot -pthread -ldiscord -lcurl
```

#### UNIX C compiler
```bash
$ cc myBot.c -o myBot -ldiscord -lcurl -lpthread
```

Note: some systems such as **Cygwin** require you to do this:
```bash
$ gcc myBot.c -o myBot -pthread -lpthread -ldiscord -lcurl
```
(this links against libpthread.a in `/usr/lib`)

## Recommended debuggers

First, make sure your executable is compiled with the `-g` flag to ensure human-readable debugger messages.

### Valgrind

Using valgrind to check for memory leaks:

```bash
valgrind --leak-check=full ./myBot
```
For a more comprehensive guide check [Valgrind's Quick Start](https://valgrind.org/docs/manual/quick-start.html).

### GDB

Using GDB to check for runtime errors, such as segmentation faults:

```bash
$ gdb ./myBot
```
And then execute your bot from the gdb environment:
```bash
(gdb) run
```
If the program has crashed, get a backtrace of the function calls leading to it:
```bash
(gdb) bt
```

For a more comprehensive guide check [Beej's Quick Guide to GDB](https://beej.us/guide/bggdb/)

## Support

Problems? Check out our [Discord Server](https://discord.gg/Y7Xa6MA82v).

## Contributing

All kinds of contributions are welcome, all we ask is to abide to our [guidelines](docs/CONTRIBUTING.md)! If you want to help but is unsure where to get started then our [Discord API Roadmap](docs/DISCORD_ROADMAP.md) is a good starting point. Check our [links](#links) for more helpful information.

## Getting Started

- [Documentation](https://cogmasters.github.io/concord/)
- [Building your first bot](docs/BUILDING_A_BOT.md)
- [Discord API Roadmap](docs/DISCORD_ROADMAP.md)

## Useful links

- [Migrating from Orca](https://gist.github.com/lcsmuller/a5f2b205c3871888656b86825db90187)
