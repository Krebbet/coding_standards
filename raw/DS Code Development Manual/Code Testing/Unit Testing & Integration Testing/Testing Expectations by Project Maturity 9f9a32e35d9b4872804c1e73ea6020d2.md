# Testing Expectations by Project Maturity

# Overview

This page describes the general expectations for code testing at each of the three primary maturity stages.

# Table of Contents

# Proof of Concept (PoC)

**Project Time**: up to 4 weeks 

**Common Testing Tools**:  PyTest  

**Reference Projects**: . [TODO: collect and link to projects ]

### Unit Tests

No unit testing is required, but module level/integration tests are highly encouraged.  

### Integration Tests

Integration tests of higher level modules will ensure that the code overall runs as expected, tests can reveal bugs before code delivery. 

Integration testing can also reduce transition cost of PoC projects to other developers, or to the next phase of maturity. 

Consider integration testing for code pieces that interface with users or other programs.

Per client needs, additional testing may be necessary.

### E2E Testing

End to End (E2E) testing is required before delivery to the client. The focus of E2E should stay high level and run through the same motions the client will go through to run the code.  

### **Automation**

Github action will run all tests that are available. If tests are present, make sure they all pass. No other action will be required for a PoC.

# Minimum Viable Product (MVP)

**Project Time**: 4-12 weeks 

**Common Testing Tools**:  PyTest 

**Reference Projects**: [Framatome](https://github.com/Deloitte/framatome_LPO_USCitizens), 

### Unit Tests

- Essential units (Units that do the key processing for the algorithm)
    - If a bug pops up, fix the bug and write a unit test to check for that bug going forward!
    - Code that is a common source of bugs should include edge case testing.

### Integration Tests

Code that interfaces with users or other modules should be carefully tested. 

### E2E Tests

End to End (E2E) testing is required before delivery to the client. E2E testing scope for MVP should also include a thorough review of all the instructions, configurations, data sources, and other project dependencies. Surface level review of the code can also be included. 

### **Automation**

Github action will run all tests that are available. PRs will be approved only when all tests pass. Code reviewer can use the statistics from GitHub action to assess the completeness of testing on critical code.  There is hard limit on what % coverage is required. The code reviewer must use technical judgement to ensure that all critical functionality is covered by tests. 

# General Availability - Production (GA)

**Project Time**: more than 12 weeks 

**Common Testing Tools**:  pytest, mock tests, bandit (security checks), flake8 (pep8 and formatting), Tox (test with multiple python env), nose2 (same as pytest but has a few different module that can be helpful), pytest plugins ([pytest-api](https://pypi.org/project/pytest-api/), [pytest-django](https://pypi.org/project/pytest-django/), [pytest-ec2](https://pypi.org/project/pytest-aws/), [pytest-ec2](https://pypi.org/project/pytest-ec2/)… etc) 

**Reference Projects**: [TODO: collect and link to projects ]

### Unit Tests

100%* unit test coverage (Test all units that can be meaningfully tested.) 

### Integration Tests

Test all code that interfaces with internal and external modules/resources. 

### E2E Tests

End to End (E2E) testing is required before delivery to the client. E2E testing should cover all common user / client workflows, assess user interfaces, latency, and outside resource connectivity. 

### **Automation**

Github action will run all tests that are available. PRs will be approved only when all tests pass. Code reviewer can use the statistics from GitHub action to assess the completeness of testing for a GA project overall percentage should be high >70%.