# stoww - A Wrapper for GNU Stow

**stoww** is a simple shell script that wraps [GNU
Stow](https://www.gnu.org/software/stow/) to provide additional features for
managing dotfiles or software packages. It handles pre/post-installation and
removal scripts, manages a `.stowed` flag file, and ensures certain files are
not stowed.

## Features

- **Pre/Post Scripts**: Automatically runs `stow-preinst`, `stow-postinst`,
  `stow-prerm`, and `stow-postrm` scripts (or their hidden counterparts
  `.stow-preinst`, `.stow-postinst`, etc.) if they exist and are executable.
- **Conflict Detection**: Reports an error if both hidden and non-hidden
  versions of a script are present in the package directory.
- **Flag File Management**: Creates a `.stowed` file in the package directory
  when the package is stowed and deletes it when unstowed.
- **Ignore Patterns**: Ensures that special scripts and any files matching
  `*.stow-*` or `stow-*` are not stowed.
- **Full GNU Stow Compatibility**: Supports all GNU Stow options by passing
  them directly to the `stow` command.

## Installation

1. **Clone the Repository**:

   ```sh
   git clone https://github.com/jpasquier/stoww.git
   ```

2. **Make the Script Executable**:

   ```sh
   cd stoww
   chmod +x stoww
   ```

3. **Move the Script to a Directory in Your PATH**:

   ```sh
   sudo mv stoww /usr/local/bin/
   ```

4. **(Optional) Create an Alias**:

   Since `stoww` is a complete wrapper for GNU Stow, you might find it
   convenient to alias it:

   ```sh
   alias stow=stoww
   ```

   Add the above line to your shell's configuration file (e.g., `.bashrc`,
   `.zshrc`) to make it permanent.

