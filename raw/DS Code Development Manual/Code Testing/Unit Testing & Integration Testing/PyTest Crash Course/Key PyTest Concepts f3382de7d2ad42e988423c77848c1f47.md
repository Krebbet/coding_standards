# Key PyTest Concepts

This notebook reviews a few more advanced unit testing concepts in PyTest that are important when writing robust and scalable unit tests in practice.  The main concepts covered include parameterization, fixtures, mocking, and combining tests into classes

### Parameterized tests

In the basic PyTest example in the [Unit Testing Basics](Unit%20Testing%20Basics%20ed9241c70eee4bb99b56d4bb6edbd1f9.md) page, we defined a simple function `sum_nums` and we hard-coded the test case within the testing function (`assert sum_nums(1,2) == 3`). A more robust approach is to create more generalized testing functions that take in the inputs and the expected outputs for the feature that is being tested. 

PyTest has a built in wrapper function that makes it easy to parameterize the arguments of a test function:

[`@pytest](http://twitter.com/pytest).mark.parametrize(argnames, argvalues)`

- `argnames` - A comma separated string with the name for each argument that will be passed to the test function. These names should correspond with each variable name in the test function
- `argvalues` - A list of tuples, where a tuple specifies a list of values corresponding to the `argname` list, and each tuple specifies a unique test case

**EXAMPLE:**

Given the following function:

```python
# In example.py file
def sum_nums(num1, num2):
	return num1 + num2
```

We can write a generalized`test_sum_nums` function that takes in the inputs and the corresponding expected output of the `sum_nums` function as parameters:

```python
def test_sum_nums(num1, num2, expected):
	"""This test checks if the sum function returns the correct value"""
	#act/arrange
	output = sum_nums(num1, num2)
	#assert
  assert output == expected
```

We then add our wrapper function [`@pytest](http://twitter.com/pytest).mark.parametrize` to specify the values that will be passed to our `test_sum_nums` function when the test is run. 

```python
@pytest.mark.parametrize('num1, num2, expected',[(3,5,8),(-2,-2,-4), (-1,5,4)])
def test_sum_nums(num1, num2, expected):
	"""This test checks if the sum function returns the correct value"""
	#act/arrange
	output = sum_nums(num1, num2)
	#assert
  assert output == expected
```

Here, our `argnames` input specifies `‘num1, num2, expected’` corresponding to each of the inputs to our `test_sum_nums` function. Our `argvalues` input specifies 3 different test cases, each with 3 values corresponding to the inputs to the `test_sum_nums` function. For example, the first test case `(3, 5, 8)` specifies `num1 = 3, num2 = 5, expected = 8`

This test can be run as above with the following command:

```markdown
pytest test_example.py -v
```

Which should return the following output:

```python
============================ test session starts ============================
platform darwin -- Python 3.9.12, pytest-7.1.1, pluggy-1.0.0 -- /Users/
cachedir: .pytest_cache
rootdir: /Users
plugins: anyio-3.5.0
collected 3 items                                                           

test_example.py::test_sum2[3-5-8] PASSED                              [ 33%]
test_example.py::test_sum2[-2--2--4] PASSED                           [ 66%]
test_example.py::test_sum2[-1-5-4] PASSED                             [100%]

============================= 3 passed in 0.03s ============================
```

Here, we see that each test case that we specified in `argvalues` returns a test result

### Fixtures

Test fixtures can help to provide a baseline upon which tests can be reliably and repeatably executed. Fixtures can be used to share test data between tests, and execute setup and teardown methods before and after test executions respectively.

PyTest has a built in wrapper function that makes it easy to establish fixtures in testing code:

[**`@pytest](http://twitter.com/pytest).fixture`**

EXAMPLE:

We can use a fixture to perform the test cases that we defined in the section above:

We first define the fixture function, which in this case outputs the test cases:

```python
@pytest.fixture
def get_sum_nums_test_data():
        return [(3,5,8), (-2,-2,-4), (-1,5,4)]
```

We can then define our test function, which will take in the fixture as an argument and run  all of the test cases defined by the fixture:

```python
def test_sum_nums(get_sum_test_data):
        for data in get_sum_nums_test_data:
								#Arange
                num1 = data[0]
                num2 = data[1]
                expected = data[2]
								#Act
								output=sum_nums(num1, num2)
								#Assert
                assert output == expected
```

When this test is run, the output should look like this: 

```markup
============================ test session starts ============================
platform darwin -- Python 3.9.12, pytest-7.1.1, pluggy-1.0.0 -- /Users
cachedir: .pytest_cache
rootdir: /Users/
plugins: anyio-3.5.0
collected 1 item                                                            

test_example.py::test_sum PASSED                                      [100%]

============================= 1 passed in 0.06s =============================
```

Notice that there is only one output in this case because our test function is only run once with the single fixture function as input

### Mocking

Mocking is a technique that can help to isolate particular operations in a given piece of code (e.g. a function) from other dependencies.  In essence, mocking allows you to replace parts of your system under test with mock objects and make assertions about how they have been used 

One particularly useful way to utilize mocking is to “force” the output of sub-functions within the function that you are looking to test, so that your test is only testing the particular operations within the function of interest and not the behavior of the sub-funciton

EXAMPLE:

Say we create a new function `is_sum_negative` which determines if the sum of two numbers is negative using our `sum_nums` function:

```python
def is_sum_negative(num1, num2):
    sum_numbers=sum_nums(num1, num2)
		if sum_numbers>=0:
			return False
		else:
			return True
```

To test this function, we want to isolate the operations that are performed by the `is_sum_negative` function from the operations that are performed by the `sum_num` function (we have already tested our `sum_nums` function above). To do this, we can use mocking

PyTest makes mocking very easy via the built in `mocker` function:

```python
def test_is_sum_negative_true_output(mocker):
        """Assert that is_sum_negative returns True if sum is negative"""

        mocker.patch("example.sum_nums", return_value=-2)

        #arrange
        num1=0
        num2=0

        #act
        output = is_sum_negative(num1, num2)
        expected = True

        #assert
        assert output == expected
```

Here, we are basically forcing the output of the `sum_nums` function to be -2 so that we can test the functionality of `is_sum_negative`, independent of the functionality of `sum_nums`

Running this test results in the following output:

```markup
======================= test session starts =======================
platform darwin -- Python 3.9.12, pytest-7.1.1, pluggy-1.0.0 -- /Users
cachedir: .pytest_cache
rootdir: /Users
plugins: anyio-3.5.0, mock-3.10.0, cov-4.0.0
collected 1 item                                                  

test_example.py::test_get_hypotenuse_negative_input PASSED  [100%]

======================== 1 passed in 0.03s =======================
```

#