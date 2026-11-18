Vicotr's tmux configuration
=============

Author: Victor Fu <supergothere@gmail.com>

Installation
------------

```
git clone https://github.com/victorfu/tmuxconf.git ~/tmuxconf
```

```
ln -s ~/tmuxconf/tmux.conf ~/.tmux.conf
ln -s ~/tmuxconf/tmuxline ~/.tmuxline
```

Requires tmux >= 3.2.

Plugins (optional)
------------------

tmux-resurrect is loaded through [tpm](https://github.com/tmux-plugins/tpm) when it is installed:

```
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```

Then press `prefix + I` inside tmux to install the plugins.