*Note:* It is also possible to [build a Debian
package](#building-the-debian-package)

## Usage

```sh
stoww [OPTIONS] PACKAGE...
```

### Options

- `-S`, `--stow`      : Stow the package(s).
- `-D`, `--delete`    : Unstow the package(s).
- `-R`, `--restow`    : Restow the package(s).
- `-h`, `--help`      : Display help information.

All other options are passed directly to GNU Stow.

### Examples

- **Stow a Package**:

  ```sh
  stoww -S mypackage
  ```

- **Unstow a Package**:

  ```sh
  stoww -D mypackage
  ```

- **Restow a Package**:

  ```sh
  stoww -R mypackage
  ```

- **Specify Target Directory**:

  ```sh
  stoww -t /usr/local -S mypackage
  ```

## Important Notes

- **Executable Scripts**: Ensure that your `stow-*` or `.stow-*` scripts are
  executable. You can make them executable with:

  ```sh
  chmod +x stow-preinst stow-postinst stow-prerm stow-postrm
  chmod +x .stow-preinst .stow-postinst .stow-prerm .stow-postrm
  ```

  If these scripts are not executable, `stoww` will not be able to run them.

- **Hidden vs. Non-Hidden Scripts**:
  - You can choose to use either hidden (`.stow-*`) or non-hidden (`stow-*`)
    scripts.
  - **Do Not Use Both**: If both versions of a script are present in the
    package directory, `stoww` will report an error and exit. Please ensure
    only one version is used per script type.

- **Idempotence:** Ensure that your `stow-*` (or `.stow-*`) scripts are
  idempotent. This means they should be safe to run multiple times without
  causing unintended side effects or errors.
  - *Repeated Execution*: The `stoww` command may invoke these scripts every
    time it is run, especially during operations like restowing or updating
    packages.
  - *Preventing Issues*: Non-idempotent scripts could, for example, add
    duplicate entries to configuration files, create conflicting symbolic
    links, or modify system state in ways that lead to errors when the script
    is executed more than once.
  - *Check Before Action*: Before performing an operation (like adding a line
    to a file), check if it has already been done.
  - *Use Conditional Logic*: Employ `if` statements to ensure actions are
    only taken when necessary.
  - *Clean Up Safely*: When removing or modifying files, ensure that they
    exist and are in the expected state.

- **Ignored Files**: The script automatically ignores the following files to
  prevent them from being stowed:
  - `.stowed`
  - Any files matching `*.stow-*` (e.g., `.stow-preinst`)
  - Any files matching `stow-*` (e.g., `stow-postinst`)

## How It Works

1. **Stowing (`-S`, `--stow`)**:

   - Runs `stow-preinst` or `.stow-preinst` if it exists and is executable.
   - Reports an error if both versions exist.
   - Creates the `.stowed` flag file in the package directory.
   - Executes `stow` with the specified options and ignore patterns.
   - Runs `stow-postinst` or `.stow-postinst` if it exists and is executable.
   - Reports an error if both versions exist.

2. **Unstowing (`-D`, `--delete`)**:

   - Runs `stow-prerm` or `.stow-prerm` if it exists and is executable.
   - Reports an error if both versions exist.
   - Removes the `.stowed` flag file from the package directory.
   - Executes `stow -D` with the specified options and ignore patterns.
   - Runs `stow-postrm` or `.stow-postrm` if it exists and is executable.
   - Reports an error if both versions exist.

3. **Restowing (`-R`, `--restow`)**:

   - Runs `stow-prerm` or `.stow-prerm` if it exists and is executable.
   - Reports an error if both versions exist.
   - Removes the `.stowed` flag file from the package directory.
   - Executes `stow -R` with the specified options and ignore patterns.
   - Runs `stow-postinst` or `.stow-postinst` if it exists and is executable.
   - Reports an error if both versions exist.
   - Recreates the `.stowed` flag file.

### Important Note on `stow-*` Scripts



## Building the Debian Package

You can build a Debian package for `stoww` using the provided
`build-stoww-deb` script.

### Prerequisites

Ensure the following tools are installed on your system:

- `git`
- `fakeroot`
- `dpkg-deb`

### Usage

1. **Clone the Repository** (if you haven't already):

   ```sh
   git clone https://github.com/yourusername/stoww.git
   cd stoww
   ```

2. **Make the Build Script Executable**:

   ```sh
   chmod +x build-stoww-deb
   ```

3. **Build the Package from GitHub Repository**:

   This is the default behavior. The script will clone the latest code from
   GitHub and build the package.

   ```sh
   ./build-stoww-deb
   ```

4. **Build the Package from Local Repository**:

   If you have made local changes and wish to build the package from your
   current working directory, use the `--local` option.

   ```sh
   ./build-stoww-deb --local
   ```

5. **Find the Generated Package**:

   The `.deb` package will be located in your current directory.

   ```sh
   ls *.deb
   ```

### Help

For usage information:

```sh
./build-stoww-deb --help
```

### Notes

- **Building from Local Repository**:
  - Ensure you are in the root directory of the `stoww` Git repository.
  - The script will use the current state of your local code, including any
    uncommitted changes.
  - The version number in the package will reflect your local Git commit count
    and hash.

- **Permissions**:
  - The script uses `fakeroot` to build the package without requiring root
    privileges.

- **Cleanup**:
  - Temporary build files are cleaned up automatically after the script
    finishes.

## Zsh Completion for stoww

A completion function for `stoww`, adapted from the completion script for
`stow`, is included in the repository. To enable it, follow these steps:

1. **Create a Custom Completion Directory**:

   ```sh
   mkdir -p ~/.zsh/completion
   ```

2. **Add the Directory to Your `$fpath`**:

   Add the following line to your `~/.zshrc` before calling `compinit`:

   ```zsh
   fpath=(~/.zsh/completion $fpath)
   ```

3. **Copy the `_stoww` Completion Function**:

   ```sh
   cp  zsh_completion ~/.zsh/completion/_stoww
   ```

4. **Initialize the Completion System**:

   In your `~/.zshrc`, ensure you have:

   ```zsh
   autoload -Uz compinit
   compinit
   ```

6. **Reload Zsh Configuration**:

   ```sh
   rm -f ~/.zcompdump
   source ~/.zshrc
   ```

7. **Test the Completion**:

   Type `stoww` followed by `[Tab]` to see if completions are suggested.

### Notes

- **Ensure `compinit` Is Called**: The completion system must be initialized
  with `compinit` for completions to work.
- **Avoid Caching Issues**: If completions don't seem to update, try deleting
  the `.zcompdump` file:

  ```sh rm -f ~/.zcompdump compinit ```

- **No Need for Root Permissions**: Placing the completion script in your home
  directory avoids the need for `sudo` or root access.

## Limitations

While `stoww` provides additional features over GNU Stow, it remains a simple
wrapper script. For more complex package management needs, consider using
dedicated package managers or configuration management tools.

But hey, sometimes simple is better! This script aims to be a straightforward
solution for straightforward needs.

## Contributing

Contributions are welcome! Feel free to open issues or submit pull requests.

## License

This project is licensed under the [MIT License](LICENSE).
