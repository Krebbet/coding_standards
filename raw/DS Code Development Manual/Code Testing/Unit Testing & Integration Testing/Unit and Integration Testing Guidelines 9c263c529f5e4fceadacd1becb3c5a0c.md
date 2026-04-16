# Unit and Integration Testing Guidelines

[Types of Tests](Unit%20and%20Integration%20Testing%20Guidelines%209c263c529f5e4fceadacd1becb3c5a0c.md) 

[On the importance of testing](Unit%20and%20Integration%20Testing%20Guidelines%209c263c529f5e4fceadacd1becb3c5a0c.md)

[When to write tests](Unit%20and%20Integration%20Testing%20Guidelines%209c263c529f5e4fceadacd1becb3c5a0c.md)

[How to write tests](Unit%20and%20Integration%20Testing%20Guidelines%209c263c529f5e4fceadacd1becb3c5a0c.md)

# Types of tests

Testing can be divided into two categories, **unit testing** and **integration testing**. 

A **unit test** verifies the internal consistencies of a small piece of code, making sure it is doing what it is intended to do. Unit tests are narrow in scope and should not have any dependencies on outside system. 

An **integration test** verifies that different pieces of the system work together. Integration tests should cover the entire pipeline of a given project and should catch code changes that will break the flow of the program. 

# On the importance of testing

Test driven development and writing tests are beneficial in many ways, some of which are listed below:

1. Tests allow the developer to make big changes to the code without feeling the anxiety of:
    - Did the change break the functionality of the code? **(unit tests)**
    - Did the change break the overflow of the code? **(integration tests)**
2. Unit tests will allow other developers who might need to tweak the code to do so without the anxiety of changing the intended functionality of the code. 
3. Writing unit tests forces the developer to write more modular code. Having to write unit tests pushes the developer to write code that performs only 1 or 2 'testable' tasks.
4. Testing increases the chances of catching a bug in the code before building other code blocks on a potentially buggy code.
5. Testing is code documentation. Good tests act as documentation for other developers to read and understand the functionality of the code. Additionally, unit tests facilitate other developers to catch potential bugs in the code by suggesting untested edge cases.
6. Tests help with code re-use. Migrating both the code and the tests to a new project will fasten the migration process. 

---

# When to write tests

Keeping the aforementioned benefits in mind, it is important to understand that unit/integration tests are a tool, and like any other tool, should be used where the benefits outweigh the cost. Writing **unit tests** for each functionality or **integration tests** for each pipeline may not be possible and/or a waste of time. **The best way to determine which functions require testing, we suggest a discussion with your team and the team lead**. 

Please consider the following when deciding whether to write a test for a given functionality:

1. If a developer believes that **a function is too simple to be worth a unit test**, then it should not take too much time to write a unit test for such a function. Thus, we encourage unit tests for such functions.
2. If a developer believes that a function is **too complex to write a unit test for,** this might be a good indication that the method should be divided up into several more modular functions that can be unit tested. Usually, these types of complex methods performs several tasks such as read a dataset, perform multiple operations on the dataset, and potentially write the output to a file. Such methods should be refactored into multiple methods, each of which is responsible for a single task. In such cases, it is expected that methods that are responsible for reading/writing data will not be tested. 
3. In the development process, each manually found a bug, aka overlooked by the performed tests, should be reported to the team and the team should discuss how the testing can be improved to catch such bugs.

As there is no single criteria/equation that can determine what functionalities require testing, the team, especially **the team lead is given the responsibility to determine when to require testing**. As it is possible to write too little testing, it is also possible to write too much testing. Thus, as a rough guideline, SFL Scientific recommends that **a developer should spend 25 to 30% of their time on writing tests**. 

# Follow up on manually found bugs

In the development process, each manually found a bug, aka overlooked by the performed tests, should be reported to the team and the team should discuss how the testing can be improved to catch such bugs.

If a bug is manually found in a function for which the developer and the lead believed that writing units tests were not needed and/or arduous, the team should determine whether writing more comprehensive unit tests could have caught the bug. If the answer is yes, then the team should adjust the criteria by which the criteria for requiring tests are decided.

# How to write tests

A good test documents the developer's design and makes it easier to refactor and expand the code while retaining a clear overview of each component’s behavior. The distinction between a unit test and an integration test is very clear. 

Unit tests contain **a lot of knowledge** about **a single unit of code** and **zero knowledge** about **other parts of the codebase**. A change in a *function X* should NEVER affect the result of the unit test for *function Y*. On the other hand, integration tests contain **no knowledge** about different units of the codebase and whether each function does its job properly, but instead **evaluates how the entire system behaves together** to achieve the expected goal. 

