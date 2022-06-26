---
title: "Working with npm"
date: 2020-02-18T08:29:30+02:00
draft: false
---

## Installing npm

Since npm is bundled with Node.js it is sufficient to install Node.js:

```bash
zypper in nodejs14
# check versions
node -v
npm -v
```

## Updating npm

```bash
npm install -g npm
```

## Updating dependencies

A convenient way to update dependencies is using the tool [npm-check-updates](https://www.npmjs.com/package/npm-check-updates):

```bash
npm install -g npm-check-updates
# check for updates
ncu
# apply updates
ncu -u
# necessary to fetch the new dependencies
npm install
```

## Linking packages

When building a package you usually want to test it. You can either publish it directly but when you change something you have to publish it again.
This is not an ideal process. The better way is to locally link the package and test it afterwards. This process has 2 steps:

1. Link the created package in its current directory, which will create a symlink in the global npm folder `{prefix}/lib/node_modules/<package>`.

```bash
npm link
```

1. Link the package in  some other location, npm link package-name will create a symbolic link from globally-installed package-name to `node_modules/` of the current folder.

```bash
npm link package-name
```

Note that the link should be to the package name, not the directory name for that package.
