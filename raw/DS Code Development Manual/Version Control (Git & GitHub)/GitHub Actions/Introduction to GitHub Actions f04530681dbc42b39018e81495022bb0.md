# Introduction to GitHub Actions

# Usage

### New Repos

 If repo is created from the SFL-Template, then it will have the actions built in. If the repo is not originating from the SFL-Template, then please reach out to your project leaders for guidance. You may be interested in [GitHub’s documentation](https://github.com/features/actions)  on the subject.

### Viewing the Actions

Actions can be view by clicking the "Actions" button on any github repo

![](Introduction%20to%20GitHub%20Actions/Untitled.png)

It will show workflows on the left and the latest runs of workflows on the right

![](Introduction%20to%20GitHub%20Actions/Untitled%201.png)

The message of the commit that the workflow ran on will show in the list on the right.

Specific workflows can be singled out by clicking their name on the left

![](Introduction%20to%20GitHub%20Actions/Untitled%202.png)

To view the output of each run of the workflow, click the commit message event on the right, in this case "updated todo fixed typo in readme..."

![](Introduction%20to%20GitHub%20Actions/Untitled%203.png)

Any artifacts and annotations will be shown on this page

The name of this workflow is "MD to PDF" with the single job called "makepdfs"

To see the logs of the run, click the name of the job on the left to go to the job page

![](Introduction%20to%20GitHub%20Actions/Untitled%204.png)

This will show the names of all steps in the job and their status. To see more info on a step, click its name (ex: "Markdown to PDF" or "Remove non readme pdfs")

![](Introduction%20to%20GitHub%20Actions/Untitled%205.png)

This output is scroll-able to see the whole log. In the screenshot above, it just shows the bash command that was ran in that step

These steps can be viewed while a workflow is running to see the output in real time.