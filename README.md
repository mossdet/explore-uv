## Initialize project with name project-name
```sh
	uv init project-name
```

## Add packages, which also creates the environment
```sh
	uv add scikit-learn xgboost
```

## Remove dependency from environment
```sh
	uv add scikit-learn xgboost
```

## Run python script directly
```sh
	uv run hello.py
```


# Creating a virtual environment
uv supports creating virtual environments, e.g., to create a virtual environment at .venv:
```sh
uv venv
```

A specific name or path can be specified, e.g., to create a virtual environment at my-name:
```sh
uv venv my-name
```

A Python version can be requested, e.g., to create a virtual environment with Python 3.11:
```sh
uv venv --python 3.11
```
Note this requires the requested Python version to be available on the system. However, if unavailable, uv will download Python for you.

## Activate and deactivate environment
```sh
source .venv/bin/activate
```
```sh
deactivate
```

# Managing Python Versions in UV
## List existing python versions
```sh
	uv python list --only-installed
```

## Changing Python versions for the current project 
- You can switch Python versions for your current UV project at any point as long as the new version satisfies the specifications in your pyproject.toml file.
- The python version is changed in the .python-version file
- If the requested version isn't found, UV downloads and installs it inside the /Users/username/.local/share/uv/python path.
- UV also creates a new virtual environment inside the project directory, replacing the old one. 
This new environment doesn't have the dependencies listed in your pyproject.toml file, so you have to install them with the following command

```sh
uv pip install -e .
```

## Avoid permission denied warnings
- Use sudo
- Even better solution would be to change the ownership of the UV home directory to the user:

```sh
sudo chown -R $USER ~/.local/share/uv
```

# UV Tools
Some Python packages are exposed as command-line tools like black for code formatting, flake8 for linting, pytest for testing, mypy for type checking, etc. UV provides two special interfaces to manage these packages:
1. Using uv tool run:
```sh
uv tool run black hello.py
```

2. Using the shorter and more convenient uvx command:
```sh
uvx black hello.py
```

When these commands are run, UV creates a temporary virtual environment in its cache. The requested tool is installed and run from there. In other words, you can use command-line tools without installing them in the project's virtual environment, resulting in faster execution and cleaner project dependencies.

### Key points about the tool run interfaces:
- Works with any Python package that provides command-line tools like flake8, mypy, black or pytest
- Cached environments are automatically cleaned up when clearing UV's cache
- New cached environments are created on-demand when needed
- Perfect for occasional use of development tools


# Lock files in UV
Lock files (uv.lock) are an essential part of dependency management in UV. When you run uv add commands to install dependencies, UV automatically generates and updates a uv.lock file. This lock file serves several critical purposes:

- It records the exact versions of all dependencies and their sub-dependencies that were installed.
- It ensures reproducible builds by "locking" dependency versions across different environments.
- It helps prevent "dependency hell" by maintaining consistent package versions.
- It speeds up installations since UV can use the locked versions instead of resolving dependencies again.

UV manages the lock file automatically - you don't need to manually edit it. The lock file should be committed to version control to ensure all developers use the same dependency versions.


# The Difference Between Lock Files and requirements.txt
While both lock files and requirements.txt serve to track dependencies, they have distinct purposes and use cases. Lock files contain detailed information about exact package versions and their complete dependency tree, ensuring consistent environments across development. Requirements.txt files are simpler, typically listing only direct dependencies, and are widely supported across Python tools.

Lock files are essential for development to maintain reproducible builds and prevent dependency conflicts. Requirements.txt files are better suited for deployment scenarios or when sharing code with users who may not use UV. They're also necessary for compatibility with tools and services that don't support UV's lock file format.

You can maintain both files by using UV's lock file for development while generating a requirements.txt for deployment. To generate a requirements.txt from a UV lock file, use the following command:

```sh
uv export -o requirements.txt
```

This creates the text file with pinned versions based on your lock file, making it easy to share your project's dependencies in a standard format while still benefiting from UV's advanced dependency management during development.


# Advanced Dependency Management With UV
In this section, we will explore more sophisticated methods to manage dependencies in UV. We will learn how to update dependencies, make them optional, or make them part of a dependency group.

## Updating dependencies
In long-term projects, it is common to update the packages you are using to bring the most up-to-date features to the table. Or sometimes, a package you are using introduces breaking changes and you want to ensure that version doesn't get installed accidentally in your environment. The add command can be used again in these and any other scenario where you need to change the constraints or versions of existing dependencies.

1. Installing the latest version of a package:
```sh
uv add requests
```

2. Installing a specific version:
```sh
uv add requests=2.1.2
```

3. Change the bounds of a package's constraints:
```sh
uv add 'requests<3.0.0'
```

4. Make a dependency platform-specific:
```sh
uv add 'requests; sys_platform="linux"'
```

## Adding optional dependencies
Optional dependencies are packages that are not required for the core functionality of your project but may be needed for specific features. For example, Pandas has an excel extra and a plot extra to avoid the installation of Excel parsers and matplotlib unless someone explicitly requires them. Optionals are usually installed with the pip install pandas[plot, excel] syntax. 
With UV, this syntax is slightly different. First, ensure that the core Pandas package is installed:
```sh
uv add pandas
```

Then, add its optional dependencies:
```sh
uv add pandas --optional plot excel
```

## Dependency groups
Dependency groups allow you to organize your dependencies into logical groups, such as development dependencies, test dependencies, or documentation dependencies. This is useful for keeping your production dependencies separate from your development dependencies.
To add a dependency to a specific group, use the --group flag:
```sh
uv add --group group_name package_name
```
Then, users will be able to control which groups to install using the --group, --only-group, and --no-group tags.


# Installing packages from files
Multiple packages can be installed at once from standard file formats.

1. Install from a requirements.txt file:
```sh
uv pip install -r requirements.txt
```

2. Install from a pyproject.toml file:
```sh
uv pip install -r pyproject.toml
```

3. Install from a pyproject.toml file with optional dependencies enabled, e.g., the "foo" extra:
```sh
uv pip install -r pyproject.toml --extra foo
```

4. Install from a pyproject.toml file with all optional dependencies enabled:
```sh
uv pip install -r pyproject.toml --all-extras
```