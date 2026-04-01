# ZPack

<p align="center">

<img src="https://www.svgrepo.com/download/236716/package-box.svg" height="130">

</p>

<p align="center">

<img src="https://img.shields.io/github/v/release/rneacsu/zpack?style=flat">
<img src="https://img.shields.io/github/stars/rneacsu/zpack?style=flat">
<img src="https://img.shields.io/github/contributors/rneacsu/zpack?style=flat">
<img src="https://img.shields.io/github/license/rneacsu/zpack?style=flat">

</p>

<p align="center">
A simple Zsh manager for all your needs.
</p>

## Table of Contents

* [Background](#background)
* [Getting started](#getting-started)
* [Commands](#commands)
  * [`zpack load`](#zpack-load)
  * [`zpack omz`](#zpack-omz)
  * [`zpack complete`](#zpack-complete)
  * [`zpack apply`](#zpack-apply)
  * [`zpack update`](#zpack-update)
  * [`zpack reset`](#zpack-reset)
  * [`zpack prune`](#zpack-prune)
  * [`zpack help`](#zpack-help)
  * [`zpack list`](#zpack-list)
  * [`zpack stats`](#zpack-stats)
* [License](#license)

## Background

This project was created mainly because I needed a ZSH plugin manager that satisfies the following requirements:

* have the entire configuration in `.zshrc`
* the configuration is easy to read and understand
* can load [Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh) plugins and libraries
* is simple to maintain and extend

After testing a couple of existing managers I couldn't find one that satisfied all the requirements, was maintained and worked with the plugins I am using. Therefore, I decided to create my own. It was also a good opportunity to learn more about Zsh.

It is heavily inspired from [zgenom](https://github.com/jandamm/zgenom) which in turn is an extension of [zgen](https://github.com/tarjoilija/zgen) which was in turn inspired by [Antigen](https://github.com/zsh-users/antigen). I also took some bits from [zinit](https://github.com/zdharma-continuum/zinit). All credits go to the respective authors and contributors.

In terms of speed is as fast as it gets once the initial setup is done and all plugins are downloaded and cached. Changing the configuration invalidates the cache and takes longer to apply, but realistically you will only want to do this pretty rarely. Therefore, when not using the cache, the code is optimised for readability and maintainability. Otherwise, it is optimised for speed.

## Getting started

Add the following lines to `~/.zshrc`:

```shell
# Init zpack
[[ -d "${ZDOTDIR:-$HOME}/.zpack" ]] || git clone https://github.com/rneacsu/zpack.git "${ZDOTDIR:-$HOME}/.zpack"
source "${ZDOTDIR:-$HOME}/.zpack/zpack.zsh"
```

Load plugins

```shell
# ohmyzsh libraries, required by most plugins
zpack omz lib/history.zsh
zpack omz lib/directories.zsh
zpack omz lib/completion.zsh
zpack omz lib/theme-and-appearance.zsh

# ohmyzsh plugins
zpack omz plugins/git
zpack omz plugins/sudo
zpack omz plugins/docker
zpack omz plugins/docker-compose
zpack omz plugins/kubectl

# Load regular plugins
zpack load zsh-users/zsh-autosuggestions
zpack load zsh-users/zsh-completions
zpack load zsh-users/zsh-syntax-highlighting

# Load theme
zpack load romkatv/powerlevel10k::powerlevel10k
[[ ! -f ~/.p10k.zsh ]] || source ~/.p10k.zsh
```

At the end make sure you apply the configuration using the following:

```shell
# Finally, apply all changes and initialise completion system
zpack apply
```

You can also check out my [dotfiles](https://github.com/rneacsu/dotfiles) for a working configuration.

In order to avoid a warning about completions already initialised, please make sure you have the following in `~/.zshenv`:

```shell
# Skip the not really helping Ubuntu global compinit
skip_global_compinit=1
```

## Commands

For all available commands, please run `zpack help`. Also check out the completions for command flags. The main ones are described below:

### `zpack load`

Loads a plugin. It takes one argument which is the repository or location of the plugin to load. Accepted formats are:

```shell
zpack load <author>/<repo> # Loads https://github.com/<author>/<repo>
zpack load <author>/<repo>@<branch> # Loads https://github.com/<author>/<repo> at branch/tag <branch>
zpack load <author>/<repo>::<location> # Loads https://github.com/<author>/<repo> at location <location>
zpack load <author>/<repo>@<branch>::<location> # Loads https://github.com/<author>/<repo> at branch/tag <branch> and location <location>
zpack load ./path/to/plugin # Loads plugin from local path
zpack load /absolute/path/to/plugin # Loads plugin from absolute path
```

It does the following:

1. download the plugin if it's not already downloaded
1. add the `functions` directory to `fpath` if it exists, otherwise the plugin's directory is added
1. find and load the plugin's entry point using common patterns

### `zpack omz`

Loads Oh My Zsh plugins and libraries. It is a wrapper around `zpack load` which also creates the necessary directories and environment variables for Oh My Zsh plugins to work. The accepted formats are:

```shell
zpack omz plugins/<plugin>
zpack omz lib/<library>.zsh
```

### `zpack complete`

Registers completions for the program/command given as argument. This is usefull when generating completions in the background which don't finish by the time the completion system is initialised. This is the case for many Oh My Zsh plugins which generate completions in the background. You can also pass the following flags:

* `-g, --generate` - generate completions for the program/command. The flag takes as argument the command to run to generate completions. If no command is given, then `<program> completion zsh` is used.

Examples:

```shell
# Kubectl completions take a long time to generate using the Oh My Zsh plugin, so we register them manually
zpack complete kubectl
# Helm completions are generated and registered using the `helm completion zsh` command
zpack complete helm -g
# Use custom command to generate completions for delta
zpack complete delta -g 'delta --generate-completion zsh'
```

### `zpack apply`

Applies all changes made by the previous commands. This should be the last `zpack` related command in the `.zshrc` file. This initialises the completion system and caches the resulting configuration for future use.

### `zpack update`

Updates the manager and all plugins to the latest version. It takes one optional argument which controls what to update:

```shell
zpack update # updates everything
zpack update all # same as above
zpack update self # updates only the manager
zpack update plugins # updates only the plugins
```

### `zpack reset`

Clears the cache entirely and restarts the shell. Usually this should not be needed, but on certain occasions it might be helpful in order to fix some completions or previous errors.

### `zpack prune`

Completely delete everything and start from scratch. This also deletes any downloaded plugins and starts fresh. Useful when you have a lot of previously downloaded plugins that you no longer need.

### `zpack help`

Shows available commands.

### `zpack list`

List all plugins that are currently loaded.

### `zpack stats`

Display current shell statistics, like number of plugins, load time, cache and completion system status. Useful for discovering plugins which slow down the shell startup.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
