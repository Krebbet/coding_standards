# GUI Testing Software

## Testing react based user interfaces

The official ReactJS website recommends using [Jest](https://jestjs.io/) and [React Testing Library](https://testing-library.com/docs/react-testing-library/intro) to create tests for applications. We recommend using these tools as well as Selenium IDE for integration/E2E tests.

## Recommended testing software

### [JEST](https://jestjs.io/)

Jest is a delightful JavaScript Testing Framework with a focus on simplicity.

- Runs out of the box without requiring any configuration (unlike Enzyme!)
- Provides snapshot tests which are popular and more lightweight than regular tests
- Tests are isolated and ran in parallel which avoids bugs from test leakage
- Made by facebook so can expect more long term viability

### [React Testing Library](https://testing-library.com/docs/react-testing-library/intro)

Ran alongside Jest (usually) and provides a lightweight interface to create tests that more accurately resemble a user interacting with the software

- Specifically designed as a replacement for enzyme
- Requires minimal setup

### [Selenium](https://www.selenium.dev/)

Selenium automates browsers, that's it!

- Useful for E2E and integration tests
- Selenium IDE allows you to create these tests through a GUI within your brows

## Other potentially useful resources

These resources are not recommended but may be useful if you end up needing more than what is provided by the recommended solutions. 

### [Sinon](https://sinonjs.org/)

Mocking and stubbing tool for js. Perhaps you have a loop that needs to run 1000 times for a component but you don't want to actually run the full thing each time. Sinon can wrap that function and run it once, then return that output for each subsequent call (mock the output). 

## When to write tests

All the time. Everyday. And in small doses. Automatic testing does not replace manually testing in the browser, but it will accelerate the testing process and alert developers to changes that inadvertently breaks another component.

Optimally, **unit tests** are written for each atomic function. When a component is defined, tests should be written in parallel. The tests can be written **before** the function itself to aid in debugging the component as it is developed (test-driven development). For example:

1. Define the inputs/outputs for the function or component.
2. Create a test that accepts the inputs and checks expected output
3. Implement the component.
4. Expand test inputs/outputs to include edge/null cases.

**Integration tests** rely upon the success of **unit tests**. Integration tests are more likely to fail because they are more complex. When integration tests fail, it indicates an incompleteness in the unit tests. When integration tests pass, it usually indicates unit tests are sufficient. 

## Writing Tests

[Unit Tests](GUI%20Testing%20Software/Unit%20Tests%20dd426b2096de42f0a4395c9851e39c8a.md)

[Testing Components](GUI%20Testing%20Software/Testing%20Components%20492b01f4cec348f8b00b7b02be3c316e.md)

[Integration/E2E Tests](GUI%20Testing%20Software/Integration%20E2E%20Tests%205f778119efaf4db0923678a399b0c04c.md)

## Solving Common Bugs

[Fantastic Bugs and How to Fix Them](GUI%20Testing%20Software/Fantastic%20Bugs%20and%20How%20to%20Fix%20Them%208a568a1349aa4f4dbe93ccb78dedb196.md)