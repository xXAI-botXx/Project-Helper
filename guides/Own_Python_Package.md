# Own Python Package

[<img align="right" width=150px src="../res/rackete_2.png"></img>](../README.md)

Contents:
- [Motivation](#motivation)
- [Local Package](#local-package)
- [Package Access](#package-access)
- [Pip Package](#pip-package)
- [Real Example (pip-module)](#real-example-pip-module)
    - [Another example](#another-example)
- [Documentation](#documentation)


> If you wonder what is a `Python Module` and what is a `Python Package`? -> Check out [the local package chapter](#local-package).

> click the **rocket** for going **back**


<br><br>

---
### Motivation

There are many good reasons to build a own module/package. Of course you can also just make python files and import and use them like:

```
|--- My_Project
    |--- my_helper.py
|--- my_active_script.py
```

my_active_script.py:
```python

import sys
sys.path += [os.path.join(os.path.dirname(__file__), "My_Project")]
import my_helper
```

not possible is:
```python
from My_Project import my_helper
```

And exporting your lib is also not possible. 

<br><br>

---
### Local Package

Let's first clear the namings. A `module` is  just any python file in python. So a project creates most likely multiple `modules` which contains, variables, classes or functions.<br>
`Packages` are a collection of python `modules` accessible from outside of this specific `package`. `Packages` can be used locally but also be provided via the package-system from python.

For a local package you just need to add a ```__init__.py``` in every folder of your project which contains a python file. These init files controls which content is accessable in the next layer. The most top init controls therefore which methods, variables and other things are accessable from outside your package.

For example:

```text
|--- My_Project
    |--- my_helper.py
|--- my_active_script.py
```

The init file can contain:

```python
from .my_helper import some_function, SomeClass
# or:
from . import my_helper
```

Now you can do:

```python
from My_Project import some_function
# or:
from My_Project import my_helper
# or:
import My_Project as mp
mp.some_function()
```

<br><br>

---
### Package Access

Through the `__init__.py` file, the module controls which content should be vailable through its package import.<br>
The way you import the modules in your package decides the way you access them later on.

If the package is not in the current folder and not in the pythons side-packages folder, then you need to tell python to tell on also another location for the packages:

```python
import sys
sys.path += ["../my_awesome_libs"]
```

Before we look at the different ways to use the __init__.py, we have to understand following:

Package structure:

```text
|--- my_package
    |--- __init__.py
    |--- my_module_1.py
    |--- my_folder
        |--- my_module_2.py
|--- main.py (notice: not part of the package)
```

wrong __init__.py:

```python
import my_module_1
from myfolder import my_module_2
```

Called by `main.py`:

```python
import my_package as mp
```

The problem here is that python will search then my_module_1 and the my_folder in the path where it gets runned, so in this case it would search in the top folder (where my_package folder and main.py are) for these modules.

So you should use the `.` to clearify that the search-path is the current package folder. Example:

```python
from . import my_module_1
from .myfolder import my_module_2
```

<br><br>

So let's look at the different ways to provide your package and how the user can the use it.

<br><br>

**Modular API**

We expect the user to use our package as:

```python
import my_package as mp
```

If our `__init__.py` looks like that:

```python
from . import my_module_1
from .myfolder import my_module_2
```

Then the usage is like:

```python
import my_package as mp

mp.my_module_1.func()
mp.my_module_2.func()
```

<br><br>

If we want to use `my_folder` in the call, we can change the import of our package:

`__init__.py`:

```python
from . import my_module_1
from . import my_folder
```

And you need now to clearify in `my_folder` which modules this folder provides with a `__ini__.py` file inside of this folder:

`__init__.py`:

```python
from . import my_module_2
```

`main.py`:

```python
import my_package as mp

mp.my_module_1.func()
mp.my_folder.my_module_2.func()
```

> In tht way you have full control over how functions should be available. Should they be called via the module name? And also if folders should have been used or not and even renamings are via `as` possible.

Renaming showcase:

`__init__.py`:

```python
from . import my_module_1
from . import my_folder as mf
```

`my_folder > __init__.py`:

```python
from . import my_module_2
```

`main.py`:

```python
import my_package as mp

mp.my_module_1.func()
mp.mf.my_module_2.func()
```

<br><br>

**Flat API**

`__init__.py`:

```python
from .my_module_1 import func  # or maybe *
from . import myfolder
# or 
# from .my_folder.my_module_2 import func
```

`my_folder > __init__.py`:

```python
from . import func  # or maybe *
```

Then the usage is like:

```python
import my_package as mp

mp.func()
mp.my_folder.func()
```


> And of course you can mix flat and modular APIs and decide which Module is available via which call.

<br><br>

Ok but what changes if the import from the user is different?

```python
from . import my_module_1
from . import my_folder as mf
```

`my_folder > __init__.py`:

```python
from . import my_module_2
```

`main.py`:

```python
from my_package import *

my_module_1.func()
mf.my_module_2.func()
```

As you can see, if the processgot understand all types of ways get very straight forward. This example follows the same logic as the examples before. We just import everything from the package directly and therefore everything which is defined in `__init__.py` we can use directly without the package name.


<br><br>

---
### Pip Package

Pip or Python Package Index (PyPI) is the standard package manager from python and does provide you quick access and installation from external code. It allows user to install your code in seconds which is pretty nice.

1. **Right Structure:**

The structure of a module can look like that:
```
my_modul/
├── my_modul/                ← Your Modul (can have multiple subfolders, all with a own init file)
│   ├── __init__.py
│   └── functions.py         ← Example functionality file
├── tests/                    ← Optional: Unit-Tests
│   └── test_functions.py
├── README.md                 ← Description for GitHub and PyPI
├── LICENSE                   ← License (e.g. MIT)
├── pyproject.toml            ← Properties and Dependencies
├── setup.cfg                 ← Metadata
├── setup.py                  ← Setup script for packaging
└── MANIFEST.in               ← To specify additional files to include
```


2. **Write your code:**

Don't miss the init files, [described here](#local-package).

my_module/\_\_init\_\_.py
```python
from .functions import pretty_print
# or:
from . import functions
```

my_modules/functions.py
```python
def pretty_print(txt:str):
    print(txt)
```

3. **Create/Write Additional Files:**

As already told, you need a *pyproject.toml* (optional), MANIFEST for non python files and a *setup.py*. 

Example

setup.py:
```python
from setuptools import setup, find_packages

setup(
    name='myproject',
    version='0.1',
    packages=find_packages(),
    install_requires=[
        # List any dependencies here, e.g. 'numpy', 'requests'
    ],
    test_suite='pytest',
    tests_require=[
        'pytest',
    ],
    long_description=open('README.md').read(),
    long_description_content_type="text/markdown",
    include_package_data=True,  # Ensures files from MANIFEST.in are included
    classifiers=[
        "Programming Language :: Python :: 3",
        "License :: OSI Approved :: MIT License",
        "Operating System :: OS Independent",
    ],
)
```

(not needed)<br>
MANIFEST.in file:
```
include README.md
include LICENSE
recursive-include tests *
```

(not needed)<br>
pyproject.toml:
```toml
[build-system]
requires = ["setuptools", "wheel"]
build-backend = "setuptools.build_meta"
```




4. **Local Build / Testing:**

Open your shell/cmd and naviagte with cd to your project folder. Then build your project:
```bash
python setup.py sdist
```



5. **Uploading:**

Last step is to upload your module on PyPI.

Register yourself on [PyPI](https://pypi.org/). Now you can click on your account and click on Publishing and you will be able to publish a project/release from github. Alternativly you can install twine:

```bash
pip install twine
```


And upload your project:

```bash
twine upload dist/* -u __token__ -p *authentification code
```

But change *authentification code with your real authentification code from PyPI!
 



<br><br>

---
### Real Example (pip-module)

[Gentic Algorithm Project](https://github.com/xXAI-botXx/Genetic-Algorithm/)

Structure:
```
Genetic-Algorithm/
├── Simple_Gentic_Algorithm/
│   ├── __init__.py
│   └── genetic_algorithm.py
├── README.md
├── License.txt
├── MANIFEST    <- auto generated
├── setup.cfg
├── setup.py                      
└── ...                 
```

init:
```python
from Simple_Genetic_Algorithm.genetic_algorithm import GA, get_random
```

> External code can now import: ```from genetic_algorithm import GA, get_random```

setup.py:
```python
from setuptools import setup

# relative links to absolute
with open("./README.md", "r") as f:
    readme = f.read()
readme = readme.replace('src="./logo.jpeg"', 'src="https://github.com/xXAI-botXx/Genetic-Algorithm/raw/v_015/logo.jpeg"')
readme = readme.replace('<a href="./example.ipynb">', '<a href="https://github.com/xXAI-botXx/Genetic-Algorithm/blob/main/example.ipynb">')
readme = readme.replace('<a href="./example_2.ipynb">', '<a href="https://github.com/xXAI-botXx/Genetic-Algorithm/blob/main/example_2.ipynb">')


setup(
  name = 'Simple_Genetic_Algorithm',         # How you named your package folder (MyLib)
  packages = ['Simple_Genetic_Algorithm'],   # Chose the same as "name"
  version = '0.1.9.5',      # Start with a small number and increase it with every change you make
  license='MPL-2.0',        # Chose a license from here: https://help.github.com/articles/licensing-a-repository
  description = 'Genetic Algorithm Framework',   # Give a short description about your library
  long_description = readme,
  long_description_content_type='text/markdown',
  author = 'Tobia Ippolito',                   # Type in your name
  url = 'https://github.com/xXAI-botXx/Genetic-Algorithm',   # Provide either the link to your github or to your website
  download_url = 'https://github.com/xXAI-botXx/Genetic-Algorithm/archive/v_01.tar.gz',    
  keywords = ['Optimization', 'Genetic-Algorithm', 'Hyperparameter-Tuning'],   # Keywords that define your package best
  install_requires=[            # used libraries
          'joblib'
      ],
  classifiers=[
    'Development Status :: 3 - Alpha',      # Chose either "3 - Alpha", "4 - Beta" or "5 - Production/Stable" as the current state of your package
    'Intended Audience :: Developers',      # Define that your audience are developers
    'Topic :: Software Development :: Build Tools',
    'License :: OSI Approved :: Mozilla Public License 2.0 (MPL 2.0)',   # Again, pick a license (https://autopilot-docs.readthedocs.io/en/latest/license_list.html)
    'Programming Language :: Python :: 3.8',
    'Programming Language :: Python :: 3.9',      # Specify which pyhton versions that you want to support
    'Programming Language :: Python :: 3.10',
    'Programming Language :: Python :: 3.11',
    'Programming Language :: Python :: 3.12'
  ],
)
```

setup.cfg:
```cfg
[metadata]
description_file = README.md
license_files = License.txt
```


<br><br>

#### Another example

[Prime Printer](https://github.com/xXAI-botXx/prime_printer)

Structure:
```
prime_printer/
├── prime_printer/
│   ├── __init__.py
│   └── cio_helper.py
├── README.md
├── LICENSE
├── MANIFEST.in
├── setup.cfg
├── setup.py                      
└── ...                 
```

\_\_init\_\_.py:
```python
from .cio_helper import *
```



setup.py:
```python
from setuptools import setup

# relative links to absolute
with open("./README.md", "r", encoding="utf-8") as f:
    readme = f.read()
readme = readme.replace('src="logo.png"', 'src="https://raw.githubusercontent.com/xXAI-botXx/prime_printer/v_01/logo.png"')
readme = readme.replace('src="./res/example_colored_delay_sound_print.gif"', 'src="https://raw.githubusercontent.com/xXAI-botXx/prime_printer/v_01/res/example_colored_delay_sound_print.gif"')
readme = readme.replace('src="./res/example_game_menu.gif"', 'src="https://raw.githubusercontent.com/xXAI-botXx/prime_printer/v_01/res/example_game_menu.gif"')
readme = readme.replace('src="./res/example_input_with_condition.gif"', 'src="https://raw.githubusercontent.com/xXAI-botXx/prime_printer/v_01/res/example_input_with_condition.gif"')
readme = readme.replace('src="./res/example_image_print.gif"', 'src="https://raw.githubusercontent.com/xXAI-botXx/prime_printer/v_01/res/example_image_print.gif"')
readme = readme.replace('src="./res/example_progress_bar_1.gif"', 'src="https://raw.githubusercontent.com/xXAI-botXx/prime_printer/v_01/res/example_progress_bar_1.gif"')
readme = readme.replace('src="./res/example_progress_bar_2.gif"', 'src="https://raw.githubusercontent.com/xXAI-botXx/prime_printer/v_01/res/example_progress_bar_2.gif"')
readme = readme.replace('src="./res/example_progress_bar_3.gif"', 'src="https://raw.githubusercontent.com/xXAI-botXx/prime_printer/v_01/res/example_progress_bar_3.gif"')

setup(
  name = 'prime_printer',         # How you named your package folder (MyLib)
  packages = ['prime_printer'],   # Chose the same as "name"
  include_package_data=True,
  version = '0.0.3',      # Start with a small number and increase it with every change you make
  license='MIT',        # Chose a license from here: https://help.github.com/articles/licensing-a-repository
  description = 'Console I/O Helper - Print Awesome. Make It Prime.',   # Give a short description about your library
  long_description = readme,
  long_description_content_type='text/markdown',
  author = 'Tobia Ippolito',                   # Type in your name
  url = 'https://github.com/xXAI-botXx/prime_printer',   # Provide either the link to your github or to your website
  download_url = 'https://github.com/xXAI-botXx/prime_printer/archive/refs/tags/v_01.zip',    
  keywords = ['Printing', 'Helper'],   # Keywords that define your package best
  install_requires=[            # used libraries
          'climage',
          'pygame'
      ],
  platform=[
    'Windows',
    'Linux'
  ],
  classifiers=[
    'Development Status :: 3 - Alpha',      # Chose either "3 - Alpha", "4 - Beta" or "5 - Production/Stable" as the current state of your package
    'Intended Audience :: Developers',      # Define that your audience are developers
    'Topic :: Software Development :: Build Tools',
    'License :: OSI Approved :: MIT License',   # Again, pick a license (https://autopilot-docs.readthedocs.io/en/latest/license_list.html)
    'Programming Language :: Python :: 3.8',
    'Programming Language :: Python :: 3.9',      # Specify which pyhton versions that you want to support
    'Programming Language :: Python :: 3.10',
    'Programming Language :: Python :: 3.11',
    'Programming Language :: Python :: 3.12',
  ],
)
```


setup.cfg:
```cfg
[metadata]
description_file = README.md
license_files = LICENSE
```


MANIFEST.in:
```cfg
include prime_printer/sounds/*
include logo.png
include res/*
```


<br><br>

---

### Documentation

If you created your own amazing python package, you also want that everybody can unleash the power of your code and therefore a need for a good documentation is needed. While Sphinx is nice, it is a bit more impractical to host a website for your documentation. A much simpler and still comfortable way is to include your documentation into your README/Markdown ecosystem on GitHub or GitLab.<br>
For that you need to document your code with PyDocs, the tripple quotes. The tripple quotes can be put on classes, functions and the beginning of files and then they are the description and can be used for building a documentation -> they are saved into the `__doc__` attribute and then can be accessed via this doc attribute.

```python
"""
This is my awesome python file with a really amazing functionality.
- Awesome Function 1
- ...
"""

def calculate_area(length: float, width: float, unit: str = "cm") -> str:
    """
    Calculate the area of a rectangle and return a formatted string.

    Parameters:
    -----------
    length : float
        The length of the rectangle.
    width : float
        The width of the rectangle.
    unit : str, optional
        The unit of measurement (default is 'cm').

    Returns:
    --------
    str
        A string describing the area with the specified unit.
    
    Example:
    --------
    >>> calculate_area(5, 3)
    'Area: 15 cm²'
    """
    area = length * width
    return f"Area: {area} {unit}²"

```

There are different styles of PyDoc Strings:

- **The Official Minimal Docstring**
    ```python
    def add(a, b):
        """Return the sum of a and b."""
        return a + b
    ```
    - One-line docstring describing the function.
    - Enough for simple functions.
- **Multi-line Docstring**
    ```python
    def calculate_area(length, width):
        """
        Calculate the area of a rectangle.

        Parameters:
            length (float): Length of the rectangle.
            width (float): Width of the rectangle.

        Returns:
            float: Area of the rectangle.
        """
        return length * width
    ```
    - Includes parameters, types, and return value.
    - Easy to generate automatic documentation with tools like Sphinx.
- **Google Style**
    ```python
    def calculate_area(length, width):
        """
        Calculates the area of a rectangle.

        Args:
            length (float): Length of the rectangle.
            width (float): Width of the rectangle.

        Returns:
            float: The area of the rectangle.
        """
        return length * width
    ```
    - Very popular, especially in Google-style Python projects.
    - Uses `Args:` and `Returns:` keywords.
- **NumPy / SciPy Style**
    ```python
    def calculate_area(length, width):
        """
        Calculate the area of a rectangle.

        Parameters
        ----------
        length : float
            Length of the rectangle.
        width : float
            Width of the rectangle.

        Returns
        -------
        float
            Area of the rectangle.
        """
        return length * width
    ```
    - Popular in scientific Python projects (`numpy`, `pandas`, `scipy`).
    - Uses `Parameters` / `Returns` blocks with dashes.
- **Sphinx Style** ([see also here](https://github.com/xXAI-botXx/Project-Helper/blob/main/guides/Sphinx_Helper.md))
    ```python
    def calculate_area(length, width):
        """
        Calculate the area of a rectangle.

        :param msg: Length of the rectangle.
        :type msg: float
        :param msg: Width of the rectangle.
        :type msg: float

        :return: Area of the rectangle.
        :rtype: float
        """
        return length * width
    ```
    - More inconvenient.
    - Only if you are 100% to use Sphinx with your project.
    - Uses `:param msg:`+`:type msg:` / `:return:`+`:rtype:`


<br><br>

With that most of your work is done. Next you can use a lib like `pdoc` or `pydoc-markdown`, or you can write your own little extraction script.

<br><br>

**Converting your documentation into an markdown format with `pdoc`**

First install the package:
```bash
pip install pdoc
```

Now create your markdown:
```bash
pdoc --output-dir docs --template-dir . --format markdown your_package
```

Or:
```bash
pdoc --output-format markdown your_package > docs/documentation.md
```

See -> https://pypi.org/project/pdoc/

<br><br>

**Converting your documentation into an markdown format with `pydoc-markdown`**

Install the package:
```bash
pip install pydoc-markdown
```

Now create a `pydoc-markdown.yml` and fill it with (for example):
```yaml
loaders:
  - type: python
    search_path: ["."]
renderer:
  type: markdown
  filename: docs/documentation.md
modules:
  - your_module
```

Then run:
```
pydoc-markdown
```


See -> https://pypi.org/project/pydoc-markdown/


<br><br>

**Converting your documentation into an markdown format with your own script**

Python provides some very useful tools inside of its standard library for working with packages.

* `importlib`: used for **dynamically importing modules and packages** at runtime.
    * `importlib.import_module(name)`: import a module from its name string.
    * `importlib.reload(module)`: reload an already imported module.
    * `importlib.util.find_spec(name)`: find a module’s import specification without importing it.
* `inspect`: used for **introspecting objects** to extract metadata such as functions, classes, signatures, and docstrings.
    * `inspect.getmembers(obj)`: return all members (functions, classes, variables) of an object.
    * `inspect.getdoc(obj)`: get the cleaned-up docstring of an object.
    * `inspect.signature(obj)`: obtain the call signature of a function, method, or class constructor.
    * `inspect.isfunction(obj)`: check if an object is a user-defined function.
    * `inspect.isclass(obj)`: check if an object is a class.
    * `inspect.ismethod(obj)`: check if an object is a bound method of a class instance.
* `os`: used for **interacting with the operating system and file system**.
    * `os.makedirs(path, exist_ok=True)`: create directories recursively.
    * `os.path.join(a, b, ...)`: safely join multiple path components.
    * `os.listdir(path)`: list all files and folders in a directory.
    * `os.remove(path)`: delete a file from the filesystem.
    * `os.environ`: access or modify environment variables.
* `pkgutil`: used for **discovering and iterating through submodules and subpackages** in a package.
    * `pkgutil.walk_packages(path, prefix)`: iterate through all submodules in a package hierarchy.
    * `pkgutil.get_data(package, resource)`: access data files bundled inside a package.
    * `pkgutil.extend_path(path, name)`: extend a package’s search path for namespace packages.
* `textwrap`: used for **formatting, indenting, and wrapping text**, especially when cleaning up docstrings.

    * `textwrap.dedent(text)`: remove common leading whitespace from text blocks.
    * `textwrap.indent(text, prefix)`: add consistent indentation to each line.
    * `textwrap.fill(text, width)`: wrap text to a fixed width for better readability.
    * `textwrap.shorten(text, width)`: truncate text to fit within a maximum width.

* `types.ModuleType`: represents the **type object for all Python modules** (useful for type checking and annotations).
    * `isinstance(obj, ModuleType)`: verify that an object is a module instance.
    * Used mainly for type hints and runtime validation in introspection tools.



```python
import importlib
import inspect
import os
import pkgutil
import textwrap
from types import ModuleType


def generate_markdown_for_module(module: ModuleType) -> str:
    """Generate markdown documentation for a single module object."""
    lines = [f"# Module `{module.__name__}`\n"]

    # Module docstring
    module_doc = inspect.getdoc(module)
    if module_doc:
        lines.append(textwrap.dedent(module_doc) + "\n")

    members = inspect.getmembers(module)
    classes = [m for m in members if inspect.isclass(m[1]) and m[1].__module__ == module.__name__]
    functions = [m for m in members if inspect.isfunction(m[1]) and m[1].__module__ == module.__name__]
    variables = [
        m for m in members
        if not inspect.isbuiltin(m[1])
        and not inspect.isclass(m[1])
        and not inspect.isfunction(m[1])
        and not m[0].startswith("__")
    ]

    # --- Classes ---
    if classes:
        lines.append("## Classes\n")
        for name, cls in classes:
            lines.append(f"### `{name}`\n")
            cls_doc = inspect.getdoc(cls)
            if cls_doc:
                lines.append(textwrap.dedent(cls_doc) + "\n")

            try:
                sig = inspect.signature(cls)
                lines.append(f"**Constructor:**\n```python\n{name}{sig}\n```\n")
            except Exception:
                pass

            methods = inspect.getmembers(cls, predicate=inspect.isfunction)
            if methods:
                lines.append("#### Methods\n")
                for m_name, method in methods:
                    if m_name.startswith("__"):
                        continue
                    doc = inspect.getdoc(method)
                    try:
                        sig = str(inspect.signature(method))
                    except Exception:
                        sig = "()"
                    lines.append(f"- **`{m_name}{sig}`**")
                    if doc:
                        lines.append(f"  \n{textwrap.indent(textwrap.dedent(doc), '  ')}\n")

    # --- Functions ---
    if functions:
        lines.append("\n## Functions\n")
        for name, func in functions:
            try:
                sig = inspect.signature(func)
            except Exception:
                sig = "()"
            lines.append(f"### `{name}{sig}`\n")
            doc = inspect.getdoc(func)
            if doc:
                lines.append(textwrap.dedent(doc) + "\n")

    # --- Variables ---
    if variables:
        lines.append("\n## Variables / Constants\n")
        for name, val in variables:
            val_repr = repr(val)
            if len(val_repr) > 60:
                val_repr = val_repr[:57] + "..."
            lines.append(f"- **{name}** = `{val_repr}`")

    return "\n".join(lines)


def iter_submodules(package_name: str):
    """Yield (module_name, module) pairs for all submodules in a package."""
    package = importlib.import_module(package_name)
    yield package_name, package  # include the root package itself

    if hasattr(package, "__path__"):  # only packages have __path__
        for mod_info in pkgutil.walk_packages(package.__path__, prefix=package.__name__ + "."):
            try:
                submodule = importlib.import_module(mod_info.name)
                yield mod_info.name, submodule
            except Exception as e:
                print(f"Could not import {mod_info.name}: {e}")


def generate_docs_for_package(
    package_name: str,
    output_dir: str = "docs",
    combine_into_one_file: bool = True
):
    """Generate Markdown documentation for an entire package."""
    os.makedirs(output_dir, exist_ok=True)
    combined_lines = [f"# Documentation for package `{package_name}`\n"]

    for mod_name, module in iter_submodules(package_name):
        print(f"Documenting {mod_name}...")
        md_content = generate_markdown_for_module(module)
        relative_name = mod_name.replace(package_name + ".", "")

        if combine_into_one_file:
            combined_lines += [f"\n---\n\n## Module `{relative_name or package_name}`\n\n"]
            combined_lines += [md_content]
        else:
            # One file per module
            file_name = f"{relative_name or package_name}.md"
            path = os.path.join(output_dir, file_name)
            with open(path, "w", encoding="utf-8") as f:
                f.write(md_content)
            print(f"Wrote {path}")

    if combine_into_one_file:
        output_file = os.path.join(output_dir, f"{package_name}_DOCUMENTATION.md")
        with open(output_file, "w", encoding="utf-8") as f:
            f.write("\n".join(combined_lines))
        print(f"\nCombined documentation written to {output_file}")


if __name__ == "__main__":
    # Example usage:
    # Replace 'your_package_name' with your actual package
    generate_docs_for_package("your_package_name", output_dir="docs", combine_into_one_file=True)
```

Just run this script:

```bash
python doc_gen.py
```

<br><br>

Now you should be able to first write your documentation inside of python and then let the documentation transfered to markdown.

