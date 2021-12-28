---
title: "Chat Server Protocol"
date: 2021-12-28T22:22:01Z
draft: false
---

I wrote a previous post about [clojure](/posts/2021-10-06-clojure/) where I expressed an intent to write a slack clone using the language. Like most ideas, this didn't go anywhere, however it did spawn a new idea: Chat Server Protocol.

There are several different popular messaging platforms, each used by different groups of people I know, for example WhatsApp, Telegram, Signal, Facebook Messenger, Slack & Discord. Having to install each one and learn the particulars of it's user interface can be tiresome. Some of them only cater for certain platforms via their official apps, e.g. WhatsApp is only available for iOS, Android and KaiOS.

Naturally some people have tried to solve this problem already and provide a universal chat client. Here are some projects I know about and what their approach is:

- [meetfranz](https://meetfranz.com/) is embedding the web UI of each chat platform into a single electron app.
- [pidgin](https://pidgin.im/) is running a plugin system where contributors can write a shared library to support a new platform.
- [beeper](https://beeper.com) is bridging messaging platforms on the server side and using [matrix](https://matrix.org) as a central system.

All of these approaches have some drawbacks. For example, franz isn't saving you from learning the web UI of each platform (citation needed, I need to try franz for longer first). Beeper relies on a hosted service running the matrix server and bridges, and you must also trust them to hold credentials to the various platforms.

I prefer pidgin's approach: a plugin system. The code is split into several pieces: libpurple is the 'core' which handles the basic functions of a messenger app. There are many 'plugins' which link into the core to provide implementations for different platforms. There are several UI programs which then build on libpurple to provide a user interface. Pidgin is one of these, and it provides an interface using GTK+.

This approach lowers the barrier to entry for integrating new messaging platforms, because the developer does not _also_ have to write the user interface. It also lowers the barrier to entry for writing new user interfaces, since the developer does not also have to write the backend integration to the messaging platform. However, the most significant downside is that plugins are called as shared libraries. This means plugin developers are restricted to C, C++ or other languages that can follow C function calling conventions. Theoretically most languages can do this, however it is often impractical, for example [calling java functions from C](https://nachtimwald.com/2017/06/17/calling-java-from-c/) involves booting up a JVM from your C code.

A similar problem has recently been solved in the field of IDE's (Integrated Development Environments): Language Server Protocol is a protocol which defines all the 'core' operations of an IDE. Requests are made by a 'client' to a 'server' via IPC (Inter-process communication) mechanisms such as standard streams (stdin/out), TCP or unix sockets or named pipes.

I propose the same technique could be applied to chat applications: define a 'Chat Server Protocol' which encapsulates all the core operations of a chat app. Clients and servers can then both be written against this protocol enabling far greater interoperability. Since the only requirement for implementing the protocol is some IPC mechanism, which every practical programming langauge supports, then this provides much greater implementation flexibility compared to libpurple. An additional benefit is that crashes in the server implementation can be handled more gracefully by the client, since they are in different processes a crash in the server will not automatically kill the client.

Some potential problems with this approach:
- multi-processing is more complicated, and indeed impossible on some operating systems (iOS)
- unlike programming language vendors, some chat platform providers are hostile to third party clients (WhatsApp, Discord)

Despite the downsides, I am interested to see if such an approach could gain traction. LSP has many clients now, of which the original (VS Code) is just one. There are language servers for nearly all popular programming languages as well; so the choices available to software developers wanting useful features has increased enormously. I'd really like to see the same happen in the messaging space.