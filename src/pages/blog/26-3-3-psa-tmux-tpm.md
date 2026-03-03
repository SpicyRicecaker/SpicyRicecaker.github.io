---
layout: "../../layouts/BlogPost.astro"
title: "PSA: Don't use `$XDG_HOME/.config/tmux/tmux.conf`, use `$XDG_HOME/.config/tmux/.tmux.conf`"
description: ""
pubDate: "March 3 2026"
# heroImage: "/placeholder-hero.jpg"
---


As of `tpm` commit hash `99469c4a9b1ccf77fade25842dc7bafbc8ce9946` and earlier, `tpm` will fail to install your plugins if you put your config file into `$XDG_HOME/.config/tmux/.tmux.conf`.

**If you store your plugin file in `$XDG_HOME/.config/tmux`, the name of the config file needs to be `tmux.conf`**, rather than `tmux.conf`. Alternatively, if you store the `tmux` config file in the `$HOME` (`~`) directory, then it needs to be called `.tmux.conf`, rather than `tmux.conf`.

---

You might then logically ask the question, "if I source the `tmux` config file with `tmux source myconfigfile`, why am I not allowed to name the config file whatever I want and put it wherever I want"? 

Well, we must take a look at the chain of dependencies in the `tpm` source. `tpm` is essentially just a shell file, and as of the above commit hash this is the chain of dependencies I was able to identify that `tpm` calls in sourcing a plugin:

`tpm > main() > source_plugins() > tpm_plugins_list_helper() > _tmux_conf_contents() > _get_user_tmux_conf()`

It's a logical sequence, right? We source a list of plugins. To get a list of plugins, we need to look at the tmux config file, and to get the tmux content file, we need to look at the user tmux config file.

Well, the very last function in that sequence, `_get_user_tmux_conf`, as of commit `99469c4a9b1ccf77fade25842dc7bafbc8ce9946`, [manually reads only 2 locations to find the `tmux` config file path](https://github.com/tmux-plugins/tpm/blob/99469c4a9b1ccf77fade25842dc7bafbc8ce9946/scripts/helpers/plugin_functions.sh#L23):

```shell
xdg_location="${XDG_CONFIG_HOME:-$HOME/.config}/tmux/tmux.conf"
default_location="$HOME/.tmux.conf"
```

In other words, despite whatever files you might've sourced with `tmux source yourconfigfile`, the only two files `tpm` actually reads are these hardcoded paths.

---

If you think that this is incredibly unintuitive and could cause a percentage of new users, who see that `.tmux.conf` is the naming scheme for tmux config files stored in `~`, and then proceed to logically extrapolate that naming scheme into `~/.config/tmux`, and then wonder why no `tpm` packages are being installed --- the installation fails silently --- you are not alone... at least one other person spent 3 hours debugging this.

It seems that the particular chunk of code that allowed for `$XDG_HOME` as a configuration option was added into `tmux` quite recently, around 8 years ago in 2018, which was ~10 years after the start of `tmux` in 2007, and 3 years after the creation of `tpm` itself: https://github.com/tmux-plugins/tpm/commit/2c4a2dfd655c13b4e15d693e2c2dba529b1d1c48.

We see in other parts of the source code that the lines defining plugins are [manually](https://github.com/tmux-plugins/tpm/blob/99469c4a9b1ccf77fade25842dc7bafbc8ce9946/scripts/helpers/plugin_functions.sh#L77C21-L77C27) [extracted](https://github.com/tmux-plugins/tpm/blob/99469c4a9b1ccf77fade25842dc7bafbc8ce9946/scripts/helpers/plugin_functions.sh#L49) from the tmux config using regex.

Key lessons from this: 
- When facing an error from a library that seems to have no easy Google fix, do not be afraid of diving into the source code
- +1 Open source
- In making a user-facing extension that interacts with a main program and config files, it is probably good to explicitly mention in the documentation when a design decision is made that results in only a subset of features being available from the main program. `tmux` works with sourcing any config file in any directory, but not `tpm`.
- Github stars != good code (https://github.com/samuelmarina/is-even)
- Probably use a well-established BNF format config language like `toml`, `yaml`, or `json` to avoid having to manually write regex.
- All the negatives about Bash (and there are *many*), it is fairly easy to debug, being interpretable, and most times fairly readable

---

## Acknowledgements

Thanks to @Dreams of Code for making an amazing [13-minute introduction](https://www.youtube.com/watch?v=DzNmUNvnB04) to `tmux`, explaining `tmux` in a nutshell, its keybinds, and good initial configuration settings such as consistent `vim`-`tmux` split navigation and rebinds to the `prefix` key.

