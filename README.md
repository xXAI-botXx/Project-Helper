<h1 align="center"> Project-Helper</h1>

by Tobia Ippolito
<br>
<br>

<p align="center">🟡 Welcome here, i'm there to help you out! 🟡</p>
<br><br>

<p align="center">In this repository you found helpful guides to specific topics of data science projects. Moreover I plan some projects as Issues.</p>

<br><br>

<div align="center">>>> Notice that this repository grow over the time <<<</div>



---

<h2 align="center">Navigation</h2>

<div align=center>

### [Python Virtual Environments](#python-virtual-environments)
### [Git Guide](guides/Git_Helper.md)
### [Sphinx Guide](guides/Sphinx_Helper.md)
### [Ipynb to Markdown](#ipynb-to-markdown)
### [Python Installation](#python-installation)

### [IPYNB Interactives](guides/IPYNB_Interactives.md)

### [Python Tests](guides/Python_Tests.md)

### [Python Custom Errors/Exceptions](#python-custom-exceptions)

### [Blueprints](#Blueprints)

</div>



---
### Python Virtual Environments

Often it's meaningful to create an extra environment for a new big project. It helps to structure your local installations and to amange libarry installations.<br>
<br>
You need a python installation and have to install the virtual environment library: 

``` terminal
pip install virtualenv
```

<br>

Now you have to open a terminal (cmd) and have to navigate to the target location.<br>
Example naviagtion:
``` terminal
D:
cd "Projects/my new cool project"
```

Now you can easily create a new environment with the installed library, like: "". In env_name you can give it your name like ".env" or "project_name_env".:
``` terminal
python -m venv env_name

or in other words:
python<version> -m venv <virtual-environment-name>
```

<br>

You activate a virtual environment with going in the Scripts directory and type activate.bat. If the environment is activate you see the name of the environment on the left.<br>
On Windows:
``` terminal
cd env_name/Scripts
activate.bat
```

On Linux:
``` terminal
cd env_name/bin
source activate
```

Now you can install packages with pip and can use the virtual environment. With the command "pip list" you can check the current installings.
If you have a requirements.txt (a list of installations with version specification) you can type:
``` terminal
pip install -r requirements.txt
```

If you want to generate a requirements.txt from a virtual environment, you type:
``` terminal
pip freeze > requirements.txt
```

For deactivating the current virtual environment you simply type:
``` terminal
deactivate
```



---

### Ipynb to Markdown

1. Installing the tools
  ``` terminal
pip install jupyter
pip install nbconvert
  ```

2. Start the cmd and go to the location of the notebook and type follow:
  ``` terminal
jupyter nbconvert --ClearMetadataPreprocessor.enabled=True --ClearOutput.enabled=True --to markdown README.ipynb
  ```



---

### Python Installation

**Python Installation under Windows**<br>
Download the exe installer file from the official Python website and follow the steps along. If you want to set it as new active environment, you should set the option to add to the PATH.<br>
Check the installation with opening a new CMD and type "python --version", now the installed version should appear.
<br><br>
**Python Installation on Linux** (unter Ubuntu 20.04)<br>

You can download and build the latest version of Python from the official Python website. It's pretty easy follow the steps and stay calm ;)<br>
Update your system's local repository list:<br>


```terminal
sudo apt update
```


Install supporting dependencies on your system with APT:

```terminal
sudo apt install build-essential zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libreadline-dev libffi-dev wget
```

Add important lib for some modules:
```terminal
sudo apt-get install libbz2-dev
```
(optional) For different TTS models you need espeak:
```terminal
sudo apt install espeak lzma liblzma-dev
```

Make a new directory to store the Python source files:

```terminal
mkdir /python && cd /python
```

Download the Python source code from the official FTP server:

```terminal
sudo wget https://www.python.org/ftp/python/3.8.10/Python-3.8.10.tgz
```


Extract the TGZ file that you just downloaded:

```terminal
sudo tar -xvf Python-3.8.10.tgz
```


You need to perform tests and optimizations before installing Python. This is important as it increases the execution speed of your code by at least 10 percent:

