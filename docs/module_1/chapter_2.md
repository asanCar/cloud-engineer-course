# Chapter 2: Environment, Dependencies & Build Tools

**Estimated Time:** 5 hours

**Goal:** To equip developers with the knowledge and skills to effectively manage project environments, dependencies, package structure, and build processes, ensuring project reproducibility and maintainability.

## 2.1 Virtual Environments

- **Objective:** Understand the importance of isolated environments and how to create them using `venv` (Python).
- **Topics:**
  - **Why Isolation?** Prevent library conflicts between projects.
  - **What is venv?** A tool to create isolated Python environments.
  - **Creating venv:** `python3 -m venv .venv` makes a new environment.
  - **Using venv:** Activate to use its Python/libraries, deactivate to switch back.
  - **Benefits:** Keep projects organized, avoid clashes, ensure reproducibility.
- **Example Snippet (Creating and Using a Virtual Environment):**

    ```bash
    # --- Example: Creating and Using a Virtual Environment ---

    # Create a virtual environment named ".venv" in the current directory
    python3 -m venv .venv

    # Activate the virtual environment (Linux/macOS)
    source .venv/bin/activate

    # (venv) should now appear in your shell prompt

    # Install a package within the virtual environment
    pip install requests

    # Use the installed package
    python -c "import requests; print(requests.get('[https://www.example.com](https://www.example.com)'))"

    # Deactivate the virtual environment
    deactivate

    # The (venv) part of the prompt should disappear
    ```

## 2.2 Dependency Management (pip)

- **Objective:** Learn how to manage project dependencies using `pip`.
- **Topics:**
  - `pip` as Python's package installer.
  - Installing packages (`pip install <package_name>`).
  - Specifying versions (`pip install <package_name>==<version>`, `pip install <package_name>>=<min_version>`, etc.).
  - Upgrading packages (`pip install --upgrade <package_name>`).
  - Uninstalling packages (`pip uninstall <package_name>`).
  - Listing installed packages (`pip list`, `pip show <package_name>`).
  - Generating `requirements.txt` (`pip freeze > requirements.txt`).
  - (Old simple way) Installing from `requirements.txt` (`pip install -r requirements.txt`).
  - Modern project setup with `pyproject.toml`.
- **Example Snippet (Using `pip`):**

    ```bash
    # --- Example: Managing Dependencies with pip ---

    # Install a specific version of Flask
    pip install Flask==2.1.2

    # Upgrade Flask
    pip install --upgrade Flask

    # Create a requirements.txt file
    pip freeze > requirements.txt

    # Install dependencies from requirements.txt
    pip install -r requirements.txt

    # Show information about the installed Flask package
    pip show Flask
    ```

- **Example Snippet (`pyproject.toml`):**

    ```toml
    # --- Example: pyproject.toml ---

    [project]
    name = "my_project"
    version = "0.1.0"
    dependencies = [
        "requests >= 2.28.0",
        "flask == 2.2.3"
    ]

    [build-system]
    # Specifies the tools required to build the project
    requires = ["setuptools>=61.0.0"]
    build-backend = "setuptools.build_meta"
    ```

    ```bash
    # To install dependencies from pyproject.toml (using pip)
    # You still typically use pip, but it leverages the build system info:
    pip install .  # Installs the current project and its dependencies
    ```

## 2.3 Package Structure

- **Objective:** Learn how to structure Python projects for organization and maintainability.
- **Topics:**
  - Basic project layout:
    - `src/`: Where the main application code resides
    - `tests/`: For test code
    - `README.md`: Project documentation
    - `requirements.txt`: Legacy way to list dependencies
    - `pyproject.toml`: Modern file for project metadata and build configuration.
  - **Packages vs. Modules:** A Python module is a single `.py` file; a package is a directory containing module files and an `__init__.py` file.
  - `__init__.py` files (for Python < 3.3, and for defining package-level imports).
  - **Importing Code:** Prefer absolute imports for clarity; use relative imports within packages.
- **Example Snippet (Basic Project Structure):**

    ```
    # --- Example: Basic Project Structure ---
    my_project/
    ├── src/
    │   ├── my_package/
    │   │   ├── __init__.py
    │   │   ├── module1.py
    │   │   └── module2.py
    │   └── main.py
    ├── tests/
    │   └── test_module1.py
    ├── README.md
    └── requirements.txt
    ```

    ```python
    # --- Example: Absolute vs. Relative Imports ---
    # src/my_package/module2.py
    from my_package import module1  # Absolute import (from top-level package)
    from . import module1          # Relative import (from same package)
    ```

## 2.4 Build Processes

- **Objective:** Introduce the concepts of building and packaging Python projects.
- **Topics:**
  - **Why Build and Package?:** To prepare your project for distribution and installation.
  - `setuptools` for building distributions (handled by `pyproject.toml` in modern projects).
  - `pyproject.toml` for package metadata and build configuration.
  - **Building source distributions (`sdist`):** Packages your project's source code, metadata, and installation details into a distributable archive, facilitating installation and handling extension module compilation.
  - **Building wheels (`wheel`):** Creates a pre-built distribution that installs faster and is generally preferred over source distributions.
  - `twine`: A tool to upload your packages to PyPI.
- **Example Snippet (`pyproject.toml`):**

    ```toml
    # --- Example: pyproject.toml ---
    [project]
    name = "my_package"
    version = "0.1.0"
    authors = [
        { name = "Your Name", email = "your.email@example.com" }
    ]
    description = "A sample package"
    readme = "README.md"
    requires-python = ">=3.8"
    classifiers = [
        "Programming Language :: Python :: 3 :: Only",
        "License :: OSI Approved :: MIT License",
        "Operating System :: OS Independent",
    ]
    dependencies = [
        "requests >= 2.28.0",
        "flask == 2.2.3"
    ]

    [project.optional-dependencies]
    dev = [
        "pytest>=7.0",
        "coverage"
    ]

    [project.scripts]
    my-script = "my_package.main:cli"  # If you have a CLI entry point

    [build-system]
    # Specifies the tools required to build the project
    requires = ["setuptools>=61.0.0", "wheel"]
    build-backend = "setuptools.build_meta"
    ```

    ```bash
    # --- Example: Building and Installing with pyproject.toml ---
    # Assuming you have a pyproject.toml configured for setuptools
    python -m build  # Uses the 'build' package to build from pyproject.toml
    # This creates a 'dist' directory with sdist and wheel files

    # To install the wheel (from the 'dist' directory)
    pip install dist/my_package-0.1.0-py3-none-any.whl
    # Or install the sdist
    pip install dist/my_package-0.1.0.tar.gz

    # To install the package in "editable" mode (for development)
    pip install -e .

    # To publish the package to PyPI (requires twine)
    # twine upload dist/*
    ```