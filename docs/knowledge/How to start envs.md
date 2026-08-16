### Development
For development, we need to create a virtual environment. We are using UV as the package and project manager
(https://docs.astral.sh/uv/). For installation of UV, look at https://docs.astral.sh/uv/getting-started/installation/.

The `pyproject.toml` defines how to build the virtual environment. Typically, use `uv venv` from the root directory of
this project (same folder as where `pyproject.toml` is located) to create an empty virtual environment named `.venv`
(this folder will be created in the root folder). Make sure that the `.python-version` file is present in your root
folder since this will be used when initializing the empty virtual environment.

Don't forget to activate the virtual environment before building the application with the dependencies as specified in
the `pyproject.toml` file.

Use `uv pip list` (after activation) to verify that the virtual environment is still empty (it should also mention the
python version being used in the virtual environment).

The command `uv sync` will build the virtual environment as well as a lock file call `uv.lock` locally, which specifies
the exact dependencies being used (this file should typically be under version control as well). Note that by using
`uv sync --no-dev` the development dependencies (such as pytest) will not be installed.

To build a virtual environment when deploying use the command `uv sync --no-sources`. This will ensure that AAP is being
fetched from the Achmea package index on Nexus instead of looking locally for the sources (as is the case for
developers).

The `uv pip list` command will list all the installed dependencies.
