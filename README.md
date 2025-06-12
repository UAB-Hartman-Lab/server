---
Server scripts and configs are organized using GNU stow packages.

Examples:

* Deploy system-wide MATE layout and themes: `sudo stow --adopt -R -t / theme`
* Deploy system-wide scripts: `sudo stow --adopt -R -t / scripts`
* Deploy system config files: `sudo stow --adopt -R -t / config`