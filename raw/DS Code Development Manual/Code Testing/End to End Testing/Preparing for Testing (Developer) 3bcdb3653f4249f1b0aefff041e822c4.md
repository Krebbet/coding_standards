# Preparing for Testing (Developer)

## Developer Responsibilities

The developer bears most of the responsibility for testing, particularly:

- Setting the pace for testing. The developer will communicate with the tester to ensure testing takes place within a timeframe that is appropriate given the size of the project and the amount of time allotted in the contract.
- The application working
    - You're the project entomologist! 🐞
    - Keep an eye on the appropriate GitHub channel in Slack, as the tester will open GitHub issues for anything that needs to be changed
    

## Documentation Template

The SFL-Template GitHub repository contains a template README.md that can be used as a starting place for creating good documentation.

[](https://github.com/SFLScientific/SFL-Template)

## Micro Dataset

You should create a small dataset to be used when a code reviewer runs your code end-to-end. This can either be synthetically created with fake data or can be a tiny subset of (anonymized) data from the original dataset. This should be stored in your `data` folder to be used as normal when someone attempts to execute the entire pipeline.  This helps reduce the total amount of time spent testing.

The micro dataset should have enough data to run all functionalities. E.g., if your data split function has `test_size=0.1` by default, your dataset should have at least 10 records to make sure the test set is not empty. 

## Preparation Checklist

The below checklist can be used to guide a developer's preparation for testing. Please make a copy and go through each item. If any part doesn't seem to apply to your particular project, use your best judgment in deciding whether to skip it or otherwise augment the list. In either case please provide feedback to the PM or TL. 

[Dev E2E Prep Checklist](https://docs.google.com/spreadsheets/d/10bQfVaYiCu8kwM_-J_QAC2LJfDDe9JB4v9_d0PVxPRY/edit?usp=drivesdk)