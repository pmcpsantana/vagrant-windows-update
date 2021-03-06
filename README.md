# Vagrant Windows Update Provisioner

[![Latest version released](https://img.shields.io/gem/v/vagrant-windows-update.svg)](https://rubygems.org/gems/vagrant-windows-update)
[![Package downloads count](https://img.shields.io/gem/dt/vagrant-windows-update.svg)](https://rubygems.org/gems/vagrant-windows-update)

This is a Vagrant plugin for installing Windows updates.

**NB** This was only tested with Vagrant 2.0.3 and Windows Server 2016.

# Installation

```bash
vagrant plugin install vagrant-windows-update
```

# Usage

Add `config.vm.provision "windows-update"` to your `Vagrantfile` to update your
Windows VM during provisioning or manually run the provisioner with:

```bash
vagrant provision --provision-with windows-update
```

To troubleshoot, set the `VAGRANT_LOG` environment variable to `debug`.

## Example

In this repo there's an example [Vagrantfile](Vagrantfile). Use it to launch
an example.

First install the [Base Windows Box](https://github.com/rgl/windows-2016-vagrant).

Then install the required plugins:

```bash
vagrant plugin install vagrant-windows-update
vagrant plugin install vagrant-reload
```

Then launch the example:

```bash
vagrant up
```

**NB** On my machine this takes about 1h to complete... but YMMV!

## Filters

You can select which Windows Updates are installed by defining a set of filters, e.g.:

```ruby
config.vm.provision "windows-update", filters: [
  "exclude:$_.Title -like '*Preview*'",
  "include:$_.Title -like '*Cumulative Update for Windows*'",
  "include:$_.AutoSelectOnWebSites"]
```

**NB** If the `filters` argument is not used, only important updates are installed (equivalent of declaring a single `include:$_.AutoSelectOnWebSites` filter).

The general filter syntax is:

    ACTION:EXPRESSION

`ACTION` is a string that can have one of the following values:

| action    | description                                                  |
| --------- | ------------------------------------------------------------ |
| `include` | includes the update when the expression evaluates to `$true` |
| `exclude` | excludes the update when the expression evaluates to `$true` |

**NB** If no `ACTION` evaluates to `$true` the update will **NOT** be installed.

`EXPRESSION` is a PowerShell expression. When it returns `$true`, the
`ACTION` is executed and no further filters are evaluated.

Inside an expression, the Windows Update [IUpdate interface](https://msdn.microsoft.com/en-us/library/windows/desktop/aa386099(v=vs.85).aspx) can be referenced by the `$_` variable.

# Development

To hack on this plugin you need to install [Bundler](http://bundler.io/)
and other dependencies. On Ubuntu:

```bash
sudo apt install bundler libxml2-dev zlib1g-dev
```

Then use it to install the dependencies:

```bash
bundle
```

Build this plugin gem:

```bash
rake
```

Then install it into your local vagrant installation:

```bash
vagrant plugin install pkg/vagrant-windows-update-*.gem
```

You can later run everything in one go:

```bash
rake && vagrant plugin uninstall vagrant-windows-update && vagrant plugin install pkg/vagrant-windows-update-*.gem
```
