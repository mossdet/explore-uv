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
