# Code Testing Best Practice

# Statements of Purpose

1. Code Testing Best Practice document is a starting point framework for ML engineers  and developers. 
2. This is a living document, users should contribute and mature the document into a comprehensive guide for Code Testing.  
3. Business Development (BD) team can use it as a reference for planning and negotiating contracts with clients. Specifically [Client Interactio](Code%20Testing%20Best%20Practice%205c01a436f6c240ba8f027d35cba90261.md)n section will be useful for BD team. 
4. The standards defined in this document are meant to raise the overall code quality and development efficiency of the organization.
5. The best practices are grounded in the SFL use-case, and should continuously be assessed for overall utility to the organization. 
6. Breaking through all the verbosity, testing must stay laser focused on client’s use case. 

# Axioms

- Not all code needs to be production code
- All production code is unit tested
- Testing improves code quality, speeds development, and reduces bugs
- If your code is hard to test, your code is bad.

# Definitions

**Units Tests** - cover an individual unit of logic, and ensure that it works as expected. For example, a single function may need several unit tests for each unit of logic or transformation it performs.

**Integration Tests** - cover module level functionality and ensure that modules interface/integrate properly. For example, a class can be tested with integration tests.  

**End to End (E2E) Testing** - User testing of the application overall. 

# Client Interaction - Conversations Before Code

The stakeholders of SOW and contracts should understand well client needs and set clear expectations for product maturity. Here are a few points that can guide a well scoped product testing strategy. 

1. How will the code be used ?
2. What are the expectations for product maturity (PoC, MVP, GA)? 
3. What are client’s testing needs ? Internally, we have a minimal code maturity and testing expectations, however the client may require a POC product to be thoroughly tested. 
4.  What are client’s testing best practices (if they have any) ?
5. What are runtime/latency requirements ? These can be included in tests as continuous checks on meeting the latency requirements. 

# Code Testing Minimum Proficiency

Developers should at a minimum: 

1. Be familiar with the pytest library and be able to write a simple pytest. 
2. Be familiar with pytest fixtures 
3. Able to run tests from terminal or IDE
4. Write at least one integration test for any given project. 
5. Ensure the tests run on GitHub actions 
6. Read through Coding Best practice and continue advancing their code quality and testing knowledge 

# General Best Practices

See [Testing Expectations by Project Maturity](Testing%20Expectations%20by%20Project%20Maturity%209f9a32e35d9b4872804c1e73ea6020d2.md) for maturity-based expectations.

### ML Model Testing

There are many ways of assessing a model. Some approaches are general, but some are modeling domain specific. Below are some of the more common model tests methods, as a starting point survey of common tests.  

**Functionality Testing**

Functionality tests assess model architecture robustness, input / output expectations, and overall functionality of the model. For example: 

- Test model compilation by assessing model characteristics (layers, number of parameters …)
- Test model inference for expected outputs,  shapes, probability sums (sum = 1 along axis -1)
- If the model is composite, you can test sub-models.
- Test model by feeding expected or unexpected inputs types/shapes…

**Directional/Perturbation Testing (DT/ PT):** 

Directional tests assess model behavior with a slight but controlled variation in input. DT test the model on different known inputs and know expected behaviors. The behaviors are not always well defined. For example, a well defined behavior for an NLP model the sentiment of a sentence when negative words are introduced. A not well defined behavior example is when introducing certain perturbation to an image. 

**Invariance Testing:** 

Tests model robustness to changes that are not suppose to affect the outcome. For example, in computer vision, changing image brightness, rotation, or slight distortions should not change object detection/labeling. Another example for NLP, a synonym in a sentence should maintain the general sentiment of the sentence. 

**Bias Testing:** 

Bias testing can be partially covered by directional or invariance testing. Because unchecked models can easily display biases from underlining datasets, focused bias testing is critical especially for models that deal with social/demographic/biographic data. 

Bias testing starts with data, where categories and labels are well distributed among each demographic feature. 

For NLP, bias test can assess model predictions when names, or any other demographic marker, are changed but the main content of the text remains the same. 

For computer vision, labeling accuracy can be assessed with data from underrepresented topics. 

**Other Tests:** 

There are many other testing methodologies that will eventually be developed in this guide. Right now here are some honorable mentions: performance drift, perturbation tests, robustness to noise, adversarial attacks, fairness, logical consistency, and explainability tests. 

### Data

- Generate random data as much as possible during testing
- Avoid heavy data for unit testing needs
- Prevent stored data needs for unit tests
- Units tests should be able to run on GitHub actions and other automation frameworks, not just on the local machine.

### Directories

- Use relative path designations
- Organize all test (test_<file_name>.py) in test folders  at the same level as your src folder, or your main package, so the code can be imported with relative imports. Your project will mirror the following directory tree:

```jsx
project/
│
├── my_app/
│   └── __init__.py
│
└── tests/
    |
    ├── unit/
    |   ├── __init__.py
    |   └── test_sum.py
    |
    └── integration/
        ├── __init__.py
        └── test_integration.py
```

- During testing, if new files or directories are created, always cleanup and remove all content generated during testing. Many helpful tools to help cleanup

### Master Mock Tests

PyTests and UnitTests have ‘mock’ packages that allows for quick mocs of OS, backend, AWS lambdas… etc. 

Moc tests are useful when the testing feature interacts with an outside resource, and it’s not possible to easily spin up or connect to the resource. 

See the [sql-code-testing-best-practices](https://github.com/Deloitte/sfl-code-testing-best-practices) repo for moc test examples.

### Imports

- relative imports should be used when working with packages and GA projects
- If paths must be appended to sys, use the true file path to avoid source directory issues: sys.append(Path(__file__).parents(x))

# References

[https://github.com/Deloitte/sfl-code-testing-best-practices](https://github.com/Deloitte/sfl-code-testing-best-practices)