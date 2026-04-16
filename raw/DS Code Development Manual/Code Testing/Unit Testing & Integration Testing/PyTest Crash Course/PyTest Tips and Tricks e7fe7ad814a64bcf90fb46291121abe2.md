# PyTest Tips and Tricks

# Running Tests from an IDE

PyTests can be run from an IDE or from terminal. Most IDEs have helpful interfaces that makes test running and debugging very useful. Behind the scene, IDEs run terminal commands. So when all else fails, you can always rely terminal commands. 

PyTest will look through the path you provided and will identify python files as tests files if they match the following format: test_*.py or *_test.py. 

## Terminal

```bash
pytest root_path # when tests are in a certain folder 
pytest . # when tests are in root directoy 
```

## Visual Studio Code

Declare your tester to be **pytest** (or unit test if you prefer) in the .vscode/settings.json file as shown below. The beaker tab will identify all your test files. You can run all the tests, run in debug mode (very useful), and see test status. The output tab in your terminal will display full test print out. VS may also do some fancy code overlay of the error to help you debug. 

 If the terminal window doesn’t display test console (output), then check if you have a dropdown on the furthest right of the terminal. If you do, then select Python Test. 

![Untitled](PyTest%20Tips%20and%20Tricks/Untitled.png)

![Untitled](PyTest%20Tips%20and%20Tricks/Untitled%201.png)

![Untitled](PyTest%20Tips%20and%20Tricks/Untitled%202.png)

## PyCharm

Same as for VS code, you will need to point your test running to PyTest. Then run as you typically run a script in python (the upper tab run button), or if you want to run individual tests you can click on the individual run marks right next to each test. You can also run tests in debug mode. 

![Untitled](PyTest%20Tips%20and%20Tricks/Untitled%203.png)

![Untitled](PyTest%20Tips%20and%20Tricks/Untitled%204.png)

# Other useful tips, tricks, and examples

python file names must include test and another tag as outlined below:

test_<test_name>.py

or

<test_name>_test.py

Import Structure edge c

```bash
ROOT = Path(__file__).parents[3]

sys.path.append(str(ROOT))

from notebooks.exploratory.utils import check_trunctation
```

Some runtime error will be shown as 

```bash
Test result not found for: ..._
```

 

make sure to go to your terminals, click on the output tab, and in the far right side, in the dropdown menu, look at the Python Test Log. 

Generally, you may need to toggle between Python and Python Test Logs to get a full account of some errors. 

# Simple Test (Starting Point)

```bash
from src.utils import special_function #or any other path to function/class

def test_special_function():
		expected = "expected function output"
    reponse = special_function()
    assert reponse == expected, "Optional string to printout when test fails"
```

# Experiment / Parametrized Tests

```bash
import pytest 
from src.utils import special_function #or any other path to function/class

@pytest.mark.parametrize(("input_a"), [0, 1, 2])
def test_special_function(input_a):
		expected = "expected function output"
    reponse = special_function(input_a)
    assert reponse == expected, "Optional string to printout when test fails"

# Same example but with multiple inputs 
@pytest.mark.parametrize(("input_a, input_b"), [(0,"str_val"), (1,"str_val2")])
def test_special_function(input_a, input_b):
		expected = "expected function output"
    reponse = special_function(input_a, input_b)
    assert reponse == expected, "Optional string to printout when test fails"

# Same example but with one input value and one expected value 
@pytest.mark.parametrize(("input_a, expected"), [(0,"val_0"), (1,"val_1")])
def test_special_function(input_a, expected):
    reponse = special_function(input_a)
    assert reponse == expected, "Optional string to printout when test fails"
```

# Test an Error is Raised as Expected

```bash
import pytest 
from src.utils import special_function #or any other path to function/class

def test_special_function():
	with pytest.raises(Exception): # accepts any other exception in .raises()
    special_function()
```

# Combine Tests into Classes

```python
# content of test_class_demo.py
class TestClassDemoInstance:
    value = 0

    def test_one(self):
        self.value = 1
        assert self.value == 1

    def test_two(self):
        assert self.value == 1
```

# Test Fixtures

Test fixtures are ‘helper functions’ that one or more may want to invoke. For example, if multiple tests require the same code, you can abstract that code into a fixture and just pass in the fixture as an argument to each test function. 

```python
# Arrange
from datetime import datetime
import pytest 
from src.utils import special_function #or any other path to function/class

@pytest.fixture
def generate_datetime():
	"""
	Fixture extracts current datetime and formats it as a string
	"""
	date_now = datetime.today()
	return date_now.strftime("%b %d %Y %H:%M:%S")

# with parameters 
@pytest.fixture
def convert_datetime_to_str():
	def _inner_method(date_now)
		"""
		To allow fixtures to accept parameters, 
		simply nest with an inner function.
		"""
		return date_now.strftime("%b %d %Y %H:%M:%S")
	return _inner_method

def test_a(generate_datetime):
	"for example, a function accepts a datetime str" 
	expected = "some expected value"
	current_datetime = generate_datetime()
	response = special_function(current_datetime)
	assert response == expected

def test_b(generate_datetime): 
	"another example, a function accepts a datetime str" 
	expected = "some expected value"
	current_datetime = generate_datetime()
	response = special_function(current_datetime)
	assert response == expected

def test_c(convert_datetime_to_str): 
	"Fixture with input parameters" 
	expected = "some expected value"
	date_now = datetime.today()
	current_datetime = convert_datetime_to_str(date_now)
	response = special_function(current_datetime)
	assert response == expected
```

# Fixtures with Classes

Very useful when you need to initiate objects on multiple occasions (test functions).

```python
import pytest

class Fruit:
    def __init__(self, name):
        self.name = name

    def __eq__(self, other):
        return self.name == other.name

@pytest.fixture
def my_fruit():
    return Fruit("apple")

@pytest.fixture
def fruit_basket(my_fruit):
    return [Fruit("banana"), my_fruit]

def test_my_fruit_in_basket(my_fruit, fruit_basket):
    assert my_fruit in fruit_basket
```

## Fixtures In Depth

[https://docs.pytest.org/en/7.1.x/explanation/fixtures.html](https://docs.pytest.org/en/7.1.x/explanation/fixtures.html)

[https://docs.pytest.org/en/7.2.x/how-to/fixtures.html](https://docs.pytest.org/en/7.2.x/how-to/fixtures.html)

## How To CookBook

[https://docs.pytest.org/en/7.2.x/how-to/index.html](https://docs.pytest.org/en/7.2.x/how-to/index.html)

## Anatomy of a Test

[https://docs.pytest.org/en/7.2.x/explanation/anatomy.html](https://docs.pytest.org/en/7.2.x/explanation/anatomy.html)

## Built In Fixtures

[https://docs.pytest.org/en/7.2.x/reference/fixtures.html#fixtures](https://docs.pytest.org/en/7.2.x/reference/fixtures.html#fixtures)

# Resources

[https://docs.pytest.org/en/7.2.x/getting-started.html](https://docs.pytest.org/en/7.2.x/getting-started.html)