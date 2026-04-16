# Pull Request Standards

Pull Requests are used to merge development code into the master branch. 

SFL Scientific uses the concept of feature-oriented pull requests. This means code shouldn't be necessarily merged at a regular interval, but rather should be pushed once a feature is complete. 

All code, once pushed to a PR, should be self-explanatory, well-documented, and fully-functional for the given feature. Never create a pull request for code that is not ready for review. Coding and documentation standards are outlined here: ‣ 

Developers should keep our [Design Checklist](../Writing%20Code/Design%20Checklist%20a8c93611bc9c44ed8e71c8bc4033812e.md)  in mind when developing their code and planning their [PR requests](../Writing%20Code/Design%20Checklist%20a8c93611bc9c44ed8e71c8bc4033812e.md).

**This page will cover:** 

# What Goes in a Pull Request

## Feature-Oriented Modularity

Pull requests should be performed when features are complete. Therefore, pull requests may occur at somewhat irregular frequency dependent on the feature development effort required.

## Definition of a Feature

Decompose the system into the features that it provides and develop each feature independently.

A feature is an incremental unit of functionality. In our world of data science, features could be:

- Data preprocessing
- Feature extraction
- Model training
- Model evaluation
- Testing of end-to-end pipeline

Features should be small enough to be easy to digest, but not be so small as to be disruptive.

## Making Pull Requests Complete

Each PR should contain all the code needed to complete a feature. In almost all cases, this includes the productionization of relevant source code for that feature.

**Requirements of Productionization**

- Use good documentation
    - Describe your input/output/dependencies
    - SFL Style requires use of Pep8 and docstrings

**Documentation is a requirement to complete a feature. PRs will not be approved without sufficient documentation.**

Bad documentation is insufficient to describe the functionality. 

```markdown
generate image features
```

Good documentation will describe functionality well. 

```markdown
generate image features using method a,b,c given inputs A, B,C. Columns X, Y, Z are required in input Dataframe and output will be saved as 1, 2, 3
```

Never create a PR for code that is not ready to be reviewed. 

# When to Create a Pull Request

Because pull requests are feature-based, they should be performed when features are complete. 
Therefore, pull requests may occur at somewhat irregular frequency dependent on the feature development effort required. 

As the size of a feature should not be too significant, pull requests should occur on an approximate cadence of every 1 to 2 weeks. 

![](Pull%20Request%20Standards/GithubBestPractices_-_CadenceHighLevel.png)

An example of the frequency expected for feature-based pull requests for a project might look like: 

```markdown
[wk 1]     0. Develop image data ingestion (IO) function
[wk 1]     1. Image conversion: convert DICOM format image into NPY format using method X,Y,Z
[wk 2]     2. Image Processing: Denoize, normalize, standardize image using X,Y,Z
[wk 2]     3. Generalize image processing with method X,Y,Z
[wk 3]     4. Develop ground truth data ingestion function
[wk 3]     5. Extract and clean feature A,B,C from ground truth data
[wk 4]     6. Develop split function
[wk 6]     7. Develop medicalNet model
[wk 7]     8. Productionize training pipeline
[wk 8]     9. Develop Inference functionalities
```

Notice that in some cases multiple features are developed within a single week and in other cases a feature can take up to 2 weeks to be developed. 

# How to Create a Pull Request

Before creating a pull request, make sure to complete the following tasks 

- [Convert Python Notebooks to MD files](https://www.notion.so/80f703305a864ce3b05868b6c25cf91d?pvs=21). (Note in the future this may be incorporated into the CI/CD pipeline)

Once you are satisfied with commits made to a branch, push your local code to the matching origin (Github) branch. Generate a new pull request to merge your branch into the origin\master branch. 

### Walk through for generating PR

Say you want to submit your branch called "Feature" for code review. Make sure all of the code you want to submit has been committed. Make sure that your code is **documented**.  

First you need to pull down any updates that have occurred on the GitHub master branch (maybe a collaborator has pushed a new feature), use:

```bash
git pull origin master
```

Now your master branch will be up to date. You should make sure that any new code in the master branch doesn't affect the code you want to push, so you need to merge:

```bash
git checkout Feature
git merge master
```

Now the code in your **branch** will be up to date; make sure all your classes/methods still work as expected. Now you're ready to push your code to the GitHub:

```bash
git push origin Feature
```

The GitHub should display your recent commits; to generate a PR, go to the SFL GitHub, find your project, and click New Pull Request:

![](Pull%20Request%20Standards/Screenshot_from_2020-04-21_15-19-42.png)

To finalize the PR, add your merge notes and click Create Pull Request. Code reviewers must be assigned manually.