```terminal
cd Python-3.8.10
sudo ./configure --enable-loadable-sqlite-extensions
```

Build the package using the MakeFile present in the directory:

```terminal
sudo make install
```


After you've implemented these steps, check if Python has been installed on your computer by typing **python3 --version** in the terminal.



---

### Python Custom Exceptions

Simple own Exception with optional doc-string:

``````python
class MyException(Exception):
    """Raise if my exception is given"""
    pass

raise MyException("The robot can not plan the motion.")
``````



More complex exception:

``````python
class MotionError(Exception):
    def __init__(self, message, motion_type):
        self.message = "Can not plan motion. " + message
        self.motion_type = motion_type
        
    def __str__(self):
        return self.message


# test out
try:
    raise MotionError("Because it is a test", "ptp")
except MotionError as e:
    print(e.message)
    print(e.motion_type)
``````

(alternative)

``````python
class ValidationError(Exception):
    def __init__(self, message, *errors):
        super(ValidationError, self).__init__(message)
        self.errors = errors


# test out
try:
    raise ValidationError("A failure because it is a test", "ptp", 2, 45.6)
except ValidationError as e:
    print(e)
    print(f"Caused by one of these values: {e.errors}")
``````



You can also inherit from another Exception:

``````python
class MyException(ValueError):
    """Raise if my exception is given"""
    pass

raise MyException("The robot can not plan the motion.")
``````

``````python
class MyAppValueError(ValueError):
    """Raise when a specific subset of values in context of app is wrong"""
    
    def __init__(self, message, error_causing_value, *args):
        self.message = message 
        self.error_causing_value = error_causing_value         
        
        super(MyAppValueError, self).__init__(message, error_causing_value, *args) 
``````



**Important Available Exceptions:**

#### 1. `SyntaxError`
- **Description**: Raised when the Python parser encounters a syntax error in the code.
- **Example**:
  
    ```python
    x = 10
    if x == 10
        print("x is 10")
    ```
- **Runtime Critical?**: Yes, as it prevents the program from being executed.

#### 2. `IndentationError`
- **Description**: Raised when there are incorrect indentations in the code.
- **Example**:
    ```python
    def func():
    print("Hello")
    ```
- **Runtime Critical?**: Yes, as it affects the structure and execution of the code.

#### 3. `TypeError`
- **Description**: Raised when an operation or function is applied to an object of an inappropriate type.
- **Example**:
    ```python
    x = 10 + "5"
    ```
- **Runtime Critical?**: Yes, it can cause unexpected behavior or program termination.

#### 4. `NameError`
- **Description**: Raised when a local or global name is not found.
- **Example**:
    ```python
    print(y)
    ```
- **Runtime Critical?**: Yes, it indicates potential issues with variable naming and scoping.

#### 5. `ValueError`
- **Description**: Raised when a function receives an argument of the correct type but with an inappropriate value.
- **Example**:
    ```python
    x = int("abc")
    ```
- **Runtime Critical?**: Yes, it can lead to unexpected behavior or program termination.

#### 6. `KeyError`
- **Description**: Raised when a dictionary key is not found.
- **Example**:
    ```python
    my_dict = {"a": 1, "b": 2}
    print(my_dict["c"])
    ```
- **Runtime Critical?**: Yes, it affects the retrieval of data from dictionaries.

#### 7. `IndexError`
- **Description**: Raised when a sequence subscript is out of range.
- **Example**:
    ```python
    my_list = [1, 2, 3]
    print(my_list[3])
    ```
- **Runtime Critical?**: Yes, it indicates potential issues with accessing elements from sequences.

#### 8. `FileNotFoundError`
- **Description**: Raised when a file or directory is requested but cannot be found.
- **Example**:
    ```python
    with open("nonexistent_file.txt", "r") as file:
        content = file.read()
    ```
- **Runtime Critical?**: Yes, it prevents file operations from being executed as expected.



---

### Blueprints

Here are some helpful python files.

- [Console Input/Output Helper](./python%20blueprints/cio_helper.py)

---

