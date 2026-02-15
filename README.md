These are things I would recommend. They are listed in no particular order, and I am entirely unaffiliated.

<details>
<summary>Here are some common principles these tools share.</summary>

__incrementally explorable UX__
- moving from day one/zero understanding to power user should happen within the app, not a manual
- modal interfaces can be good for this
> Lazygit, which-key.nvim, btop

__low latency__
- _input_ latency matters the most
> Kitty, Neovim

__declarative state__
- let me describe what I want instead of how to get there
> nix, atlas

__exist at one extreme of the customization spectrum__
- either enough flexibility to entirely build your own UI/UX
- or such a prescriptive design that only a few options are exposed
> Neovim, Lazygit, Gnome, ghq

__minimal lock in__
- use simple data formats like plaintext, markdown, JSON, etc
- _can_ be self hosted, but I'll often pay for a high uptime service
> Bitwarden, ln.ht, WakaTime, Obsidian

__keyboard first__
- using a mouse is slower
- laptop trackpads are extra slow
> Neovim

</details>

## software

### [Nix(OS)](https://nixos.org/)
- declarative package (Nix) / system (NixOS) / user environment ([Home Manager](https://github.com/nix-community/home-manager)) management
    - installing, building, containerizing, virtualization, you name it, I'm using Nix
    - nix ~~can be~~ **is** confusing, poorly documented, and has some [drama](https://discourse.nixos.org/t/a-statement-from-members-of-the-moderation-team/69828)
    - once you get comfortable its awesome, but the learning curve is steep and the lack of adoption is unsuprising
- highlights
    - the store
        - packages built with `nix` are put in a content-addressed location, meaning the _input_ to the build is hashed, and the final location of the build in the store is `$HASH-packagename`. 
        - one of the side effects of this approach is installing multiple versions of the same package is trivial.
    - checkpoints
        - NixOS systems are almost unbreakable, because every rebuild makes a checkpoint. Each generation of the system fills $PATH with a collection of symlinks pointing to the nix store. If the majority of the installed packages is the same, keeping each generation becomes effectively free.
    - **[remote builds](https://nix.dev/manual/nix/2.18/advanced-topics/distributed-builds)**
        - anything you can build with Nix, you can build remotely with ~20 lines of config
        - why wait for my shrimpy laptop to compile gcc? just remotely build it on my beefy desktop _with the same commands_
        - when any of your `buildMachines` has a dependency that another builder needs, it will be transfered automatically
    - [buildLayeredImage](https://ryantm.github.io/nixpkgs/builders/images/dockertools/#ssec-pkgs-dockerTools-buildLayeredImage)
        - build OCI (Docker) images where each store path automatically gets its own layer, so pushing an updated image to a registry will only transfer the changed layers. This is possible with normal Docker, but _much_ simpler with nix.
    - [nix-shell -p](https://nix.dev/tutorials/first-steps/ad-hoc-shell-environments#ad-hoc-envs)
        - opens a temporary shell with whatever package you want available
        - need some advice? `nix-shell -p fortune cowsay` to open the shell, then run `fortune | cowsay`.
    - [nix-index](https://github.com/nix-community/nix-index) finds files in nixpkgs
        - `nix-locate libX11` will tell you which packages have a file matching the name `libX11`. This is very useful when running non-nix packaged software and missing a library.
    - [comma](https://github.com/nix-community/comma)
        - wraps `nix-locate` and  which lets you run `, fortune | , cowsay` on a system _that has neither installed_

### **[direnv](https://github.com/direnv/direnv)**
- evaluates `.direnv` files automatically when you enter directories containing them
- good for setting environment variables
- **extremely powerful** when combined with `use flake` to source a `devShell` from a `flake.nix`
    - you can think of it like installing software by changing directories
        - its like `virtualenv`, but for all software
    - with the automatically generated `flake.lock`, you can ensure that all `devShell` users get the same software
    - to convert a project to a direnv enabled flake:
        - `nix flake new -t github:nix-community/nix-direnv .`  ([source](https://github.com/nix-community/nix-direnv/tree/master/templates/flake))

### [nh](https://github.com/nix-community/nh)
- a really pretty coat of paint for the `nix` CLI

### [Kitty](https://github.com/kovidgoyal/kitty)
- IMO the best terminal 
- [best in class performance](https://sw.kovidgoyal.net/kitty/performance/)
  - [foot](https://codeberg.org/dnkl/foot) is a serious performance contender, but not quite as featureful (yet?)
  - [WezTerm](https://wezterm.org/index.html) is good on Windows
- [kittens](https://sw.kovidgoyal.net/kitty/kittens_intro/) hold the functionality that sets kitty apart
  - <kbd>ctrl + shift + u</kbd> to start [unicode input](https://sw.kovidgoyal.net/kitty/kittens/unicode_input/)
  - duplicate your environment remotely with the [ssh kitten](https://sw.kovidgoyal.net/kitty/kittens/ssh/)
      - `kitten transfer $REMOTE_PATH $LOCAL_PATH` during an `ssh` session to painlessly transfer files, also supports `--upload`
  - [hints](https://sw.kovidgoyal.net/kitty/kittens/hints/) let you ergonomically open URLs or select file paths from the keyboard
  - [graphics-protocol](https://sw.kovidgoyal.net/kitty/graphics-protocol/), which renders images inline in your terminal
- shout-out to [Kovid Goyal](https://github.com/kovidgoyal), who also maintains [Calibre](https://github.com/kovidgoyal/calibre) and [his personal custom browser](https://github.com/kovidgoyal/vise) 🤯

### [Gnome](https://os.gnome.org/)
- gnome is a better macOS
- the default software suite is lightweight, but covers everything you would want to do with a laptop
- as a former tiling window manager user, <kbd>Alt + Tab</kbd> and maximized windows are good enough
- notable extensions
    - [paperwm](https://github.com/paperwm/PaperWM)
        - scrollable tiling window management for gnome
            - your desktop is infinitely wide, expanding as you add windows
        - inspired the more popular [niri](https://github.com/niri-wm/niri)
    - [run or raise](https://github.com/CZ-NIC/run-or-raise)
        - > If the program already runs it will get the focus, else we launch it.
        - like alt-tab, but each applicaton gets a fixed shortcut
    - [GSConnect](https://github.com/GSConnect/gnome-shell-extension-gsconnect)
        - lets you control Gnome from your phone and vice versa
            - get a Gnome notification if your phone battery is low
            - use your phone's touchscreen as a trackpad to control Gnome

### [Neovim](https://neovim.io/)
- TODO (too much to say)
- notable extensions
    - flash/sneak/jetpack
    - whichkey
    - conform
    - treesitter
    - mini.nvim
    - oil.nvim
    - fzf-lua
    - DAP

### [Kanata](https://github.com/jtroo/kanata)
- software keyboard remapping
- I like customizing my input devices
    - I was previously into custom keyboards, but I have come to prefer software remapping
        - remapping your laptop's keyboard is **way** more portable than bringing an external keyboard

- my preferred collection of mods
    - Caps lock -> ESC
        - you press ESC infinitely more than you type in allcaps
    - home row mods
        - when **tapping** keys (aka normal typing), output the normal characters
            - `f` -> `f`
            - `d` -> `d`
        - when **holding** any home row key, treat that key as a modifier
            - `a` -> `WIN`
            - `s` -> `ALT` 
            - `d` -> `CTRL` 
            - `f` -> `SHIFT` 
            - these are mirrored by the other home row keys, so `j` -> **tap:** `j` / **hold:** `SHIFT`
        - these are difficult to get used to
            - if you give them a try, try to pick your fingers up as quickly as you put them down
    - wide mod
        - Instead of there being two spaces between the home row positions, there are three.
        - ![01YR611](https://github.com/user-attachments/assets/594913f8-9ead-4d57-a69a-4bb1b1509a7e)
    - colemak
        - DH
    - layers/arrow keys on homerow

### [Librewolf](https://librewolf.net/)
- Firefox is cool, but it has some downsides
    - upsides
        - [vertical tabs](https://www.firefox.com/en-US/firefox/136.0/releasenotes/#note-790699)
        - [custom search engines](https://support.mozilla.org/en-US/kb/add-or-remove-search-engine-firefox#w_add-a-custom-search-engine-in-settings) are awesome
            - I search `https://search.nixos.org/packages?query=%s` with `@n` all the time
    - downsides
        - ["Firefox deletes promise to never sell personal data, asks users not to panic"](https://arstechnica.com/tech-policy/2025/02/firefox-deletes-promise-to-never-sell-personal-data-asks-users-not-to-panic/)
        - Pocket isn't very good
- LibreWolf applies patches to Firefox to fix those issues
    - installs uBlock Origin
    - removes all advertising and sponsored content
    - removes Pocket at compile time
    - deletes cookies and data for all websites not on the allowlist
    - and [much more](https://librewolf.net/docs/features/)
- notable extensions
    - [LinkHints](https://lydell.github.io/LinkHints/)
        - this lets you select links/inputs using only your keyboard
    - Bitwarden

### [fish shell](https://fishshell.com/)
- I use the default settings. This is the highest praise I can give

### [zoxide](https://github.com/ajeetdsouza/zoxide)
- `cd` with fuzzy matching sorted by frecency

### [Lazygit](https://github.com/jesseduffield/lazygit)
- `git` TUI
- you can hit <kbd>?</kbd> at any time to see what keys you can press next, letting you discover the UI incrementally.
- This lets Lazygit get away with a pretty complicated modal interface that is simultaniously a joy to explore.

### [atuin](https://github.com/atuinsh/atuin)
- shell history management
    - fuzzy <kbd>CTRL + R</kbd> that can filter by host/directory/session
    - painless history sync across machines

### [starship](https://github.com/starship/starship)
- fast shell-agnostic prompt

### [atlas](https://atlasgo.io/)
- declarative DB schema management
    1. update a _single file_ `schema.sql` that holds your schema definition
    1. run `atlas` to compare that file the current DB schema
    1. approve the generated migrations
        - if they are incorrect, edit `schema.sql` until they look good
- useful for AI workflows, simply put `schema.sql` in your context

### [Podman](https://podman.io/)
- `podman` is a drop in replacement for the `docker` CLI that fixes:
    - adding a user to the `"docker"` group is equivalent to [making it root](https://github.com/moby/moby/issues/9976)
    - the docker daemon is a single point of failure

### [fzf](https://github.com/junegunn/fzf)
- fuzzy search provider
- I either use `fzf` directly or fuzzy search on every input I can

### [btop](https://github.com/aristocratos/btop)
- system monitoring at a glance
- high information density 
- discoverable UI/shortcuts

### [ghq](https://github.com/x-motemen/ghq)
- an opinionated way to organize your local git repos
- if you have lots of repos checked out across lots of computers, let `ghq` remember where everything goes

### d2
- diagramming

## services

### [Kagi](https://kagi.com/)
- a paid search engine
- if its free, you are the product

### [Fastmail](https://www.fastmail.com/)
- custom domain email management
- supports some cool features
    - anonymous [masked emails](https://www.fastmail.com/features/masked-email/)
    - wildcard delivery
        - you can use `$SERVICE_NAME@$YOUR_DOMAIN` for every `$SERVICE` and they all go to one inbox

### [Tailscale](https://tailscale.com/)
- mesh VPN that makes networking between machines a breeze
- built with [wireguard](https://www.wireguard.com/) 
> self hosted with [Headscale](https://headscale.net/stable/)

### [NextDNS](https://nextdns.io/)
- DNS level ad blocking
- with Tailscale, you can run a tailnet wide adblock
    - its like a [pi-hole](https://pi-hole.net/) but at the VPN level, meaning you can use it on any network

### [Bitwarden](https://bitwarden.com/)
- password manager
- trivializes sharing passwords with your friends and loved ones, and you can self host it if you want
- the browser extension is well thought out and always acts how one would want/expect
- open source!
> [self host-able](https://bitwarden.com/help/self-host-bitwarden/)

### [WakaTime](https://wakatime.com/)
- tracks coding time automatically by recording edits per file
- very useful if you need to track billing
> self hosted with [wakapi](https://github.com/muety/wakapi)


## hardware

### ThinkPad X1 Carbon (6th)

- my daily driver since 2018
- very light, makes a macbook feel like a brick
- great linux support
- a pretty darn good keyboard
  - the speaker/mic mute buttons have lights
  - a dedicated screenshot button
  - wide delete key right above backspace

### [Jelly Star](https://www.unihertz.com/products/jelly-star)
- a cellphone as big as a credit card
- thicker than most smartphones, but it has a great heft
- sadly, 4G only phones are supported by fewer carriers each year, so it wont last too much longer

### [nwm ONE](https://us.nwm.global/products/one)
- more like on ear speakers than headphones
- light
- the open design gives them the opposite of noise cancellation
    - however, they can be worn forever without making your ears stuffy
    - you have more situational awareness
- they don't leak nearly as much noise as you would expect

## stack

- can be managed by a single developer
- inexpensive to get started
- scales well

### Go
- my preferred language for web applications
    - simple syntax
    - straightforward type system
        - static typing is great, but IMO mostly as a form of documentation
        - a good middleground between dynamic/duck typing and strong type systems like TypeScript or Rust
    - errors as values
        - as controversial it is, I have grown to love the readability of `if err := nil`
            - you will always spend more time reading code than writing it
    - easy concurrency with goroutines
        - `sync` provides really nice concurrency primitives
        - easy cancellation with `context`
    - great standard library
        - you can build real software using just the stdlib
            - `net/http`
            - `database/sql`
            - `flag`
        - very readable if you want to peek under the hood
    - directives
        - comments that do stuff at compile time
        - [embed](https://pkg.go.dev/embed) files into the compiled program
            ```go
            //go:embed hello.txt
            var s string
            ```
    - testing
        - fuzzy
        - benchmarking
- profiling tools
    - delve for debugging
    - pprof for sampled profiling

### [fly.io](https://fly.io/)
- if it runs in an OCI container (Docker/Podman), you can run it on Fly in [10 minutes](https://fly.io/speedrun/)
- it converts those containers to [Firecracker Virtual Machines](https://github.com/firecracker-microvm/firecracker) which boot almost instantly, meaning you can easily scale to 0
- any use under 5$ a month is free
- easily set up automatic staging previews per PR with [fly-pr-review-apps](https://github.com/superfly/fly-pr-review-apps)

### [Tigris](https://www.tigrisdata.com/)
- object storage
- zero egress fees
- low global latency
- [bucket snapshots](https://www.tigrisdata.com/blog/fork-buckets-like-code/)
- Fly integration

### [sqlite](https://sqlite.org/)
- my favorite RDB
- embed it directly in your application to remove network latency
- turn on the [write-ahead log](https://sqlite.org/wal.html)
    - >  Reading and writing can proceed concurrently
- back up to object storage with [Litestream](https://litestream.io/)

### [porkbun](https://porkbun.com/)
- domain name registrar
- _prodigiously porky presentation paragon performantly purveys perfectly priced pages_

### [Open Props](https://open-props.style/)
- a curated collection of CSS variables
- gives you the opinionated defaults of Tailwind, but sidesteps the confusing mess that tailwind classes become.
- encourages you to use CSS how it was designed, instead of utility classes

### varnish cache
- in memory cache for frequently visited pages

### duckdb
- analytics db
