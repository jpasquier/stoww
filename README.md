# stoww - A Wrapper for GNU Stow

**stoww** is a simple shell script that wraps [GNU
Stow](https://www.gnu.org/software/stow/) to provide additional features for
managing dotfiles or software packages. It handles pre/post-installation and
removal scripts, and manages a `.stowed` flag file.

## Features

- **Pre/Post Scripts**: Automatically runs `.stow-preinst`, `.stow-postinst`,
  `.stow-prerm`, and `.stow-postrm` scripts if they exist and are executable.
- **Flag File Management**: Creates a `.stowed` file in the package directory
  when the package is stowed and deletes it when unstowed.
- **Ignore Patterns**: Ensures that special scripts and the `.stowed` flag file
  are not stowed.
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

- **Executable Scripts**: Ensure that your `.stow-*` scripts are executable.
  You can make them executable with:

  ```sh
  chmod +x .stow-preinst .stow-postinst .stow-prerm .stow-postrm
  ```

  If these scripts are not executable, `stoww` will not be able to run them.

- **Ignored Files**: The script automatically ignores the following files to
  prevent them from being stowed:

  - `.stowed`
  - `.stow-preinst`
  - `.stow-postinst`
  - `.stow-prerm`
  - `.stow-postrm`

## How It Works

1. **Stowing (`-S`, `--stow`)**:

   - Runs `.stow-preinst` if it exists and is executable.
   - Creates the `.stowed` flag file in the package directory.
   - Executes `stow` with the specified options and ignore patterns.
   - Runs `.stow-postinst` if it exists and is executable.

2. **Unstowing (`-D`, `--delete`)**:

   - Runs `.stow-prerm` if it exists and is executable.
   - Removes the `.stowed` flag file from the package directory.
   - Executes `stow -D` with the specified options and ignore patterns.
   - Runs `.stow-postrm` if it exists and is executable.

3. **Restowing (`-R`, `--restow`)**:

   - Runs `.stow-prerm` if it exists and is executable.
   - Removes the `.stowed` flag file from the package directory.
   - Executes `stow -R` with the specified options and ignore patterns.
   - Runs `.stow-postinst` if it exists and is executable.
   - Recreates the `.stowed` flag file.

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
