# Own Python Module

[<img align="right" width=150px src="../res/rackete_2.png"></img>](../README.md)


- [Own Python Module](#own-python-module)
    - [Motivation](#motivation)
    - [Local Package](#local-package)
    - [Package Access](#package-access)
    - [Pip Package](#pip-package)
    - [Real Example (pip-module)](#real-example-pip-module)
      - [Another example](#another-example)



> click the **rocket** for going **back**


<br><br>

---
### Motivation

There are many good reasons to build a own module. Of course you can also just make python files and import and use them like:

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





