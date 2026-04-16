# Unit Testing Basics

This page is intended for readers that have never been exposed to unit testing. The first section gives some quick introduction/background on what unit testing is and why it is important. The following section walks through basic PyTest unit testing syntax and coding practices. Finally, a very basic PyTest unit testing example walkthrough is covered at the end. 

# Introduction to Unit Testing

At the most basic level, a unit test is code that executes your code (or a feature within your code) to check if the code behaves correctly. A unit test is generally written to test a particular unit of code (e.g. a function, class method, etc.) to check if the unit returns the correct output for different inputs (which often include a series of different test cases and edge cases). 

Unit testing is essential in development because it ensures that each feature within your code will operate correctly as you add/change features within a piece of software. This becomes increasingly paramount as the complexity of the code increases. Without unit tests, a developer would have to keep track of each feature in their code and continuously test/update features as new features are added and new edge cases are discovered. With unit testing, the testing code automates this process. 

In software engineering, it is standard practice to write unit tests for every feature in your code, and in fact, most software engineers write unit tests before they write the code itself (AKA test driven development). This ensures that a developer has considered all of the test/use/edge cases for a feature before the feature is developed. 

# Coding Best Practices for Unit Testing

1. **Simplicity** - Each individual unit test should be simple and readable. A unit test should be developed without too much effort. The operation that is tested by a unit test should be obvious just by glancing at the testing function. A unit test should also be fast and computationally inexpensive.
2. **Independence and reliability** - Each test should test one specific operation/functionality. The test should only test a stand alone piece of code that does not depend on any other functions (i.e. a test should never fail because of a change in a completely unrelated function)
3. **Arrange/act/assert -** It is best practice to write unit tests in an arrange-act-assert pattern. The idea is to split a test into 3 clear operations: 
    1. Arrange - Prepare the variables, objects, and other prerequisite for your test
    2. Act - Perform the main operation that is being tested (i.e. calling a function, class method, etc.). Keep the action focused on the target behavior. 
    3. Assert - Following the act step (which should elicit some sort of response), the assert step will evaluate if the result is correct. 
    
    This arrange/act/assert pattern helps to ensure that tests are readable, modular, and have a unified structure/format. Each step should be clearly labeled with a comment. See the example below for more details
    

# Introduction to PyTest

Unit testing is generally performed with a unit testing framework. The most popular unit testing framework for python is PyTest. The basic syntax for writing tests in PyTest is below

## Basic PyTest Syntax

1. File name for the file containing the testing code should start or end with `test`
    
    ex: 
    
    `test_<test_name>.py`
    `<test_name>_test.py`
    
2. Unit testing functions should start with `test_`
3. For tests that are defined as methods of a class, the class name should start with `Test`, and the class should have no `__init__` method. Test method names should start with `test_`

## Basic unit test example walkthrough (with PyTest)

1. Install the PyTest library using the following command
    
    `pip install pytest`
    
2. Create a project directory for the purpose of this example with a python file and a testing python file as indicated below
    
    ```markdown
    - **demo_tests/**
    	- example.py
    	- test_example.py
    ```
    
3. Write a basic function for the sake of this example in the [`example.py`](http://example.py) file
    
    ```python
    # In example.py file
    def sum_nums(num1, num2):
    	return num1 + num2
    ```
    
4. Write some tests for the `sum_nums` function in the `test_example.py` file. Here, we first import the `pytest` module and we import our `sum_nums` function as a module. We then write two testing functions; one to test that the `sum_nums` function returns the correct output given an input, and a second to test that the `sum_nums` function returns the correct output type. This example is oversimplified, but note that each testing function only tests one thing
    
    ```python
    import pytest
    from example import *
    
    def test_sum_nums_value():
    	"""This test checks if the sum function returns the correct value"""
    	#arrange
    	num1 = 1
    	num2 = 2
    	#act
    	output = sum_nums(num1,num2)
    	expected = 3
    	#assert
    	assert output == expected
    
    def test_sum_nums_output_type():
    		"""This test checks if the sum function returns the correct type"""
      #arrange
    	num1 = 1
    	num2 = 2
    	#act
    	output = sum_nums(num1,num2)
    	expected = 3
    	#assert
    	assert output == expected
    ```
    
    **NOTE** that the tests above are arranged in a #arrange #act #assert format. This is the standard format for writing tests because it makes testing function clear and interpretable
    
5. Run the example test file from the command line. First navigate to the directory with the test file (`demo_tests` in this case). Then run the command below to run our test file:

```bash
pytest test_example.py -v
```

The -v (verbose) option is useful for viewing more information about the tests

After running this in the command line, you should see output similar to below:

```markdown
===================== test session starts ============================
platform darwin -- Python 3.9.12, pytest-7.1.1, pluggy-1.0.0 -- /Users/
cachedir: .pytest_cache
rootdir: /Users
plugins: anyio-3.5.0
collected 2 items                                                                                                                                                                                                                        

test_example.py::test_sums_value PASSED                                [ 50%]
test_example.py::test_sum_nums_output_type PASSED                    [100%]

====================== 2 passed in 0.06s =============================
```

As you can see, PyTest runs through all of the tests in the `test_example.py` file and outputs results that show whether each test was completed successfully

For more examples and additional PyTest concepts, see the [PyTests Concepts](Key%20PyTest%20Concepts%20f3382de7d2ad42e988423c77848c1f47.md) page