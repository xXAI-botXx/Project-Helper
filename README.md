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

### [Git Guide](guides/Git_Helper.md)
### [Python Virtual Environments](#Python-Virtual-Environments)
### [Sphinx Guide](guides/Sphinx_Helper.md)
### [Ipynb to Markdown](#Ipynb-to-Markdown)  <!-- README.md#Ipynb-to-Markdown -->
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
D
cd "Projects/my new cool project"
```
Now you can easily create a new environment with the installed library, like: "". In env_name you can give it your name like ".env" or "project_name_env".:
``` terminal
python -m venv env_name

or in other words:
python<version> -m venv <virtual-environment-name>
```
<br>
You activate a virtual environment with going in the Scripts directory and type activate.bat. If the environment is activate you see the name of the environment on the left.
``` terminal
cd env_name/Scripts
activate.bat
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

<!--<a name="Ipynb_to_Markdown"></a>-->

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

<a name="Blueprints"></a>
### Blueprints

- [Console Input/Output Helper](./python%20blueprints/cio_helper.py)

---

