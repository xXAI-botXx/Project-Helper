# Doxygen

[<img align="right" width=150px src='../res/rackete_2.png'></img>](../README.md)


Doxygen is documentation creation software for C++, similiar to Sphinx in python.


Table of Contents:
- [Installation](#installation)
- [Write Doxygen Comments](#write-doxygen-comments)
- [Generation](#generation)



> click the **rocket** for going **back**






---
### Installation

On Ubuntu:
```bash
sudo apt install doxygen
sudo apt install graphviz
```

On Windows (via Chocolatey, see also [this chocolately guide](../guides/Chocolately.md)):
```powershell
choco install doxygen.install
choco install graphviz
```





---
### Write Doxygen Comments

> Watch the Big Example, this should be most helpful

In order to create a documentation with doxygen, you have to use special comments. Since the header files contains the information about all the available functions, methods, classes and variables you only need to write such comments in the header files.<br>
The comments always starts with a @, for example @class MyAwesomeClass. Comments in C++ for doxygen are made with:

```cpp
/// comment

///< var comment

/**
 * My
 * Multiline
 * comment
 */
```

Available doxygen tags are:

| Category | Tag      | Description | Example |
| -------- | -------- | ------- | ------- |
| **General** | @brief   | A short description of a class, function, or variable. | ```/** @brief Computes activation. */``` |
|          | @details | A longer description following @brief. | ```/** @details Uses ReLU or Sigmoid. */``` |
|          | @note    | Adds a note section. | ```/** @note This function is experimental. */``` |
|          | @warning | Adds a warning section. | ```/** @warning Avoid negative values! */``` |
|          | @bug     | Documents a known bug. | ```/** @bug Division by zero occurs. */``` |
|          | @todo    | Adds a TODO item. | ```/** @todo Implement batch normalization. */``` |
| **Class** | @class | Documents a class. | ```/** @class Layer @brief Represents a NN layer. */``` |
|          | @struct  | Documents a struct. | ```/** @struct Vector @brief A 3D vector. */``` |
|          | @namespace | Documents a namespace. | ```/** @namespace rush::math */``` |
|          | @typedef | Describes a typedef. | ```/** @typedef float Scalar @brief Defines Scalar. */``` |
|          | @enum | Documents an enumeration. | ```/** @enum Activation @brief Types of activation. */``` |
|          | @union | Documents a union. |   |
| **Function** | @param | Describes function parameters. | ```/** @param input The input value. */``` |
|          | @return  | Describes the return value of a function. | ```/** @return Activated value. */``` |
|          | @throws | Describes an exception the function might throw. | ```/** @throws std::runtime_error on error. */``` |
| **Variable** | @var | Describes a variable. | ```/** @var int size @brief Number of neurons. */``` |
|          | @brief | Brief description of a member variable. |   |
| **Code** | @code / @endcode | Formats a code block. | ```/** @code Layer l(10); @endcode */``` |
|          | @verbatim / @endverbatim | Preserves spacing (raw text). | ```/** @verbatim Raw text here @endverbatim */``` |
| **Lists** | @li | Creates a list item. | ```/** - @li Sigmoid - @li ReLU */``` |
|          | @tableofcontents | Inserts a table of contents. | ```/** @tableofcontents */``` |
| **Reference** | @see | Links to another function or class. | ```/** @see activate() */``` |
|          | @ref | Creates a reference to another section. | ```/** @ref ActivationTypes */``` |
| **Grouping** | @ingroup | Assigns an item to a group. | ```/** @ingroup MathFunctions */``` |
|          | @defgroup | Defines a new group. | ```/** @defgroup MathFunctions Math Ops */``` |
| **Sections** | @section | Creates a new section. | ```/** @section intro Introduction */``` |
|          | @subsection | Creates a subsection. | ```/** @subsection details More Details */``` |
| **Diagrams** | @dot | Creates a Graphviz diagram. | ```/** @dot digraph G { Layer -> Neuron; } @enddot */``` |
|          | @uml | Creates a UML diagram (if Graphviz is installed). | ```/** @uml class Layer { } @enduml */``` |

And there are some more. Like @author.


Small Real Example:
include/rush/nn/layer.h

```cpp
#pragma once

namespace rush {
namespace nn {

/**
 * @class Layer
 * @brief Represents a neural network layer.
 */
class Layer {
public:
    /**
     * @brief Constructs a new Layer object.
     * @param size The number of neurons in the layer.
     */
    Layer(int size);

    /**
     * @brief Performs a forward pass through the layer.
     */
    void forward();

private:
    int size; ///< The number of neurons in the layer.
};

} // namespace nn
} // namespace rush
```


Big Example, neural_network.hpp:

```cpp
/**
 * @file neural_network.h
 * @brief Defines core neural network components.
 * @author Tobia Ippolito
 * @date 2025-03-25
 * @version 1.0
 */

#pragma once

#include <vector>
#include <stdexcept>

/**
 * @namespace rush
 * @brief The main namespace for the Tensor-Rush deep learning framework.
 */
namespace rush {

/**
 * @namespace rush::math
 * @brief Contains mathematical utilities.
 */
namespace math {

/**
 * @class Vector
 * @brief Represents a mathematical vector.
 */
class Vector {
public:
    std::vector<float> data; /**< @var data Holds vector elements. */

    /**
     * @brief Constructs a vector of given size.
     * @param size The size of the vector.
     */
    Vector(size_t size) : data(size, 0.0f) {}

    /**
     * @brief Computes the dot product with another vector.
     * @param other The other vector.
     * @return The dot product value.
     * @throws std::runtime_error If vectors have different sizes.
     */
    float dot(const Vector& other) const {
        if (data.size() != other.data.size()) {
            throw std::runtime_error("Vector sizes do not match!");
        }
        float result = 0;
        for (size_t i = 0; i < data.size(); i++) {
            result += data[i] * other.data[i];
        }
        return result;
    }
};

} // namespace math

/**
 * @enum Activation
 * @brief Defines different activation functions.
 */
enum class Activation {
    Sigmoid, /**< @brief Sigmoid function */
    ReLU,    /**< @brief Rectified Linear Unit */
    Tanh     /**< @brief Hyperbolic Tangent */
};

/**
 * @class Layer
 * @brief Represents a layer in a neural network.
 */
class Layer {
public:
    size_t size; /**< @var size Number of neurons in the layer. */
    Activation activation; /**< @var activation The activation function used. */

    /**
     * @brief Constructs a new layer.
     * @param size Number of neurons.
     * @param activation Activation function.
     */
    Layer(size_t size, Activation activation)
        : size(size), activation(activation) {}

    /**
     * @brief Computes activation of a value.
     * @param x Input value.
     * @return Activated value.
     */
    float activate(float x) const {
        switch (activation) {
            case Activation::Sigmoid: return 1.0f / (1.0f + exp(-x));
            case Activation::ReLU: return x > 0 ? x : 0;
            case Activation::Tanh: return tanh(x);
        }
        return 0.0f; // Should never reach here
    }
};

/**
 * @defgroup Utilities Miscellaneous Utilities
 * @brief Helper utilities for debugging and validation.
 * @{
 */

/**
 * @brief Prints debug information.
 * @param msg The message to print.
 * @note Used for logging.
 * @bug Logging to files is not implemented.
 * @todo Implement log levels.
 */
void debugPrint(const std::string& msg) {
    std::cout << "[DEBUG]: " << msg << std::endl;
}

/** @} */ // End of Utilities

} // namespace rush
```




---
### Generation

1.

Just open your powershell/bash, navigate to your project folder and run following command inside your project directory:

```sh
doxygen -g
```

This will generate a Doxyfile -> a configuration file for the generation.
<br><br>

2. 

Write inside of the Doxfile (open it witht he GUI or with 'nano Doxyfile'):
```Doxyfile
PROJECT_NAME           = "Tensor-Rush"
OUTPUT_DIRECTORY       = "docs"
INPUT                  = include
RECURSIVE              = YES
GENERATE_HTML          = YES
GENERATE_LATEX         = NO
HAVE_DOT               = YES
```

Save and close it.
<br><br>

3. 

Now you can build your documentation:
```sh
doxygen Doxyfile
```
<br><br>

4. 

Show it with:
```sh
xdg-open docs/html/index.html  # Linux
start docs/html/index.html     # Windows
```



> Now you can copy all needed files (needed images and the html file) into another website branch in github and can host it over github as website.











