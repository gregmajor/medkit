<!--![MEDKIT](medkit.png "MEDKIT")-->
# MEDKIT
> MEDKIT (Multi-Environment Dotfiles Kit) is the dotfile management solution for the obsessive compulsive.

[![Go Report Card][go-report-card-image]][go-report-card-url]
[![CircleCI][circleci-image]][circleci-url]

### Overview
MEDKIT is a tool to help you take control of your preferences and configuration across all of your computers, including:

* [Dotfile Management][dotfile-management]
* [Run Command (rc) Script Management][rc-management]
* [PATH Variable Management][path-management]

### Dotfile Management <a id="dotfile-management"></a>
The trouble with dotfiles is that, unless you have just one account on one computer, you'll have lots of the little
devils spread all over the place. If you're obsessive like us, you'd prefer to have your preferences and configuration
consistent no matter where you log in. MEDKIT follows a "Write Once, Use Anywhere" philosophy. With a little help from
your favorite version control tool, you can keep a "master" copy of your dotfiles and apply them anywhere you go.

### Run Command (rc) Script Management <a id="rc-management"></a>
Startup files often start simple, but turn into a nightmare to manage. It gets worse when tools and installers try to be
helpful and make changes to them without asking. You can take advantage of MEDKIT's version control integration and
templates to get your startup scripts under control once and for all.

### PATH Variable Management <a id="path-management"></a>
Much like startup scripts, keeping your environment's `PATH` in order can be a real challenge. MEDKIT's convention-based
system can help!

### But Why?
MEDKIT certainly isn't the first or only one of its kind. In fact, you really don't _need_ a tool to manage your
dotfiles at all. Most people don't use a tool like MEDKIT and get along just fine (usually).

We're building MEDKIT to suit our own needs and sharing it with the world. If you need something a bit different or more
feature complete, consider [one of these alternatives][alternatives] instead.

## Conventions <a id="conventions"></a>
MEDKIT favors convention over configuration (however, [nearly everything can be configured][configuration]). Let's first
look at the dotfiles directory structure.

### Dotfiles Directory Structure
The convention for the dotfiles directory is this:
```
dotfiles/
├ setting-group
│  ├ .dotfile
├ default.env
├ host.env
```

Where:

#### `~/dotfiles`
This is the root folder. By default it is located in your home directory and named `dotfiles`.

#### `setting-group`
Each subdirectory underneath the root is considered a Setting Group. In a Setting Group directory you will keep all of
the files related to a particular topic, program, or some other logical group. Examples might be `vim` or `dotnet` or
`programming`.

The Setting Group folders matter because that's how MEDKIT allows you to enable or disable settings per environment. In
other words, if you use vim on your Mac and emacs on your Linux computers, you might have two Setting Groups; `vim` and
`emacs`. Inside each directory you would keep your respective dotfiles and so forth.

#### `.dotfile`
Any file that begins with a hard stop (a.k.a., "dot" or "period") is a dotfile. This is the good stuff we're trying to
manage and organize. A good example might be a `.vimrc` file in a Setting Group directory named `vim`.

#### `default.env`
In the root folder you can create Environment Configurations. These are plain text files formatted with YAML that define
how MEDKIT should behave on different computers. The `default.env` file contains all the stuff that applies to every one
of your environments.

#### `host.env`
Much like the `default.env` file defines what should happen in ALL of your environments, you can also have environment-
specific configurations. MEDKIT's convention is to use `$HOST.env` and so, assuming your computer's hostname was set as
`sapphire`, your environment specific file would be named `sapphire.env`.

### Example Dotfiles Directory
In practice, a dotfiles directory might look something like this:
```
dotfiles/
├ node
│  └ .npmrc
├ vim
│  └ .vimrc
├ default.env
├ sapphire.env
```

## Installation <a id="installation"></a>
Download a binary from the [releases page][releases-url], or if you have the go programming language installed, run
`go get github.com/LeadPipeSoftware/medkit`.

### macOS:
At the moment we provide a pre-compiled, self-contained binary. Download the version appropriate for your OS and
architecture and put it in a `$HOME/bin` folder (make sure that folder is in your `$PATH`) or wherever you feel
comfortable (like perhaps `/usr/local/bin`).

### Linux:
At the moment we provide a pre-compiled, self-contained binary. Download the version appropriate for your OS and
architecture and put it wherever your distribution suggests.

### Windows WSL:
We don't currently have a Windows Subsystem for Linux (WSL) binary, but it's coming very soon!

## README-Oriented
MEDKIT is a work in progress and follows the [README Driven Development][readme-driven-development-url] model. As such,
you'll see some things that are considered feature complete, some things that are a work in progress, and some things
that simply haven't been started. We'll try to follow this convention in our docs:

* [ ] Incomplete
* [X] Complete (or at least _good enough for now_)

## Usage

### Initialization
If you already have a dotfiles directory just ensure that it follows the [conventions described above][conventions]. If
you do not yet have your own dotfiles directory, let MEDKIT help you create one:
```sh
$ medkit init
```

This command will:

* Create a new folder in your `$HOME` directory named `dotfiles`
* Create a blank `default.env` file
* Create a blank `$HOST.env` file

As with most of MEDKIT's commands, you can change the behavior with command line flags. Be sure to type
`$ medkit init --help` to see all your options.

