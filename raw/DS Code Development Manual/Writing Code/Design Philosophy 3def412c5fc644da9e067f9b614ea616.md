# Design Philosophy

# General IO

## KIS

Keep it simple!

### IO

**DO**
Utilize basic types (str, int, float, bool, etc.)
They make it easy for your code to be understood, reused and maintained.
**AVOID**
Passing complex data types such as custom classes, dictionaries of arguments, or pandas dataframes. There are some cases where the above is necessary but when doing so make sure to clearly document in your docstring what keys and values or other input requirements exist for these objects. If you can use individual, basic arguments like str/int/float do that instead. If you can operate on the data as a list or generic iterable, do that instead.

If utilizing your code requires fumbling with complex data structures, people won't re-use it, and your code will be hard to maintain.

**DO**
Use functions for most functionality until you have a clear need for a class.
**AVOID**
Making classes without clear need. They add complexity, are easier to get wrong, and can be harder to maintain.

## Cohesion and Coupling

Cohesion and coupling are fundamental principles in software design that play a critical role in determining code maintainability and stability. Cohesion refers to the level of relatedness and organization within a component or module. It suggests that related code should be grouped together, promoting a clear and logical structure. When code exhibits high cohesion, it means that functions or elements within a module are closely related to a common purpose, making the code easier to understand, test, and maintain.

On the other hand, coupling represents the degree of interdependence between different modules or components. Low coupling signifies that these modules interact with each other in a minimal and well-defined manner, reducing the risk of unintended consequences when changes are made. In essence, low coupling follows the principle of "each unit should only talk to its friends and not to strangers," isolating the impact of changes to the intended components.

To enhance code maintainability and minimize the risk of introducing errors, it is advisable to strive for high cohesion and low coupling in software design, ensuring that related code is organized together while minimizing unnecessary dependencies between modules. This design philosophy promotes a more flexible, scalable, and robust software architecture.

![Untitled](Design%20Philosophy/Untitled.png)

## Maintain a stable interface

If you plan for one thing before you start building your program, let it be IO.
"[Breaking Changes](https://en.wikipedia.org/w/index.php?title=Breaking_changes&redirect=no)" are changes that prevent existing code from using the changed code, and these are most frequently caused by changes to IO.
This could be

- Changing the name of a function, method, or class
- Changing the input arguments to a function, method, or class
- Changing the output format of a function or method

## Designing Good IO First

Even if you don't know exactly *how* a piece of code is going to do what it needs to do, you should be able to determine *what* the piece of code needs to do.

Start at high level and then each time you notice a piece having more than one task, break out those tasks until you have a clear plan. A piece of paper, whiteboard, or your drawing tool of choice can be helpful here.

Maybe you need a UI and a backend? What information do they need to do their jobs? You could break them down into...

- Login
- Model prediction
- Logout

Then you might identify IO as

- Login
    - Inputs
        - Username (str)
        - Password (str)
    - Output
        - Session token (str)
- model prediction
    - Inputs
        - (str) Image file path in s3
    - Output
        - (str) Predicted class
        - (float) Confidence for class prediction

You can then break down each of these tasks even more and define IO for those subtasks and so on until you are confident in your design. These can be inputs to functions, classes, REST endpoints, anything. Just each junction where data is moving from one piece of code to another.

## Getting into the Weeds

When it comes time to write the actual code, you will notice things you didn't account for when planning, but most of the core inputs should match up.

### Identifying Core Inputs (Positional Arguments)

Positional arguments are the minimum inputs required for the code to complete it's task. If you designed IO ahead of time, these should be the main items in your design.

Going back to the loss metric example, to calculate a supervised loss you **always** need the model output and some expected output to compare against, and the specific values of these arguments are expected to change effectively every time the function is called. So `actual_output` and `expected_output` are good positional arguments.

You never want to change positional arguments if it can be avoided, doing so will cause breaking changes throughout your codebase, and for anyone else using it.

### Setting Sensible Defaults (Keyword Arguments)

Once you get into implementing the code, you may notice assumption you have to make to calculate the output. For example, if you are sampling the next token for a language model with your own custom code- you may for your use case find that `0.1` is a good temperature. If someone was to reuse your code they may want to change this value though!