![Figure is taken from [https://blog.stevensanderson.com/2009/08/24/writing-great-unit-tests-best-and-worst-practises/](https://blog.stevensanderson.com/2009/08/24/writing-great-unit-tests-best-and-worst-practises/)](Unit%20and%20Integration%20Testing%20Guidelines/Screen_Shot_2020-07-14_at_1.50.41_PM.png)

Figure is taken from [https://blog.stevensanderson.com/2009/08/24/writing-great-unit-tests-best-and-worst-practises/](https://blog.stevensanderson.com/2009/08/24/writing-great-unit-tests-best-and-worst-practises/)

## How to write unit tests

Properties of a good unit test:

1. **Easy to write**. Each unit test should be developed without enormous effort. If this is not the case, please [**see](Unit%20and%20Integration%20Testing%20Guidelines%209c263c529f5e4fceadacd1becb3c5a0c.md).** 
2. **Readable**. A unit test should be outlined such that the aspects of the code being tested are clear. Remember to always write the test so that a different developer can understand it in little to no time.
3. **Reliable**.  An example of an **UNRELIABLE** test would be a series of tests that succeed when run separately but fail when run together, or vice-versa. The tests should **ONLY** fail when there is a bug in the function being tested, or in the unit test. 
4. **Independent**. Each test should be a stand-alone piece of code that does not depend on any other functions. A unit test should **NEVER** fail because of a change in a completely unrelated function. 
5. **Fast**. Unit tests should be fast to run and should be run each time a change is made to the codebase. 

### Example

```python
def get_time_of_day():
    import datetime
    time = datetime.datetime.now()
    if time.hour >= 0 and time.hour < 6:
        return "Night"

    elif time.hour >= 6 and time.hour < 12:
        return "Morning"
    elif time.hour >= 12 and time.hour < 18:
        return "Afternoon"
    return "Evening"
```

The code snippet given above shows a BAD example for testing purposes because it would not be possible to write a unit test for this function given that the result of the function would depend on when the unit test is run. This function does not follow the single responsibility principle which states every module should have responsibility over a single part. It is instead responsible for both reading the current time and then making a decision based on that. This problem could easily be alleviated by passing the time as a variable instead

```python
def get_time_of_day(time):
    assert isinstance(time, datetime)
    if time.hour >= 0 and time.hour < 6:
        return "Night"
    elif time.hour >= 6 and time.hour < 12:
        return "Morning"
    elif time.hour >= 12 and time.hour < 18:
        return "Afternoon"
    return "Evening"
```

With this simple change, this code can now be easily unit tested. SFL Scientific encourages developers to keep 'testability' in mind when developing different functionalities.

There are different packages that can be used to write tests, such as [unittest](https://docs.python.org/3/library/unittest.html) or [pytest](https://docs.pytest.org/en/latest/). Given that unittest is easier to read as well as to get started and comes as a default package with Python, SFL Scientific recommends using the unittest package. Below is an example of a unit test for the [GetTimeOfDay](Unit%20and%20Integration%20Testing%20Guidelines%209c263c529f5e4fceadacd1becb3c5a0c.md) function.

```python
class get_time_of_day_test_case(unittest.TestCase):
    def setUp(self):
		    #Set up variables that are used by the test function
        self.time_to_test_1 = datetime.strptime(
            "8-10-2018 11:52:40", "%d-%m-%Y %H:%M:%S"
        )
        self.time_to_test_2 = datetime.strptime(
            "8-10-2018 13:52:40", "%d-%m-%Y %H:%M:%S"
        )
        self.time_to_test_3 = datetime.strptime(
            "8-10-2018 04:52:40", "%d-%m-%Y %H:%M:%S"
        )
        self.time_to_test_4 = datetime.strptime(
            "8-10-2018 23:52:40", "%d-%m-%Y %H:%M:%S"
        )

    def test_get_time_of_day(self):
        #Test the GetTimeOfDay function
	      #Please include all cases that can be retuned by the function
        self.assertEqual(get_time_of_day(self.time_to_test_1), "Morning")
        self.assertEqual(get_time_of_day(self.time_to_test_2), "Afternoon")
        self.assertEqual(get_time_of_day(self.time_to_test_3), "Night")
        self.assertEqual(get_time_of_day(self.time_to_test_4), "Evening")
        #Test the assertion
        with self.assertRaises(AssertionError):
            get_time_of_day(15)
        with self.assertRaises(AssertionError):
            get_time_of_day("ERROR")
```

It is highly recommended to write functions such that all the potential outputs and edge cases are tested. Please take a look at unittest [documentation](https://docs.python.org/3/library/unittest.html) for further usage details. 

For those who are interested in more advanced testing, please consider using the following packages:

- [coverage](https://coverage.readthedocs.io/en/coverage-5.2/): A tool for measuring code coverage of Python programs. It monitors your program, noting which parts of the code have been executed, then analyzes the source to identify code that could have been executed but was not. For unit tests, **a 100% coverage** is recommended. Coverage measurement is typically used to gauge the effectiveness of tests. It can show which parts of your code are being exercised by tests, and which are not. The example below shows the coverage per script in terms of total number of lines, total number of missed lines (lines that were not run), percent coverage, and a list of missed lines.

![](Unit%20and%20Integration%20Testing%20Guidelines/Screen_Shot_2020-07-20_at_4.39.41_PM.png)

- [mock](https://pypi.org/project/mock/): A library for testing in Python. It allows you to replace parts of your system under test with mock objects and make assertions about how they have been used (now part of the default python pa.

### Unit Test Example

When developing unit tests, a `test` folder should be created in the file structure which exactly mimics the structure of the src folder. For example: 

```markdown
**data**
	**raw**
		data_subset.csv (this is maybe a 5 row header of the data file?) 
	**processed** 
		.gitkeep
**src**
	**model**
		model.py
**test**
	**model**
		model.py (this tests the model.py file using the model_data.csv file?) 
		model_data.csv (fake data for the model.py unit test)?
```

Note that the `src` and the `test` folders have the same file structure, both containing the folder `model` which contains the respective [`model.py`](http://model.py) main and test files respectively. 

Code reviewers will run your `test/model/model.py` as black-box unit tests. Documentation should be provided in `test/model/model.py` to include all edge cases covered in unit test script `test/model/model.py`

A general guide for writing unit tests is that they are actively trying to break your code as much as possible. Most code will break with edge cases (e.g., all values in one single column are Nan or merge key is missing in one dataframe) and boundary values. To find edge cases in your program explicitly, tools like category partitioner will help you to develop black box unit tests.