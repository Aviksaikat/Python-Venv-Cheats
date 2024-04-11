# Python Venv Cheats

Repo consists of personal notes of using different python version management systems.

## Initialise

| Tool    | Command                                    |
|---------|--------------------------------------------|
| pdm     | `mkdir my-project && cd my-project && pdm init` |
| hatch   | `hatch new my-project`                     |
| poetry  | `poetry new my-project`                    |


## Dependencies

<details><summary>Pdm</summary>

```sh
pdm add requests   # add requests
pdm add requests==2.25.1   # add requests with version constraint
pdm add requests[socks]   # add requests with extra dependency
pdm add "flask>=1.0" flask-sqlalchemy   # add multiple dependencies with different specifiers

# Local dependencies
pdm add ./sub-package
pdm add ./first-1.0.0-py2.py3-none-any.whl
```

##### Add development only dependencies

```sh
pdm add -dG test pytest
```

- This will create the following

```toml
[tool.pdm.dev-dependencies]
test = ["pytest"]
```

- You can specify more 

```toml
[tool.pdm.dev-dependencies]
lint = [
    "flake8",
    "black"
]
test = ["pytest", "pytest-cov"]
doc = ["mkdocs"]
```


##### Update dependencies

```sh
pdm update

# specific
pdm update requests

# comma-separated list
pdm update -G "security,http"

# Update all default + dev-dependencies
pdm update -d
# Update a package in the specified group of dev-dependencies
pdm update -dG test pytest
```

##### Remove dependencies

```sh
# Remove requests from the default dependencies
pdm remove requests
# Remove h11 from the 'web' group of optional-dependencies
pdm remove -G web h11
# Remove pytest-cov from the `test` group of dev-dependencies
pdm remove -dG test pytest-cov
```

- More info [here](https://pdm-project.org/latest/usage/dependency/)

</details>


<details><summary>Hatch</summary>
Installing dependencies in `hatch` is a bit different. You have to specify the dependencies in `pyproject.toml` or in `hatch.toml` file and when you `hatch shell` or `hatch run` the dependencies will automatically install.

###### Examples
You can select which environment to enter or run commands in by using the `-e/--env` root option or by setting the `HATCH_ENV`environment variable.

The run command allows for more explicit selection by prepending `<ENV_NAME>`: to commands. For example, if you had the following configuration:

```toml
# pyproject.toml
[tool.hatch.envs.docs]
dependencies = [
  "mkdocs"
]
[tool.hatch.envs.docs.scripts]
build = "mkdocs build --clean --strict"
serve = "mkdocs serve --dev-addr localhost:8000"

# hatch.toml
[envs.docs]
dependencies = [
  "mkdocs"
]
[envs.docs.scripts]
build = "mkdocs build --clean --strict"
serve = "mkdocs serve --dev-addr localhost:8000"
```
you could then serve your documentation by running:

```sh
hatch run docs:serve
```
- More info [here](https://hatch.pypa.io/1.9/environment/)

</details>


<details><summary>Poetry</summary>
You can simply do

```sh
poetry add requests # to install requrests
```

###### Examples
- If you specify new dependecies in `pyproject.toml` file you can do `poetry install` to install them.

```toml
[tool.poetry.dependencies]  # main dependency group
httpx = "*"
pendulum = "*"
[tool.poetry.group.test.dependencies]
pytest = "^6.0.0"
pytest-mock = "*"
```

- Optional groups can be installed in addition to the default dependencies by using the --with option of the install command.
```toml
[tool.poetry.group.docs]
optional = true

[tool.poetry.group.docs.dependencies]
mkdocs = "*"
```

```sh
poetry install --with docs
```


- More info [here](https://python-poetry.org/docs/managing-dependencies/)

</details>


## Shell

| Tool    | Command                                    |
|---------|--------------------------------------------|
| pdm     | `pdm shell` |
| hatch   | `hatch shell`                     |
| poetry  | `poetry shell`                    |


##### Active venv in Pdm

=== "bash/csh/zsh"

    ``` sh
    eval $(pdm venv activate for-test)
    ```

=== "fish"

    ```sh
    eval (pdm venv activate for-test)
    ```

=== "powershell"

    ```ps1
    PS1> Invoke-Expression (pdm venv activate for-test)
    ```


**N.B.** In order to do `pdm shell` you have to set the following in your shell.

- Add a shell function to activate the virtualenv, here is an example of BASH function that also works on ZSH & BASH:

```sh
pdm() {
  local command=$1

  if [[ "$command" == "shell" ]]; then
      eval $(pdm venv activate)
  else
      command pdm $@
  fi
}
```
Copy and paste this function to your `~/.bashrc` or `~/.zshrc` file and restart your shell.

- For fish shell you can put the following into your `~/fish/config.fish` or in `~/.config/fish/config.fish`

```sh
  function pdm
      set cmd $argv[1]

      if test "$cmd" = "shell"
          eval (pdm venv activate)
      else
          command pdm $argv
      end
  end
```


## Example pyproject.toml

**N.B** They are not the ideal projects and I don't recommend anyone to strictly follow them. They just get the job done :)


| Tool    | Link                                    |
|---------|--------------------------------------------|
| pdm     | [swarm-bee-py](https://github.com/alienrobotninja/bee-py/blob/main/pyproject.toml) |
| hatch   | [bmt_py](https://github.com/alienrobotninja/bee-py/blob/main/pyproject.toml)                     |
| poetry  | [Delegation](https://github.com/Aviksaikat/ApeWorX-work/blob/main/CTFs/Ethernaut/Delegation_DONE/pyproject.toml)                    |