This is when you should use a keyword argument. When for most instances you see the code being used one value is fine, but you could see it being used with a different value in the future.

### Wrapping complex tools without destroying functionality

In the vast majority of cases, using \*\*params or \*\*kwargs is not necessary, but it can be helpful at times.

If your code relies heavily on one function or method from another codebase, then you may want to expose different keyword arguments from that function as part of your own. Doing a keyword argument for each of these would take a lot of time. Instead, you can use `**params` to capture any additional keyword arguments as a dictionary. These can then be sent on to the wrapped code by calling it with the same `**params`

Example for passing keyword arguments to read_csv

```
def data_pipeline(input_csv, output_csv, **kwargs):
	"""
	Args:
			input_csv (str): The path to the input CSV file.
			output_csv (str): The path to the output CSV file where the processed data will be saved.
			**kwargs: Additional keyword arguments to customize the behavior of the Pandas read_csv function. See pandas.read_csv for relevant documentation.
	"""
    # Read the input CSV using Pandas with optional kwargs
    data = pd.read_csv(input_csv, **kwargs)

    # Perform some processing on the data
    processed_data = process(data)

    # Save the processed data to the output CSV in a standardized format
    data.to_csv(processed_data, index=False)

```

This way, if we need to run the pipeline on a new CSV that was formatted differently, it's easy to do so just by passing the read_csv options directly to process_data. In many cases, this is not necessary.

### Adding functionality without breaking IO

If you notice you need to update functionality, consider first if you *need* to update an existing function. Would it be better to create another function to perform the task? In the case that you do need to update the function, can you do so only by adding keyword arguments or \*\*kwargs? Try to avoid changing the positional arguments if possible.

# Functional Programming

Functional Programming is a programming paradigm that focuses on treating computation as the evaluation of mathematical functions. It promotes immutable data and avoids side effects.

## Why use Functional Programming?

### Parallelization & Efficiency

Functional programming is particularly well-suited for ***parallelization*** due to its emphasis on **pure functions** and **immutability**. Pure functions, which have no side effects and depend solely on their input, can be executed in parallel without the need for locks or synchronization mechanisms. Because there are no shared mutable states or variables that can be modified concurrently, each function call can be isolated and run independently across multiple threads or processes. This inherent thread safety simplifies the task of parallelizing code and can lead to significant performance improvements, especially in in the modern age, where practically all computers have many cores.

### Reduced Syntax Sugar

OOP, especially when dealing with inheritance and complex class hierarchies, can require a significant amount of boilerplate code. Functional Programming can lead to more concise and expressive code.

## What to avoid

### 1. Avoid side effects

Side effects occur when a function modifies a state or variable outside its scope. This can make code unpredictable and challenging to debug. For example, if a dataframe was passed to a function and edited In functional programming, it's essential to minimize side effects to create pure, deterministic functions. This makes your code more reliable and easier to debug.

**Example of a side effect**

```
def remove_even_numbers(input_list):
    for item in my_list:
        if item % 2 == 0:
            input_list.remove(item)
    return input_list

my_list = [1, 2, 3, 4, 5, 6]
odd_list = remove_even_numbers(my_list)
print(my_list)  # my_list and odd_list are the same object

```

prints

```
[1,3,5]

```

**Example that avoids side effects**

```
def remove_even_numbers(input_list):
    filtered_list = [item for item in my_list if item % 2 != 0]
    return filtered_list

my_list = [1, 2, 3, 4, 5, 6]
odd_list = remove_even_numbers(my_list)
print(my_list) # my_list and odd_list are different objects

```

prints

```
[1,2,3,4,5]
```

### 2. Be careful with mutable objects

Mutable objects can lead to unexpected behavior, as they can change their state at any time. In FP, immutability is a key principle. Immutable data structures ensure that once a value is created, it cannot be modified. This reduces the risk of bugs and simplifies reasoning about your code.

The example for #1 is only possible because lists are mutable objects.

## What to do

### 1. Modularity

Modularity in functional programming is about breaking down complex problems into smaller, independent functions. Each function should have a single responsibility and ideally be pure (no side effects). This approach results in reusable and composable components that make your codebase easier to maintain and extend.

### 2. Functions as values

