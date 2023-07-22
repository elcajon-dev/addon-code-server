# Home Assistant Add-on: Advanced Studio Code Server

[![GitHub Release][releases-shield]][releases]
![Project Stage][project-stage-shield]
[![License][license-shield]](LICENSE.md)

![Supports aarch64 Architecture][aarch64-shield]
![Supports amd64 Architecture][amd64-shield]
![Supports armhf Architecture][armhf-shield]
![Supports armv7 Architecture][armv7-shield]
![Supports i386 Architecture][i386-shield]

[![Github Actions][github-actions-shield]][github-actions]
![Project Maintenance][maintenance-shield]
[![GitHub Activity][commits-shield]][commits]

Studio Code Server, accessible through the browser.

## About

This add-on runs [code-server](https://github.com/cdr/code-server), which
gives you a Visual Studio Code experience straight from the browser. It allows
you to edit your Home Assistant configuration directly from your web browser,
directly from within the Home Assistant frontend.

The add-on has the Home Assistant, MDI icons and YAML extensions pre-installed
and pre-configured right out of the box. This means that auto-completion works
instantly, without the need for configuring anything.

[:books: Read the full add-on documentation][docs]

## Installation

To install this Add-On, manually add the HA-Addons repository to Home Assistant
using [this GitHub repository][ha-addons] or by clicking the button below.

[![Add Repository to HA][my-ha-badge]][my-ha-url]

## Other Features

Basic image based on [Home-Assistant Community Add-on Visual Studio Code][hassio-addons]
with the following additional features:

- Host Docker access
- Material Design icons pre-installed
- Cron installed and configured
- Tailscale installed
- Cloudflare installed
- Rclone installed
- Appdaemon & hass-apps installed
- Custom cont-init.d and services.d scripts

[aarch64-shield]: https://img.shields.io/badge/aarch64-no-red.svg
[amd64-shield]: https://img.shields.io/badge/amd64-yes-green.svg
[armhf-shield]: https://img.shields.io/badge/armhf-no-red.svg
[armv7-shield]: https://img.shields.io/badge/armv7-no-red.svg
[i386-shield]: https://img.shields.io/badge/i386-no-red.svg
[commits-shield]: https://img.shields.io/github/commit-activity/y/elcajon-tech/addon-code-server.svg
[commits]: https://github.com/elcajon-tech/addon-code-server/commits/main
[docs]: https://github.com/elcajon-tech/addon-code-server/blob/main/code-server/DOCS.md
[github-actions-shield]: https://github.com/elcajon-tech/addon-code-server/workflows/CI/badge.svg
[github-actions]: https://github.com/elcajon-tech/addon-code-server/actions
[license-shield]: https://img.shields.io/github/license/elcajon-tech/addon-code-server.svg
[maintenance-shield]: https://img.shields.io/maintenance/yes/2023.svg
[releases-shield]: https://img.shields.io/github/release/elcajon-tech/addon-code-server.svg
[releases]: https://github.com/elcajon-tech/addon-code-server/releases
[project-stage-shield]: https://img.shields.io/badge/project%20stage-production%20ready-brightgreen.svg
[hassio-addons]: https://github.com/hassio-addons/addon-vscode
[my-ha-badge]: https://my.home-assistant.io/badges/supervisor_add_addon_repository.svg
[my-ha-url]: https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2Felcajon-tech%2Frepository-stable
[ha-addons]: https://github.com/elcajon-tech/repository-stable
