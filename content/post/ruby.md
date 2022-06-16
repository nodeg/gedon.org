---
title: "Working with Ruby"
date: 2020-06-25T09:50:10+02:00
draft: false
---

# Installation

```bash
zypper install ruby2.7 ruby2.7-devel
```

# Usefull commands

```bash
# List of commands
$ gem help commands

GEM commands are:

    build             Build a gem from a gemspec
    cert              Manage RubyGems certificates and signing settings
    check             Check a gem repository for added or missing files
    cleanup           Clean up old versions of installed gems
    contents          Display the contents of the installed gems
    dependency        Show the dependencies of an installed gem
    environment       Display information about the RubyGems environment
    fetch             Download a gem and place it in the current directory
    generate_index    Generates the index files for a gem server directory
    help              Provide help on the 'gem' command
    info              Show information for the given gem
    install           Install a gem into the local repository
    list              Display local gems whose name matches REGEXP
    lock              Generate a lockdown list of gems
    mirror            Mirror all gem files (requires rubygems-mirror)
    open              Open gem sources in editor
    outdated          Display all gems that need updates
    owner             Manage gem owners of a gem on the push server
    pristine          Restores installed gems to pristine condition from files
                      located in the gem cache
    push              Push a gem up to the gem server
    query             Query gem information in local or remote repositories
    rdoc              Generates RDoc for pre-installed gems
    search            Display remote gems whose name matches REGEXP
    server            Documentation and gem repository HTTP server
    signin            Sign in to any gemcutter-compatible host. It defaults to
                      https://rubygems.org
    signout           Sign out from all the current sessions.
    sources           Manage the sources and cache file RubyGems uses to search
                      for gems
    specification     Display gem specification (in yaml)
    stale             List gems along with access times
    uninstall         Uninstall gems from the local repository
    unpack            Unpack an installed gem to the current directory
    update            Update installed gems to the latest version
    which             Find the location of a library file you can require
    yank              Remove a pushed gem from the index

For help on a particular command, use 'gem help COMMAND'.

Commands may be abbreviated, so long as they are unambiguous.

# Update the RubyGems system software
gem update --system

# Update installed gems to the latest version
gem update

# Display information about the RubyGems environment
gem environment

RubyGems Environment:
  - RUBYGEMS VERSION: 3.1.4
  - RUBY VERSION: 2.7.1 (2020-03-31 patchlevel 83) [x86_64-linux-gnu]
  - INSTALLATION DIRECTORY: /usr/lib64/ruby/gems/2.7.0
  - USER INSTALLATION DIRECTORY: /home/dom/.gem/ruby/2.7.0
  - RUBY EXECUTABLE: /usr/bin/ruby.ruby2.7
  - GIT EXECUTABLE: /usr/bin/git
  - EXECUTABLE DIRECTORY: /usr/bin
  - SPEC CACHE DIRECTORY: /home/dom/.gem/specs
  - SYSTEM CONFIGURATION DIRECTORY: /etc
  - RUBYGEMS PLATFORMS:
    - ruby
    - x86_64-linux
  - GEM PATHS:
     - /usr/lib64/ruby/gems/2.7.0
     - /home/dom/.gem/ruby/2.7.0
  - GEM CONFIGURATION:
     - :update_sources => true
     - :verbose => true
     - :backtrace => true
     - :bulk_threshold => 1000
     - :benchmark => false
     - :install => "--format-executable --no-user-install"
     - "install" => "--format-executable --no-user-install"
     - :format_executable => true
     - :update => "--format-executable --no-user-install"
     - "update" => "--format-executable --no-user-install"
     - :sources => ["https://rubygems.org"]
  - REMOTE SOURCES:
     - https://rubygems.org
  - SHELL PATH:
     - /home/dom/.gem/ruby/2.7.0/bin
     - /home/dom/bin
     - /home/dom/git/diff-so-fancy
     - /usr/local/bin
     - /usr/bin
     - /bin

# Clean up old versions of installed gems
gem cleanup
```