In FP, functions are first-class citizens, which means they can be treated as values. You can pass functions as arguments to other functions, return them from functions, and store them in data structures. This functional technique allows for powerful abstractions, such as higher-order functions, map, filter, and reduce, which can lead to more concise and expressive code.

# Object Oriented Programming

Object-Oriented Programming (OOP) is a powerful paradigm that helps in designing and structuring software systems. However, like any programming paradigm, it comes with best practices and pitfalls. In this guide, we will explore what to do and what to avoid when working with OOP.

## Why Use OOP?

Generally, prefer functions over classes/methods unless there is a clear need to encapsulate some state or create a generic interface with multiple implementations.

## Maintaining State (Encapsulation)

The bundling of attributes and methods within a class allows users of that class to avoid delving into the intricacies of its inner workings. For instance, consider a class handling a model object. It may involve a tokenizer, the model itself, and the selection of various hyperparameters. Once you've instantiated the object with your chosen (or default) values for each of these, you can safely forget about them. The object will manage and remember them on your behalf.

## Creating Explicit Interfaces

By designing an abstract class or interface, you can establish clear guidelines that all potential future code must adhere to in order to be compatible with your own code. This can enhance code interoperability, extensibility, and maintainability.

### Abstraction and Modeling

OOP excels in modeling complex systems by using real-world concepts and encapsulating them into objects. These objects bundle both data (attributes) and behaviors (methods) into a single unit, providing a natural way to abstract and represent entities.

This abstraction is valuable when dealing with systems that have a strong relationship with the real world, such as simulations, graphical user interfaces, or video games.

This abstraction can be ***dangerous*** when we don’t subdivide the individual components of the concept we are representing- leading to large unwieldy classes.

## What to Avoid

### 1. Avoid multiple inheritance

Multiple inheritance is a feature in some OOP languages that allows a class to inherit properties and behaviors from more than one base class. While it can be useful in some cases, it can also lead to complex and ambiguous relationships, known as the [diamond problem](https://en.wikipedia.org/wiki/Multiple_inheritance#The_diamond_problem). This can make your code harder to understand and maintain. Instead, consider using interfaces or [composition](https://en.wikipedia.org/wiki/Composition_over_inheritance) when dealing with multiple aspects of an object's behavior.

### 2. Avoid inheriting from anything that isn't an interface (abstract base class)

In OOP, inheritance is a way to create a new class that is a modified version of an existing class. While it can be a powerful tool, it should be used with caution. Inheriting from concrete classes or non-abstract classes can lead to tight coupling, making your code less flexible, harder to test, and harder to read/understand. Inheriting from abstract base classes or implement interfaces does not cause this issue, as they only define general IO (or `contracts`) but do not hide code.

Generally, prefer [composition over inheritance](https://en.wikipedia.org/wiki/Composition_over_inheritance). That is to say, rather than inheriting from an object to gain it's functionality, contain an instance of that other class as part of your new class. This makes it much easier to follow where different pieces of functionality are coming from.

### 3. Avoid large or monolithic classes

Just like functions, creating large, monolithic classes can make your codebase difficult to understand, manage and extend. They should have one focused purpose.

Strive to create smaller, focused classes that have clear responsibilities. This not only improves code organization but also enhances reusability and testability.

## What to do

### 1. Composition over inheritance

Composition is a design principle that encourages you to create objects by composing them from smaller, reusable components rather than using inheritance. It promotes loose coupling, allowing you to change the behavior of an object by re-configuring its components. This approach leads to more flexible and maintainable code.

### 2. Keep objects targeted

When designing classes and objects, ensure that they have a specific and well-defined purpose. This practice follows the [Single Responsibility Principle](https://en.wikipedia.org/wiki/Single-responsibility_principle) and helps you create classes that are easy to understand, maintain, and test. A focused and targeted object is more likely to be reusable in different contexts.

### 3. Creating Interfaces

Interfaces define a contract (Input & output) that classes can implement. By using interfaces, you can decouple your code from specific implementations and promote flexibility and modularity. This is particularly valuable when multiple classes need to adhere to a common set of methods and behaviors. Creating interfaces allows you to write code that is more adaptable and less dependent on specific class hierarchies.

By defining an interface, you make a clear assumption about what the class will do- and enable's you to more easily swap out one implementation of the interface for another.