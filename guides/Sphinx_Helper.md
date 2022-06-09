---

# Guide for Sphinx

by Tobia Ippolito 
<br>
<br>

This guide explains Sphinx and how to use it in projects. For an example see this documentation which is created with Sphinx

<br>

> click the **rocket** for going **back**

---

## Table of Content

[<img align="right" width=150px src='../res/rackete_2.png'></img>](../README.md)

- [What is Sphinx and why i should use it?](#What-is-Sphinx-and-why-i-should-use-it?)
- [How to program your own module in python](#How-to-program-your-own-module-in-python)
- How to use it
  - [Generate Code Documentation](#Generate-Code-Documentation)
- [Publish as Website](#Publish-as-Website)
- [How you should code (to use Sphinx later)](#How-you-should-code)

---

### What is Sphinx and why i should use it?

Sphinx it an nice tool for generating a documentation for your own module. Moreover you can generate an online api website which is pretty nice.<br>
<br>
Here are few picture of an api I have created. 

<!-- images -->

You are interested: than let's start :)

---

### How to program your own module in python

1. Create a folder named as your module has to being called

2. You can now adding python files in this directory and/or you can add more directories with python files there

3. Add a python file called __init__.py which defines which submodules/files will be loaded.

As an example I want to create my own game engine as a module. The engine/the module should call Real Engine.
The directory-tree looks like this:<br>
(./ means relative to the project)
<pre>
./Real Engine
      main.py
      /physics
            /physic_helper.py
            /physic_engine.py
      /graphics
            /graphic_engine.py
      /sound
            /mp4_player.py

</pre>
<br><br>
You have to implement it like this:


<br><br>
In the first __init__.py stands something like this:<br>
  ./Real Engine/__ini__.py<br>

  ```python
  from . import main
  from .physics import *
  from .graphics import *
  from .sound import *
  ```

<br><br>
And you don't have to import each method or class in the __init__.py, only the things you need. Internally you can easily import own methods from another python file of your module with:

```python
  from ..other_file import helper_method
```

The .. stands for 1 folder above and . is the current directory. So you can navigate to the file/folder you want and import your things. 
<br><br>
And remember: You can't run your own module in your own module. You have to call your module/method/file from another location and file.
<br><br>

When using your module from another location/file you have to adding the directory where the module is, that the python interpreter can find your module for importing. It could look like this:<br>

```python
  import os
  import sys
  sys.path.insert(0, os.path.abspath('../../src'))
```
<br><br><br>
I hope all was clear and I missed nothing. And I know sometimes it can be confusion but try it out, make some mistakes and learn from it. Good luck :)

---

### Generate Code Documentation

1. pip nstall sphinx

2. in directory where the generation (html/pdf) should be (maybe doc or code_doc)
    -> as example: C:\Users\tobia\AppData\Local\Programs\Python\Python39\Scripts\sphinx-quickstart 

3. generate documentation with cmd: location_of_sphinx-apidoc -o dirname path_to_module
    -> as example: C:\Users\tobia\AppData\Local\Programs\Python\Python39\Scripts\sphinx-apidoc -o  code_doc src/Real Engine/

4. Adding following by extinsions = [] in conf.py
  extensions = [
      'sphinx.ext.autodoc',
      'sphinx.ext.napoleon',
      'sphinx.ext.viewcode'
  ]

5. Adding: html_theme = 'sphinx_rtd_theme' by conf.py by html_theme

6. pip install sphinx_rtd_theme

7. Go to first 3 Commands in index.rst (in source folder) and write after them this: modules (feel free to change here the structure)

8. go in doc folder (or how it calls) and call make html or make latexpdf (then see in build)

If you want to add your own tabs, you have to create extra .rst-files and call them in the index.rst file. Watch tutorials on how to create .rst files and go for it :)
<br><br>
An example index.rst file is that:<br>

``` rst
.. Wer hat gebohrt? documentation master file, created by
   sphinx-quickstart on Sat Jan 15 22:56:30 2022.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to the documentation of Wer hat gebohrt?
================================================

..  toctree::
   :maxdepth: 4
   :caption: Contents

   introduction.rst
   tutorial.rst
   explain.rst
   anoog.rst

Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
```

And the tutorial as an second example looks like that:<br>

``` rst
Tutorial
^^^^^^^^
This library is programmed for distinguish drilling persons.

To start the application, you need a setup with following points:

* drilldriver and drillcapture programs
* wired drill
* wood for drilling
* at least 2 persons
* our library
* python3 installed
* all modules in the requirements.txt (see below)

.. code-block:: none

    ###### Requirements without Version Specifiers ######
    pandas
    matplotlib
    sklearn
    ttkthemes
    pyyaml
    tsfresh
    pillow
    webbrowser

    ###### Requirements with Version Specifiers ######
    numpy <= 1.20

    ###### Linux System need to install ######
    # Tkinter
    # sudo apt-get install python3-tk 

    # And Pillow Tk
    # sudo apt-get install python3-pil.imagetk


If the setup is ready, you have to call the run-function in following module:

:ref:`gui`

See the RUN.py for an exemplary call.

.. hint::

    Dabei muss sichergestellt sein, dass kein weiterer Drilldriver läuft. Um das zu tun, muss einfach der Output in der Konsole/Terminal überprüft werden und es muss **MCCUDP found!** dastehen. Fall dort etwas steht wie **MCCUDP Acquiring...** muss die Anwendung geschlossen werden und folgende Schritte befolgt werden:
    
    1. Im Terminal **killall MCCUDP** eingeben
    2. Erneut **killall MCCUDP** im Terminal eingeben
    3. Anwendung mit **python3 RUN.py** starten
    4. Anwendung schließen
    5. Anwendung erneut starten und nun funktioniert alles wieder
```

<br><br>
Remember: the .rst for the code-doc should be created by Sphinx but you still can manipulate them. You could say that the max-depth should be smaller and so the subtab amount is smaller. 

---

### Publish as Website

 You can make a GitHub Page. It's a fully website with no restrictions. And it's for free. If you coded with GitHub it's more easy. You have to make a repository and follow the points following:<br>
<br>
1. make a branch and there should only be the html stuff
2. create a empty .nojekyll file for right show
3. go to settings and activate github page with the branch
<br><br>
For an example see the [Wer hat gebohrt?](https://github.com/xXAI-botXx/Wer-hat-gebohrt) project. Switch the branch to the html branch.


---

### How you should code

You have to write a python docstring under each def, class, module. So it's easier if you start with writing these docstrings to the beginning of the project.<br>
You didn't know how it is mean. No problem here is an example to help out:<br>
<br>

```python
def hello_world(msg):
  """
  This is a docstring
  """
  print("hello World"+msg)
  return "hello World"+msg
```
<br><br>
And now prepared as how you should program it:<br>

```python
def hello_world(msg):
  """
  Helper Method, which print a message with hello world before.
  It returns it too. 

  :param msg: Message which will be printed
  :type msg: str

  :return: Returns the input message after hello world
  :rtype: str
  """
  print("hello World"+msg)
  return "hello World"+msg
```

<br>
<br><br>
Here are important writing styles for Sphinx:<br>
<br>
:param [ParamName]: [ParamDescription], defaults to [DefaultParamVal]<br>
:type [ParamName]: [ParamType](, optional)<br>
...<br>
:raises [ErrorType]: [ErrorDescription]<br>
...<br>
:return: [ReturnDescription]<br>
:rtype: [ReturnType]<br>
<br>
<br>
Reference to a class or function or something:<br>
:class:`~anoog.automation.graphical_user_interface.GUI_App`<br>
<br>
For more/specific info look here: https://www.sphinx-doc.org/en/master/usage/restructuredtext/domains.html#cross-referencing-syntax

<br><br><br>
And at least a bigger/real example:<br>

```python
"""
This module is used to hold the train/predict data and also to train/predict with a ML-Algorithm

Author: Tobia Ippolito
"""

import os
from enum import Enum
from datetime import datetime
from time import time
import numpy as np 
import pandas as pd 
import matplotlib.pyplot as plt

from ..model import train_random_forest, train_svc, train_knn, train_naive_bayes, train_logistic_regression, train_adaboost, train_voting_classifier
from ..model import predict, predict_proba 
from ..io import load_single_data, extraction_mode, selection_mode, X_y_split

ALGORITHM = Enum('ALGORITHM', 'RANDOM_FOREST SVC KNN NAIVE_BAYES LOGISTIC_REGRESSION ADA_BOOST VOTING_CLASSIFIER')

class AI_Model(object):
    """
    This class used to:
        - collect train- and predict-data
        - train supervised classification ML-Algorithmn
        - make prediction with the trained model 

    This class makes an protocoll of it tasks, to understand the progress.
    By init or resetting it clears this protocol.
    """
    def __init__(self):
        """
        Constructor method
        """
        self.train_data = pd.DataFrame()
        self.predict_data = pd.DataFrame()
        self.model = None
        self.should_normalize = False
        self.drill_id = 0
        self.file_path = os.path.abspath(__file__).replace("\\", "/")
        self.dir_path = "/".join(self.file_path.split("/")[:-1])
        self.clean_log()
        # constant
        self.cv = 3

    def add_train_dataset(self, person_id:int, person:str, data_path:str):
        """
        Load last train-drill-data in DataFrame. Using buffer with 30s timeout (if the data need some time to saving).
        
        :param person_id: ID of the Person who drill last time.
        :type person_id: int
        :param person: Name of the Person who drill last time.
        :type person: str
        :param data_path: Path to the data-directory. To know where collect the data.
        :type data_path: str
        """
        now = datetime.now()
        date = f"{now.year}-{now.month}-{now.day}"
        data_path = f"{data_path}/{date}"
        self.write_log("Load a single train_data")
        # load new dataset + buffer
        begin = time()
        while True:
            try:
                new_data = load_single_data(person_id,
                                            person=person,
                                            data_path=data_path, 
                                            drill_id =self.drill_id,
                                            extraction=extraction_mode.MANUEL,
                                            #extraction=extraction_mode.TSFRESH,
                                            show_extraction=False,
                                            selection=selection_mode.NONE,
                                            train_test_split=False,
                                            test_size=0.3,
                                            save_as_csv=False,
                                            csv_name=None,
                                            normalize=False)
                if new_data.empty != True:
                    break
            except FileNotFoundError as e:
                print("File not found...")
                if int(time() - begin) > 30:
                    print("Wait too long for File saving!")
                    raise FileNotFoundError("While Saving Single Train-Data")
            except IndexError as e:
                print("Latest drill not found (Index out of Bounds)...")
                if int(time() - begin) > 30:
                    print("Wait too long for File saving!")
                    raise IndexError("While Saving Single Train-Data")

        if type(new_data) != None:
            # add new dataset
            self.train_data = self.train_data.append(new_data)
            print("Dataentry added:\n", new_data)
            self.drill_id += 1
        self.write_log("Loading a single train_data is finished")

    def add_predict_dataset(self, person_id:int, person:str, data_path:str):
        """
        Load last predict-drill-data in DataFrame. Using buffer with 30s timeout (if the data need some time to saving).

        :param person_id: ID of the Person who drill last time.
        :type person_id: int
        :param person: Name of the Person who drill last time.
        :type person: str
        :param data_path: Path to the data-directory. To know where collect the data.
        :type data_path: str
        """
        now = datetime.now()
        date = f"{now.year}-{now.month}-{now.day}"
        data_path = f"{data_path}/{date}"
        self.write_log("Load a single predict_data")
        # load new dataset + buffer
        begin = time()
        while True:
            try:
                new_data = load_single_data(person_id,
                                            person=person,
                                            data_path=data_path, 
                                            drill_id =self.drill_id,
                                            extraction=extraction_mode.MANUEL,
                                            show_extraction=False,
                                            selection=selection_mode.NONE,
                                            train_test_split=False,
                                            test_size=0.3,
                                            save_as_csv=False,
                                            csv_name=None,
                                            normalize=False)
                if new_data.empty != True:
                    break
            except FileNotFoundError as e:
                print("File not found...")
                if int(time() - begin) > 30:
                    print("Wait too long for File saving!")
                    raise FileNotFoundError("While Saving Single Predict-Data")
            except IndexError as e:
                print("Latest drill not found (Index out of Bounds)...")
                if int(time() - begin) > 30:
                    print("Wait too long for File saving!")
                    raise IndexError("While Saving Single Predict-Data")
                    
        if type(new_data) != None:
            # add new dataset
            try:
                self.predict_data = self.predict_data.append(new_data.drop(columns=['y']))
            except KeyError as e:
                self.predict_data = self.predict_data.append(new_data)
            self.drill_id += 1
        self.write_log("Loading a single predict_data is finished")

    def remove_last_train_dataset(self):
        """
        Removes last train-data-entry from DataFrame.
        """
        self.train_data = self.train_data.head(self.train_data.shape[0]-1)
        self.write_log("Removed Last Train Dataset")

    def remove_last_predict_dataset(self):
        """
        Removes last predict-data-entry from DataFrame.
        """
        self.predict_data = self.predict_data.head(self.predict_data.shape[0]-1)
        self.write_log("Removed Last Predict Dataset")

    def remove_predict_dataset(self):
        """
        Removes complete predict-data from DataFrame.
        """
        self.predict_data = pd.DataFrame()
        self.write_log("Removed Complete Predict Dataset")

    def train(self, algorithm=ALGORITHM.RANDOM_FOREST, auto_params=False, normalize=True):
        """
        Train a model of choice. The Hyperparameters can choose as predefined or searched with GridSearchCV.
        And the data can be normalized if needed.

        Note:
        If there are not enough data-entries, GridSearchCV don't be choosen. 
        There have to be more than 5 entries.

        :param algorithm: To know which ML-Algorithmn to choose
        :type algorithm: int, optional
        :param auto_params: To know if Hyperparameter choose predefined or GridSearchCV
        :type auto_params: str, optional
        :param normalize: To know if the data should be normalized
        :type normalize: str, optional
        """
        # auto-params only if greater than CV*2
        if auto_params:
            if len(self.train_data) < self.cv*2:
                auto_params = False

        self.write_log("Start train")
        self.should_normalize = normalize
        self.train_data = self.remove_unused_columns(self.train_data)
        X, y = X_y_split(self.train_data)
        if algorithm == ALGORITHM.RANDOM_FOREST:
            self.model = train_random_forest(X, y, auto_params=auto_params, normalize=normalize, cv=self.cv)
        elif algorithm == ALGORITHM.SVC:
            self.model = train_svc(X, y, auto_params=auto_params, normalize=normalize, cv=self.cv)
        elif algorithm == ALGORITHM.KNN:
            self.model = train_knn(X, y, auto_params=auto_params, normalize=normalize, cv=self.cv)
        elif algorithm == ALGORITHM.NAIVE_BAYES:
            self.model = train_naive_bayes(X, y, auto_params=auto_params, normalize=normalize, cv=self.cv)
        elif algorithm == ALGORITHM.LOGISTIC_REGRESSION:
            self.model = train_logistic_regression(X, y, auto_params=auto_params, normalize=normalize, cv=self.cv)
        elif algorithm == ALGORITHM.ADA_BOOST:
            self.model = train_adaboost(X, y, auto_params=auto_params, normalize=normalize, cv=self.cv)
        elif algorithm == ALGORITHM.VOTING_CLASSIFIER:
            self.model = train_voting_classifier(X, y, auto_params=auto_params, normalize=normalize, cv=self.cv)
        self.write_log("End train")

    def predict(self):
        """
        Predicts the predict-data with the trained model.
        If the train-data was normalize, the predict-data also do. Normalization is working with train-data,
        to have the same scale.

        If there are more than one predict-data-entry. Every Entry going to predict 
        and the probabilities are stacked (soft voting) and scaled to 100%.

        :return: Returns the prediction probability
        :rtype: tuple(float, float)
        """
        self.write_log("Start Predict")
        X, y = X_y_split(self.train_data)    # for normalization
        self.predict_data = self.remove_unused_columns(self.predict_data)
        result = predict_proba(self.model, self.predict_data, X, self.should_normalize)
        # If more then one drill -> we would like to add the votes
        self.write_log(f"Predict Result: {result}")
        #print(result)
        votes = np.sum(result, axis=0)
        if len(votes) == 1:
            votes = np.append(votes, 0.0)
        all = votes[0] + votes[1]
        proba_result = [votes[0]/all, votes[1]/all]
        self.write_log(f"Voted: {proba_result}")
        self.write_log("End Predict")
        return proba_result

    def remove_unused_columns(self, data):
        """
        Tries to remove some irrelevant Features, which shouldn't exist anymore.
        It's no problem, if they don't exist.

        :param data: Data, which will be trimmed
        :type data: pd.DataFrame

        :return: Return new trimmed DataFrame
        :rtype: pd.DataFrame
        """
        new_data = data.copy()
        if 'Audio' in data.columns:
            new_data = new_data.drop(columns=['Audio'])
        if 'Current' in data.columns:
            new_data = new_data.drop(columns=['Current'])
        if 'Voltage' in data.columns:
            new_data = new_data.drop(columns=['Voltage'])
        if 'ID' in data.columns:
            new_data = new_data.drop(columns=['ID'])
        return new_data

    def draw_result(self, person1_name, person1_proba, person2_name, person2_proba, save_path):
        """
        Plots the result of probability classification as bar-chart with 2 charts.

        Arguments:
            - person1_name as str (to plot the name)
            - person1_proba as int (to plot the probability)
            - person2_name as str (to plot the name)
            - person2_proba as int (to plot the probability)
            - save_path as str (to know where to save the plot)
        

        :param person1_name: Name of the first person.
        :type person1_name: str
        :param person1_proba: Probability of the first Person. Defines the height of one bar.
        :type person1_proba: float or int
        :param person2_name: Name of the second person.
        :type person2_name: str
        :param person2_proba: Probability of the second Person. Defines the height of one bar.
        :type person2_proba: float or int
        :param save_path: Path, where the plot should be saved.
        :type save_path: str
        """
        plt.style.use('seaborn-whitegrid')
        fig = plt.figure()
        ax = plt.axes()

        ax.bar(x=[person1_name, person2_name], height=[person1_proba, person2_proba])
        ax.set_title("Prediction")
        ax.set_xlabel('Candidates')
        ax.set_ylabel('Probability')

        plt.savefig(save_path, transparent=False)

    def clean_log(self):
        """
        Clears the protocol of the AI-Model.

        Should be called once at the beinning of the program.
        """
        with open(f"{self.dir_path}/output/AI-Model-log.txt", "w") as f:
            f.write("")

    def reset(self):
        """
        Sets the variables (like the train and predict-DataFrame) to initial values.
        (Clears also the protocol)
        """
        self.train_data = pd.DataFrame()
        self.predict_data = pd.DataFrame()
        self.model = None
        self.drill_id = 0
        self.file_path = os.path.abspath(__file__).replace("\\", "/")
        self.dir_path = "/".join(self.file_path.split("/")[:-1])
        self.clean_log()

    def write_log(self, message:str):
        """
        Writes a String in the protocol-file.

        This method should be used to add a entry to the protocoll.

        :param message: Message which will be saved in log.
        :type message: str
        """
        now = datetime.now()
        date_str = f"{now.hour}:{now.minute}:{now.second}"
        with open(f"{self.dir_path}/output/AI-Model-log.txt", "a") as f:
            message = f"\n\n- log entry at {date_str}:: {message}"
            f.write(message)
```

---

