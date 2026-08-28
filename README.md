# stoww

`stoww` is a small wrapper around [GNU Stow](https://www.gnu.org/software/stow/)
that runs scripts before and after stowing or unstowing a package.

## Installation

GNU Stow is required. Copy `stoww` somewhere in your `PATH`:

```sh
sudo install -m 755 stoww /usr/local/bin/stoww
```

## Usage

```text
stoww [OPTIONS] PACKAGE...
```

```sh
stoww package             # stow
stoww -D package          # unstow
stoww -R package          # restow
```

`-S`/`--stow`, `-D`/`--delete`, and `-R`/`--restow` select the operation. Other
options are passed to Stow.

Each package must be an existing directory. Operations stop immediately if a
script, file operation, or Stow exits with an error.

## Scripts

Executable scripts in the package directory are run in this order:

| Operation | Before Stow | After Stow |
| --- | --- | --- |
| Stow | `stow-preinst` | `stow-postinst` |
| Unstow | `stow-prerm` | `stow-postrm` |
| Restow | `stow-prerm` | `stow-postinst` |

Scripts may instead use hidden names such as `.stow-preinst`. The hidden and
non-hidden form of the same script cannot both be present. Scripts should be
idempotent.

The scripts and the `.stowed` flag file are ignored by Stow. The flag is
created when stowing and removed when unstowing.

## Zsh completion

Copy `zsh_completion` to a directory in your Zsh `fpath` as `_stoww`, then
initialize completion with `compinit`.

## Debian package

From the repository root, install `git`, `fakeroot`, and `dpkg-deb`, then run:

```sh
./build-stoww-deb
```

The script builds from the current working tree, including uncommitted changes,
and writes the `.deb` file to the repository root.
