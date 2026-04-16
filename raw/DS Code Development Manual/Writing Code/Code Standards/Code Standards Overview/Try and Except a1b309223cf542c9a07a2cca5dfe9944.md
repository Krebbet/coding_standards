# Try and Except

Level: Class, Function, Library
Constraint: Medium
Last Edit: June 12, 2024 4:02 PM
Project Types: GA, MVP, POC

As a general rule- avoid try and except blocks as much as possible.

They can often be avoided by using a lbyl (look before you leap) style.

Exceptions generally tend to hide bugs in code- preventing them from being found and fixed.

If you absolutely must use an exception

1. Reconsider - You probably don’t have to
2. Make it very specific- bare exceptions should never be used. More general errors like ValueError are alright- but this is still very broad. Many exceptions may be caught by this. The ideal case is to use very specific exceptions like `requests.exceptions.Timeout` from the request library. The libraries you are using likely provide some exceptions like these, and if not you may consider creating your own.
3. Don’t handle it silently. Add some logging to make it clear that code errored out, that way someone may find the root cause in the future and find a way to factor out the exception handling.

For example

```python
# Really bad (Extraneous code in try, bare exception, unneeded exception)
try:
	item = foo(bar)
	return item[0]
except:
	return None

# Bad (Bare exception)
item = foo(bar)
try:
	return item[0]
except:
	return None
	
# Bad but Better (Specific Exception)
item = foo(bar)
try:
	return item[0]
except IndexError:
	return None
	
# Good (Look before you leap, no exception needed)
item = foo(bar)
if len(item) == 0:
	return None
else:
	return item[0]
	
```