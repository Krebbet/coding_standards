# Comments & Code Line Breaks

Level: Documentation, Function, Library, Notebook cell
Constraint: Hard
Last Edit: October 26, 2023 2:22 PM
Project Types: GA, MVP, POC

## Check Comment Order and Justification

Comments should come before or on-line with the code, explaining context prior to the reader interpreting whatever's going on. Cells generally shouldn't end with code on its own line(s) - when explaining something without attached code, use markdown for greater readability.

Related portions of code should be grouped with a comment at the start and a line break between sections. 

**Bad:**

```python
Comments after code
df["known_range"] = pd.to_timedelta(df["known_range"])
df["known_range_days"] = df["known_range"].apply(lambda x: x.days)
df_test["foo"] = df_test["bar"].apply(lambda x: adjust(x))
df_test["foo2"] = df_test["bar"].apply(lambda x: do_something(x))
# convert our "client relationship lifetime" column into a Pandas timedelta
# then extract the days for later graphing

Bad indentation
# originally subsetted the data to 'called', and sometimes 'called' | 'prescribed' for graphing/slides
	# df_sub = df[df['total_calls']>0]
	# for our complete-data-merging pipeline, no need to do that until training data selection

Use of hashtag in docstring 
def foo(x):
	# docstring here using hashtag not recommended
	...

Poorly indented docstring 
def foo(x):
	""" LOREM IPSUM...
LOREM IPSUM
1. LOREM IPSUM
2. LOREM IPSUM
"""
	...
```

In the above example, the comments are *multifariously* bad: 

- the explanations come after the code
- commented code is leftover
- explanations for leftover commented code are included
- the explanations are ultimately extraneous, informally phrased and could be done in markdown
- the test dataframe might be better separated into a separate portion
- multi-line comments should be on the same justification
- single-line docstrings should still use the triple double quotes.

**Good:**

Move relevant comments before the code, trim the irrelevant parts and give the code some space!

```python
Multiline well indented comment
# convert "client relationship lifetime" to a pd.timedelta and 
# extract days for graphing
df["known_range"] = pd.to_timedelta(df["known_range"])
df["known_range_days"] = df["known_range"].apply(lambda row: row.days)

Short and relevant comment to add info
# manipulate date for XYZ
df_test["foo"] = df_test["bar"].apply(lambda x: adjust(x))
df_test["foo2"] = df_test["bar"].apply(lambda x: do_something(x))

Short and relevant comment to add info
def foo(x):
	""" use triple quotes even for one-liner """
	return bar 

Nicely indented comments aids reading quality
def foo(x):
	""" 
		LOREM IPSUM...
		LOREM IPSUM
			1. LOREM IPSUM
			2. LOREM IPSUM
	"""
	...
```