### [ ] Adding Dotfiles
Once you have a dotfiles directory, you can add existing dotfiles like this:
```sh
$ medkit add dotfile -f ~/{dotfile-name} -d {setting-group-directory-name}
```

Where:

* `{dotfile-name}` is the name of the dotfile to add
* `{setting-group-directory-name}` is the name of the Setting Group directory to put the dotfile in

This command will:

* Create a new folder in your dotfiles repo (if it doesn't already exist) named `vim`
* Create a backup of the original file named `.vimrc.backup`
* Move the specified dotfile into the folder
* Add the dotfile to your `$HOST.env` configuration
* Run the equivalent of `$ medkit install dotfiles`

Note that there's a lot of ways to change this behavior. For example you could:

* Opt to skip the backup (`--no-backup` or `-b`)
* Skip adding the dotfile to any configuration (`--no-environment` or `-e`)
* Add the dotfile to the default environment (`--default-environment` or `-d`)
* Skip the installation (`--no-install` or `-i`)

To see all of the available options, just type `$ medkit add dotfile --help`.

### [X] Viewing Dotfiles
You can view all of the dotfiles MEDKIT is managing like this:
```sh
$ medkit show dotfiles
```

### [X] Install Dotfiles
When you run `$ medkit install dotfiles` any file that starts with a hard stop (a.k.a., "dot" or "period") defined in
your `default.env` or your `$HOST.env` files will be symlinked to your `$HOME` directory. Programs that use these files
will see them as they always did. Now your files are source controlled and, if you choose, can be made available to your
other computers.

### [X] Updating Dotfiles
When you make a change to a dotfile, you'll probably want to make that change available on all your computers. The
specifics of how to synchronize your MEDKIT repo will depend on the tools you choose. If you're using GitHub, for
example, you'll need to make your changes, push them to GitHub, then pull them on all your other computers. No matter
which process you prefer, your dotfiles will be automatically updated thanks to the symlinks.

### Configuration <a id="configuration"></a>
MEDKIT has reasonable, opinionated defaults for all of its settings. However, if you need to change something you have a
few options:

* Command line flags
* Environment variables
* Configuration file

#### Command Line Flags
To see what command line flags are available, just type:
```$ medkit --help```

You may also include a command such as `$ medkit install dotfiles --help` to see specific flags.

#### Environment Variables
You can set any of MEDKIT's settings in a matching environment variable. Environment variables are case sensitive and
must be prefixed with `MEDKIT_`. For example, you can set the location of your dotfiles directory with
`$ export MEDKIT_DotfilesDirectory=~/mydotfiles`.

#### Configuration File
**TODO: Add info regarding the config file.**

## How to Contribute
Contributions are welcome! Check out [this link][contributing] on how you can help!

## Release History
MEDKIT's full release history can be found [here][changelog].

## Credits
* [Zach Holman][zach-holman-github-url] and his excellent ["dotfiles are meant to be forked" blog post][zach-holman-blog-url]

## License
Distributed under the MIT license. See the [LICENSE][license] file for more information.

## Alternatives <a id="alternatives"></a>
As mentioned above, MEDKIT isn't alone in trying to solve the problem of managing dotfiles. Here are some alternatives
you might consider:

* [dotbot][https://github.com/anishathalye/dotbot]
* [dotdrop][https://github.com/deadc0de6/dotdrop]
* [dotfiles][https://github.com/jbernard/dotfiles]
* [dotgit][http://github.com/Cube777/dotgit]
* [dots][https://github.com/EvanPurkhiser/dots]
* [homeshick][https://github.com/andsens/homeshick]
* [homesick][https://github.com/technicalpickles/homesick]
* [Pearl][https://github.com/pearl-core/pearl]
* [vcsh][https://github.com/RichiH/vcsh]

<!-- Markdown link & img definitions -->
[conventions]: #conventions
[installation]: #installation
[configuration]: #configuration
[alternatives]: #alternatives
[homebrew-bundle]: https://coderwall.com/p/afmnbq/homebrew-s-new-feature-brewfiles
[changelog]: https://github.com/LeadPipeSoftware/medkit/blob/master/CHANGELOG.md
[authors]: https://github.com/LeadPipeSoftware/medkit/blob/master/AUTHORS.md
[contributing]: https://github.com/LeadPipeSoftware/medkit/blob/master/CONTRIBUTING.md
[security]: https://github.com/LeadPipeSoftware/medkit/blob/master/SECURITY.md
[license]: https://github.com/LeadPipeSoftware/medkit/blob/master/LICENSE
[wiki]: https://github.com/LeadPipeSoftware/medkit/wiki
[go-report-card-image]: https://goreportcard.com/badge/github.com/LeadPipeSoftware/medkit
[go-report-card-url]: https://goreportcard.com/report/github.com/LeadPipeSoftware/medkit
[circleci-image]: https://circleci.com/gh/LeadPipeSoftware/medkit.svg?style=shield
[circleci-url]: https://circleci.com/gh/LeadPipeSoftware/medkit
[releases-url]: https://github.com/LeadPipeSoftware/medkit/releases
[zach-holman-github-url]: zach@zachholman.com
[zach-holman-blog-url]: https://zachholman.com/2010/08/dotfiles-are-meant-to-be-forked/
[readme-driven-development-url]: http://tom.preston-werner.com/2010/08/23/readme-driven-development.html
