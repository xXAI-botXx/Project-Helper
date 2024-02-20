# IPYNB Interactives

[<img align="right" width=150px src="../res/rackete_2.png"></img>](../README.md)

- [IPYNB and interactives](#ipynb-and-interactives)
- [Basics](#basics)
- [Example](#example)
- [All Widgets](#all-widgets)



> click the **rocket** for going **back**



---

### IPYNB and interactives

Interactive python notebooks (short IPYNB) are the key file for data science, because you can interactifly and flexible work with data, try out things and also show your results to customers, because of markdown cells.

It also can be very helpful to have interaction in your notebook, so that customers don't have to change variables and else can change these values with a graphical element. Here we show, how they can be used with an example and a list of available widgets.

There are many more features, like differen stylings, layouts and more. Here we only cover the basics.



---

### Basics

Create a Widget:

``````python
import ipywidgets as widgets

feature_check = widgets.Checkbox(
                value=False,
                description='If you want to use x?',
                disabled=False,
                tooltip='If checked this will lead to ...',
                icon='check'
            )

feature_check.value
``````

There are many different Widgets available (see below). The variable will keep the value -> variable.value. The "value" paramater is the start value. The description is the showed text. 



For displaying widgets you just can call display-function.

``````python
from IPython.display import display

display(feature_check)
``````



It is also possible to listen to a change of a widget and make actions to it. It can be helpful using global to use variables outside the function:

``````python
# update features
def change_in_feature_check(change):
    global a_variable
    
    new_value = change.new
    if new_value:
        # do something
    else:
        # do something else

# event observing a widget
feature_check.observe(change_in_feature_check, names='value')
``````

If there is a change in feature_check widget the function will run.



With clear_output it is possible to clear the ouptu of a cell. That can be helpful when updating widgets (adding and removing widgets).

``````python
from IPython.display import clear_output

clear_output()
``````





---

### Example

This is a complex example to show what is possible with the Ipywidgets. We use a display function which will be called to draw the widgets. In some changes, some widgets will be added or removed, then the output get cleared and the widget get drawn again.<br>You can use lists of widgets to make that easily. The code looks a bit complicated but is pretty simple.  

Here is the notebook to the example.

``````python
# RUN ME AND CHECK MY OUTPUT #

import ipywidgets as widgets
from IPython.display import display, clear_output
from datetime import datetime

##################################
# Helper functions and listeners #
##################################

def display_widgets():
    """
    Display widgets for configuration.
    """
    print(f"{'#'*32}\n            GENERAL\n{'#'*32}")
    display(scaling_check)
    display(new_machine_check)
    [display(new_machine_widget) for new_machine_widget in new_machine_widgets]
    [display(date_widget) for date_widget in date_widgets]
    display(prediction_color)
    display(true_color)
    print(f"\n{'#'*32}\n             MODEL\n{'#'*32}")
    display(models_list)
    [display(hyperparameter_widget) for hyperparameter_widget in hyperparameter_widgets]
    print(f"\n{'#'*32}\n           FEATURES\n{'#'*32}")
    [display(feature_widget) for feature_widget in feature_widgets]

def create_feature_widgets(exclude=[], first_5_true=False):
    """
    Create feature widgets based on provided configurations.

    Args:
    - exclude (list): Features to exclude.
    - first_5_true (bool): Whether the first 5 features should be set to True.

    Returns:
    - list: List of feature widgets.
    """
    feature_widgets = []
    counter = 0
    for feature in Feature.__members__:
        if feature not in exclude:
            if first_5_true and counter < 5:
                value = True
            else:
                value = False
            feature_check = widgets.Checkbox(
                value=value,
                description=f'{feature}',
                disabled=False,
                tooltip='Should that feature be used?',
                icon='check'
            )
            feature_widgets += [feature_check]
            counter += 1
    return feature_widgets

def get_hyperparameter_widgets(model):
    """
    Get hyperparameter widgets based on the selected model.

    Args:
    - model (str): Selected model.

    Returns:
    - list: List of hyperparameter widgets.
    """
    hyperparameter_widgets = []
    if model == "Random Forest":
        hyperparameter_widgets += [widgets.IntSlider(
            value=12,
            min=1,
            max=800,
            step=1,
            description='n_estimators:',
            disabled=False,
            readout_format='d'
        )]
        hyperparameter_widgets += [widgets.Dropdown(
            options=['mse', 'friedman_mse', 'poisson'],
            value='mse',
            description='criterion:',
            disabled=False
        )]
        hyperparameter_widgets += [widgets.IntSlider(
            value=100,
            min=1,
            max=800,
            step=1,
            description='max_depth:',
            disabled=False,
            readout_format='d'
        )]
        hyperparameter_widgets += [widgets.FloatSlider(
            value=1.0,
            min=0.01,
            max=1.0,
            step=0.01,
            description='max_features:',
            disabled=False,
            readout_format='.2f',
        )]
        hyperparameter_widgets += [widgets.Checkbox(
            value=False,
            description='bootstrap',
            disabled=False,
            icon='check'
        )]
    elif model == "TSMixer":
        hyperparameter_widgets += [widgets.IntSlider(
            value=12,
            min=1,
            max=800,
            step=1,
            description='epochs:',
            disabled=False,
            readout_format='d'
        )]
        hyperparameter_widgets += [widgets.FloatSlider(
            value=0.01,
            min=0.000001,
            max=0.099999,
            step=0.000001,
            description='learning_rate:',
            disabled=False,
            readout_format='.6f'
        )]
        hyperparameter_widgets += [widgets.IntSlider(
            value=12,
            min=1,
            max=800,
            step=1,
            description='n_blocks:',
            disabled=False,
            readout_format='d'
        )]
        hyperparameter_widgets += [widgets.IntSlider(
            value=12,
            min=1,
            max=800,
            step=1,
            description='ff_dimensions:',
            disabled=False,
            readout_format='d'
        )]
        hyperparameter_widgets += [widgets.FloatSlider(
            value=0.1,
            min=0.00,
            max=0.99,
            step=0.01,
            description='dropout:',
            disabled=False,
            readout_format='.2f'
        )]
        hyperparameter_widgets += [widgets.Dropdown(
            options=['relu', 'sigmoid', 'tanh', 'laekyrelu'],
            value='laekyrelu',
            description='activation:',
            disabled=False
        )]
        hyperparameter_widgets += [widgets.Dropdown(
            options=['mean squared error', 'mean absolute error', 'cross entropy loss'],
            value='mean squared error',
            description='loss:',
            disabled=False
        )]
        hyperparameter_widgets += [widgets.Dropdown(
            options=['Adam', 'SGD', 'ASGD', 'Rprop', 'Adagrad'],
            value='Adam',
            description='optimizer:',
            disabled=False
        )]
    return hyperparameter_widgets

def update_hyperparameters(change):
    """
    Update hyperparameters based on the selected model.

    Args:
    - change (object): Change object.
    """
    global hyperparameter_widgets
    selected_model = change.new
    hyperparameter_widgets = get_hyperparameter_widgets(selected_model)
    clear_output()
    display_widgets()

def get_new_machine_widgets():
    """
    Get widgets for a new machine.

    Returns:
    - list: List of new machine widgets.
    """
    new_machine_widgets = []
    new_machine_widgets += [widgets.Text(
                            value='Date',
                            placeholder='Date',
                            description='Time Column Name',
                            disabled=False   
                        )]
    new_machine_widgets += [widgets.Text(
                            value='y',
                            placeholder='y',
                            description='Target Column Name',
                            disabled=False   
                        )]
    new_machine_widgets += [widgets.Text(
                            value=',',
                            placeholder=',',
                            description='Seperator',
                            disabled=False   
                        )]
    return new_machine_widgets

def update_features(change):
    """
    Update features based on the selection of using a new machine or not.

    Args:
    - change (object): Change object.
    """
    global new_machine_widgets
    global feature_widgets
    new_machine = change.new
    clear_output()
    if new_machine:
        new_machine_widgets = get_new_machine_widgets()
        feature_widgets = create_feature_widgets(exclude=["CORRELATION", "MASS_FLOW"])
        display_widgets()
    else:
        new_machine_widgets = []
        feature_widgets = create_feature_widgets()
        display_widgets()

##################
# Create Widgets #
##################

scaling_check = widgets.Checkbox(
    value=False,
    description='Scaling',
    disabled=False,
    tooltip='Toggle Scaling',
    icon='check'
)

new_machine_check = widgets.Checkbox(
    value=False,
    description='Using New Machine Data',
    disabled=False,
    tooltip='Do you use not the hacker and conveyor?',
    icon='check'
)

date_widgets = []
date_widgets += [widgets.DatePicker(
                        value=datetime.strptime('13.09.2023', "%d.%m.%Y").date(),
                        description='Train Start Date',
                        disabled=False
                    )]
date_widgets += [widgets.DatePicker(
                        value=datetime.strptime('27.11.2023', "%d.%m.%Y").date(),
                        description='Train End Date',
                        disabled=False
                    )]
date_widgets += [widgets.DatePicker(
                        value=datetime.strptime('28.11.2023', "%d.%m.%Y").date(),
                        description='Test Start Date',
                        disabled=False
                    )]
date_widgets += [widgets.DatePicker(
                        value=datetime.strptime('10.12.2023', "%d.%m.%Y").date(),
                        description='Test End Date',
                        disabled=False
                    )]

prediction_color = widgets.ColorPicker(
    concise=False,
    description='Prediction Color',
    value='#a225d0',
    disabled=False
)

true_color = widgets.ColorPicker(
    concise=False,
    description='Groundtruth Color',
    value='#d0ab25',
    disabled=False
)

models_list = widgets.Dropdown(
    options=['Random Forest', 'TSMixer'],
    value='Random Forest',
    description='Model:',
    disabled=False
)

hyperparameter_widgets = get_hyperparameter_widgets("Random Forest")

feature_widgets = create_feature_widgets(first_5_true=True)

####################
# Attach listserns #
####################

# Event observing for model choice
models_list.observe(update_hyperparameters, names='value')

# Event observing for new machine choice
new_machine_check.observe(update_features, names='value')



# Display widgets
display_widgets()
``````





---

### All Widgets

[Widget List — Jupyter Widgets 8.1.2 documentation](https://ipywidgets.readthedocs.io/en/latest/examples/Widget List.html)

[jupyter-widgets/ipywidgets · GitHub](https://github.com/jupyter-widgets/ipywidgets/blob/main/docs/source/examples/Index.ipynb) or [see here the notebook](./code/Widget List.ipynb) 

