# DRAFT Updated Code Practices

Still being finalized but this is a great resource for what to expecting working on SFL project and expectations!

# Code Practices

## Development

### 1. Understand Your Tools

- Learn to use your IDE’s debugger.
- Read the documentation to understand how something works.
    - Don’t rely solely on ChatGPT or copy-paste from Stack Overflow.
    - LLMs can confidently produce incorrect or subtly flawed solutions.
- GitHub Copilot can improve efficiency, **but**:
    - Turn it off when you need to deeply think through a problem.
    - Understand every single line of code it generates.
    - LLM-generated code can introduce subtle, hard-to-detect bugs.

### 2. Follow the Unix Philosophy

Adhere to the principles of the Unix philosophy:

- Write programs that do one thing well.
- Build small, composable units.
- Favor simplicity and clarity over cleverness.
- Design for interoperability.

Reference: [https://en.wikipedia.org/wiki/Unix_philosophy](https://en.wikipedia.org/wiki/Unix_philosophy)

---

## Organization

### Branch Naming

- Branch names must start with the ticket number.
- Format:
    
    ```
    fave-123_add_foo_bar
    ```
    

### Commit Messages

- Commit messages must start with the ticket number in brackets.
- Format:
    
    ```
    [FAVE-123] Add foo bar functionality
    ```
    

---

## Pull Requests (PRs)

- All automated tests must pass before:
    - Requesting a review
    - Merging a PR
- The developer who opened the PR is responsible for merging it.

---

## Code Reviews

- All code must be reviewed.
- If requesting a non-trivial change, the reviewer **must provide a code snippet**.
    - Avoid vague suggestions.
    - Reviewers should share responsibility for proposed changes.
- Refactors must not include logic changes.
- Logic changes must not include refactors.
- Use “we” instead of “you” in review comments.
    - Reference: [https://mtlynch.io/human-code-reviews-1/#never-say-you](https://mtlynch.io/human-code-reviews-1/#never-say-you)

---

## Testing

### General Principles

- TEST YOUR CODE.
- If it’s hard to test, that’s a design smell.
    - The unit likely needs to be broken up further.
- If something is difficult to test, your code may be too complex.

### Unit Test Structure

Use the **Arrange–Act–Assert** pattern:

1. **Arrange** – Set up the test data and dependencies.
2. **Act** – Execute the behavior under test.
3. **Assert** – Verify the expected outcome.

Reference:

[https://automationpanda.com/2020/07/07/arrange-act-assert-a-pattern-for-writing-good-tests/](https://automationpanda.com/2020/07/07/arrange-act-assert-a-pattern-for-writing-good-tests/)

---

## Design Principles

### Abstraction

- Abstraction barriers are your friend.
- Keep implementation details hidden behind clear interfaces.

### Coupling

- Avoid tightly coupled units.
- Favor loosely coupled, composable components.

### Complexity

- Cyclomatic complexity should be as low as possible.
- Prefer simple, readable logic over dense or clever code.

### Data Structures

- Dictionaries are for mappings, not general-purpose data storage.

[SFL Software Design Principles](DRAFT%20Updated%20Code%20Practices/SFL%20Software%20Design%20Principles%2031922bab7e31802193e9dbe68aef56c2.md)