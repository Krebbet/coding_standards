# Design Checklist

These guidelines are less cut-and-dry compared to the Code Guidelines, but are crucial to delivering a streamlined and easy-to-read repository to your teammates or the client. It is encouraged to reference this page before and during code review, and check every box before any client delivery.

- Tip on using this page (click to expand)
    
    To practice consistent employment of the guidelines in this page (and to use checkboxes interactively), right click this page on the left sidebar, `duplicate` it and then `move` the duplicated page to your private Notion workspace.
    

The developer should ensure that their code meets several expectations, grouped into broad categories here.

## Naming and Documentation

The code should be accompanied by clear and useful documentation and docstrings which enable users and code reviewers to quickly understand what the code does and how it works.

- [ ]  Is the README well-documented? Does it give a good overview of the entire program? Can a naive user understand the purpose of the code and how to run it? Does the sequence of sections match the flow of how things should be run? Will someone not involved in the project understand how to run the code and what the expected output should be.
    - [ ]  Consider using the `tree` command to generate a filetree for the README.
- [ ]  Does the README include explicit instructions for installation? Is the “requirements.txt” file  included (ideally) in the base directory of the repository, along with the install commands?

## Remove Vestigial Code, Folders, & Data

All folders, scripts, and as much as possible code in the scripts should be cleaned to include only what is explicitly necessary for the release. 

- [ ]  Are all temporary files and folders including data etc removed from the repo?
- [ ]  Are all data files handled appropriately? Generally, data should not be included in the repository. The README should include instructions for how data should be imported or loaded.

## Functionality and Complexity

The code should be functional, and structured so that it reduces complexity. 

- [ ]  Can the code be made simpler? Is it well understood without much effort from the code reviewer, user, or future developer? Ideally, the code should be simple enough so that comments are unnecessary.
- [ ]  Does the code behave as intended? Code for projects that are more than 4-weeks long should be accompanied by tests that are correct, well-designed and that consider edge cases. See [here](https://www.notion.so/4481ab0262774980871785a277ffa4ff?pvs=21) for details on testing.
- [ ]  Functions should be written as modular as possible. Functions should do one thing and do it well**.**
- [ ]  Nothing within a function should be hardcoded. Instead, functions should have default values for such variables.
- [ ]  Any piece of code that repeats more than once in the program should be made into a function.

## Style and Design

The code style and design should facilitate its review and use.

- [ ]  Are requirements in the coding-guidelines followed?
    
    [Code Standards](Code%20Standards%20bb20e0f77b0f4d52b8d416a19ef1699b.md)
    
    [DE Code Guidelines](https://www.notion.so/DE-Code-Guidelines-01bfcffdfea5433e9fe08c3c426ad839?pvs=21) 
    
    [Design Philosophy](Design%20Philosophy%203def412c5fc644da9e067f9b614ea616.md)
    
- [ ]  Does the code follow [PEP8](https://www.python.org/dev/peps/pep-0008/) guidelines?
- [ ]  Use SFL’s code formatters (integrated in the [SFL-Template](https://github.com/Deloitte/SFL-Template/)) to ensure the code meets our formatting requirements. If the code does not meet the formatting requirements, it will not be committed.
- [ ]  Organize functions into separate packages (e.g., utility functions, feature engineering functions, etc.) to keep the main script short.

## PR Guidelines

The PR should have characteristics that encourage a quick and efficient code review process, allowing continued coding and reducing complexity for any required rollbacks (see [Pull Request Standards](../Version%20Control%20(Git%20&%20GitHub)/Pull%20Request%20Standards%204c2301249b194eb7970eab97105d5db6.md) for more information).

- [ ]  The first PR of a project should clean up the repository (include files on gitignore, clean up parts irrelevant to the project, etc) and give enough information about the project on the README. Subsequent PRs will build on the information in the README by accompanying code with any updates to the documentation.
- [ ]  The PR should ideally have 50-200 line changes (or 50-300 after SFL’s automated formatter is run)
- [ ]  The PR should be a small unit of code but not be so small that its implications are difficult to understand. Try to include some context on how the code will be used so that code reviewers can also assess the code's functionality. Keep related code in the same PR.
- [ ]  Functionally different parts of the project should be developed on different branches and pushed to different PRs. Having self-contained portions allows continued coding while PR reviews are pending.
- [ ]  Developers should ensure that projects that are more than 4 weeks long should include [unit tests](https://www.notion.so/4481ab0262774980871785a277ffa4ff?pvs=21) in the PR.
- [ ]  Generally, code should be pushed with complete functions. However, if a code HAS to have a placeholder function, it should be clearly identified with a FIXME or TODO comment.
    - Example (click to expand)
        
        E.g., when waiting on client to provide data that has to be combined with other open-source data, it may be easier to write an empty function returning an empty dataset that can be integrated into the full pipeline while waiting. The FIXME fields can then be changed later when data is available.
        
- [ ]  The commit message should include information about *what* change is made and *why* it is made.
- [ ]  **TODO** and **FIXME** comments are allowed during development, and we recommend migrating these to GitHub’s Issue Tracker during the PR process. Please refer to our issues standards for proper tracking
    
    [Issues Standards](../Version%20Control%20(Git%20&%20GitHub)/Issues%20Standards%2090450972bfa8482bafbe16a424105b99.md)