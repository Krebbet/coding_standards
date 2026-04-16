# Avoid Unnecessary Conditionals

Last Edit: April 5, 2023 6:26 PM
Level: Soft

## Avoid Unnecessary Conditionals

Else statements are sometimes redundant when returning from functions.

**Bad:**

```python

#
# if input==False, 'fish' is returned regardless of the else
#
def purchase(crab_in_stock):
	if crab_in_stock == True:
		return "crab"
	else: 
		return "fish"

#
# horizontal scaling of if statements as more choices added
#

# pipeline creation 
if model_selection == "adaboost":
	pipeline = Pipeline(
		["ab", sklearn.ensemble.AdaBoostRegressor()] 
	)
elif model_selection == "random forest":
	pipeline = Pipeline(
		["rf", sklearn.ensemble.RandomForestRegressor()] 
	)
```

**Good:**

```python
# remove the redundant else line
def purchase(crab_in_stock):
	if crab_in_stock == True:
		return "crab"
	return "fish"

#
# to save redundant lines, use dictionary storage
#
models = {
    "adaboost": sklearn.ensemble.AdaBoostRegressor(),
    "random forest": sklearn.ensemble.RandomForestRegressor(),
    "gradient boosting": sklearn.ensemble.GradientBoostingRegressor()
}
model_selection = "gradient boosting"
...

# pipeline creation 
pipeline = Pipeline(
	[ (model_selection, models[model_selection]) ]
)
```

Python now also supports case statements as of [PEP 636: Structural Pattern Matching.](https://www.python.org/dev/peps/pep-0636/)