+++
title = "Installing Java"
description = "One of my previous blog articles on basic Java"
date = 2024-02-19
[taxonomies]
tags = ["java", "archive"]
[extra]
toc = true
toc_inline = true
+++

Before you can run your first Java program, you need to have Java installed.
And since you’ll probably need multiple Java versions in the future — maybe even Maven (we’ll get to that later) —
I recommend installing [SDKMAN!](https://sdkman.io/) right away. It’ll make your life much easier.

SDKMAN! is basically a version manager for JDKs and SDKs.
It lets you easily download, install, and switch between different versions and tools whenever you need to.

---

### Installing SDKMAN!

In the following examples, don’t copy the `$` symbol into your terminal — just the command that follows it.

Run this command in your terminal:

```bash
$ curl -s "https://get.sdkman.io" | bash
```

Follow the on-screen instructions, and then run this command:

```bash
$ source "$HOME/.sdkman/bin/sdkman-init.sh"
```

After that, you can verify that it’s working by checking the version:

```bash
$ sdk version


SDKMAN!
script: 5.18.2
native: 0.4.6
```

You can find the official installation guide (including Windows instructions) [here](https://sdkman.io/install).

---

### Installing Java

Now installing Java is super simple.
Running the command without parameters will install the latest version:

```bash
$ sdk install java
```

After installation, SDKMAN! will ask if you want to set this version as default:

```bash
Do you want java 21.0.2-tem to be set as default? (Y/n):
```

Just press Enter, and it’ll confirm:

```
Setting java 21.0.2-tem as default.
```

That’s it — Java is installed and set up.

If you want to double-check that everything’s in place, run:

```bash
$ sdk current java
```

You should see something like this:

```bash
Using java version 21.0.2-tem
```

By the way, what you’ve just installed is the full **JDK (Java Development Kit)**,
so you already have everything you need to **run and build** Java applications.

