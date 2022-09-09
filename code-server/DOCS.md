# Home Assistant Add-on: Advanced Code Server

Code Server experience integrated in the Home Assistant frontend,
allowing you to edit your Home Assistant configuration directly from your
web browser.

The add-on has the Home Assistant, MDI icons and YAML extensions pre-installed
and pre-configured right out of the box. This means that auto-completion works
instantly, without the need for configuring anything.

Take a look [here][hassio-addons] if you are looking for the original
Code Server Add-on from HA Community Add-ons.

## Installation

To install this Add-On, manually add the HA-Addons repository to Home Assistant
using [this GitHub repository][ha-addons] or by clicking the button below.

[![Add Repository to HA][my-ha-badge]][my-ha-url]

## Preinstalled tools

- Ctop (CLI Interface to monitor and manage Docker)
- RClone (Rclone has powerful cloud equivalents to the unix
  commands rsync, cp, mv, mount, ls, ncdu, tree, rm, and cat.)
- Tailscale (Zero config VPN. Installs on any device in minutes.)
- Cloudflared (Cloudflare Tunnel provides you with a secure way to connect your
  resources to Cloudflare without a publicly routable IP address.)
- Custom Init scripts and services (see next chapter)

## Configuration

**Note**: _Remember to restart the add-on when the configuration is changed._

### Option: `init_path`

The `init_path` option allows to set the path containing the custom
init scripts and services. For exmpale `/share` will use directories:
`/share/code-server/{cont-init.d,services.d}`.

If this option isn't set, this function is disabled.

**Note**: _Please be aware that this is a really powerful function which can damage
your whole system if handled incorrectly._

### Option: `log_level`

The `log_level` option controls the level of log output by the addon and can
be changed to be more or less verbose, which might be useful when you are
dealing with an unknown issue. Possible values are:

- `trace`: Show every detail, like all called internal functions.
- `debug`: Shows detailed debug information.
- `info`: Normal (usually) interesting events.
- `warning`: Exceptional occurrences that are not errors.
- `error`: Runtime errors that do not require immediate action.
- `fatal`: Something went terribly wrong. Add-on becomes unusable.

Please note that each level automatically includes log messages from a
more severe level, e.g., `debug` also shows `info` messages. By default,
the `log_level` is set to `info`, which is the recommended setting unless
you are troubleshooting.

## Custom Init scripts

When `init_path` configuration option is set. The add-on will generate
the two folders `cont-init.d` and `services.d`.

Place your custom cont-init files inside the folder `cont-init.d` and
they will be processed during add-on startup phase.

Place your custom service file called `run` inside a
subfolder of `services.d` and a corresponding service will run
when the add-on starts.
For example: `services.d/my-service/run` will spin up a service called `my-service`
Place your service logic in the file called `run`.

**Note**: _Please be aware that this is a really powerful function which can damage
your whole system if handled incorrectly._

**Note**: _Use command `cust-service` to control your custom services from code
server terminal. Use `--help` for more information._

## Resetting your Code Server settings to the add-on defaults

The add-on updates your settings to be optimized for use with Home Assistant.
As soon as you change a setting, the add-on will stop doing that since it
might be destructive. However, in case you changed some things, but want to
return to the defaults as delivered by this add-on, do the following:

1. Open the Visual Studio Code editor.
1. Click on `Terminal` in the top menu bar and clik on `New Terminal`.
1. Execute the following command in the terminal window: `reset-settings`.
1. Done!

[my-ha-badge]: https://my.home-assistant.io/badges/supervisor_add_addon_repository.svg
[my-ha-url]: https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2Felcajon-tech%2Frepository-stable
[ha-addons]: https://github.com/elcajon-tech/repository-stable
[hassio-addons]: https://github.com/hassio-addons/addon-vscode
