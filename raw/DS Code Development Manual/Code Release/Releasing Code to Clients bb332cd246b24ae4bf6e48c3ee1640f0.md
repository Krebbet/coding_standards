# Releasing Code to Clients

# Reviews and Approvals

After CRs and all comments have been addressed between the E2E tester and the DS, the code should be reviewed for a final time by the TL before approving to move forward with the release actions listed below.

# Final Repo Cleaning

## Ensure All Contents are Relevant and Applicable

Responsibility: Developer

- Remove any folders or files which are vestigial from the template but which aren't necessary for your final delivery.
- Update the Readme with thorough instructions for all components of the delivery. Ensure formatting is correct. Convert all readme files to a PDF format & store them in the repo.
- Make sure all notebooks included meet [Jupyter Notebook Guidelines](../Writing%20Code/Jupyter%20Notebook%20Guidelines%20d8d4eab8446a4aa586026326d9cf0689.md)
- Remove all TODO and similar comments from the code.

## Check Repo Contents are Correct

Responsibility: Project Manager

Each repo derived from the SFL Template repo should have a GitHub action called "Pre-release-checks" which verifies the above steps. The PM should run the action and ensure the developer resolves any warnings/errors prior to finalizing the repo. The pre-release checks perform automated checking in:

1. Readme format and contents are up to date
2. The code base does not contain holdovers from the template
3. All to-do tags and commented code is removed
4. The readme file is converted into PDF format

The action will be automatically triggered every time a pre-release is created. It can also be run manually by clicking the Actions tab is the repo page, from `All workflows` select `Pre-release-checks`. There will be a Run workflow button shown on the right, click it to trigger the action.

![](Releasing%20Code%20to%20Clients/Untitled.png)

The execution time of the job depends on the size of the repo and generally takes less than 3 minutes. You will receive an email notification if the check fails (need to subscribe to the repo).

You can always go back to the actions page to check the status and results of the workflow. If the check failed, you can access the log by clicking the job or downloading the logs from the artifacts session on the page.

# Creating a Release on Github

Responsibility: Project Manager

There are three components required to create a release: 

1. Version number
2. Release title
3. Release description

Releases can be created through the [Github.com](http://github.com) web interface or locally via CLI. 

If released via the web user interface, the components described here are entered directly. 

If releasing via the CLI, follow the separate instructions below to enter this information. 

![Screen Shot 2022-01-06 at 3.32.22 PM.png](Releasing%20Code%20to%20Clients/Screen_Shot_2022-01-06_at_3.32.22_PM.png)

## 1. Version Number

Responsibility: Project Manager

SFL Scientific uses Semantic versioning in all the code base releases. The version title is seen externally as it is automatically used for the naming of the compressed file repo that is provided to the client. 

![](Releasing%20Code%20to%20Clients/Untitled%201.png)

The versioning is a formal convention for specifying compatibility using a three-part version number: major version; minor version; and patch. All the numbers are started from 0, and specified as follows:

- The **patch number** is incremented for minor changes and bug fixes which do not change the software's core functionality — generally the model pipeline’s inputs or outputs. Some examples to be considered as patches:
    - Edge case fix
    - Hyperparameter updates
    - Style or color updates in output plots or UI

- The **minor version number** is incremented for releases that add new, but backward-compatible, features. For instance, if the pipeline was updated and now takes in an optional parameter, or outputs an additional file in the S3 bucket, the customer can still use the code in the old fashion (ignore the optional parameter and addition output). This is a minor version change.

- The **major version number** is incremented for changes that are not backward-compatible.
    - During development, the major version generally remains 0 (if a numbered release is needed), even if the code base is iterating and not backward-compatible.
    - Generally, the first project handoff to a client should be versioned as v1.0.0.
    - Further changes like follow-up requests, bug fixes, or enhancement contracts should be versioned with this semantic versioning guideline.

## 2. Release Title

Responsibility: Project Manager

The version title is only for use internally, seen on the Github, and seen on the project's Github release page. This is not shared with the client. 

The title should match the project repo, followed by the version number. 

## 3. Release Description

Responsibility: Project Manager

As with the version title, the version description is only for use internally, as seen on the project's Github release page

For the first release (assuming `v1.0.0`), the release description can be as simple as: 

Codebase release for XXX project. 

Including data ETL, modeling, cloud deployment, and documentation. The model puts a beard on people’s photos with a GAN network.

For additional incremental releases, the release description should briefly describe the feature updates, with the help of git commit messages. Depending on the actual amount of changes, you can work in three different ways. You can create the tag/release first and edit the release description later on [Github.com](http://github.com/).

1. **Small amount of changes.** At Github.com, go to the releases page and use the compare function to get the incremental commit history from a previous release.
2. **A long list of changes.** You may only look at the merge history on the master branch via the command line. Checkout the master branch and do `git log --merges <commit ref of previous release>..<last commit ref to include in this release>`
3. **An overwhelming long list** of merges without informative commit / merge messages. Check with the developers for a change log.

The description should take the following format: 

```
Fixes:
- fix bug
- other bug

Changes:
- Change the running path to ...

New:
- Add new figure outputs
- Support json read-in
```

## Releasing via CLI

If working via the command line, the same components are included, but an additional step is needed for signature. 

SFL Scientific uses GPG to sign all the code releases, as a proxy for internally committing the code is verified and approved for release. The signed release can be used to verify whether the delivered release is intact when entering a phase of maintenance or resolving issues.

**Install GPG Certificate** 

You need to generate a GPG key with your GitHub email and add it to your GitHub account. A detailed step-by-step guide can be found at [GitHub Documentation](https://docs.github.com/en/github/authenticating-to-github/generating-a-new-gpg-key).

**Create a Signed and Tagged Release**

Within the repo folder in your Terminal, create a git tag in the following format. Make sure to replace placeholders (e.g., `info@sflscientific.com`) with the appropriate project-relevant information.

```bash
git tag -u 'info@sflscientific.com' -s -a templateGPGSigned_v100 -m "Code Template Release 1.0.0 Tag GPG Signed"
```

**Push Tags to Github**

A regular git push command will now push your signed and tagged release. 

```bash
git push --tags
```

# Sharing with the Client

Responsibility: Project Manager

Code and weights are typically shared via the [external SharePoint site](https://www.notion.so/Pre-Kickoff-Responsibilities-65ef95691fdb43bf91ffee7eaecb787d?pvs=21), but ensure that the project’s security level permits this. For example, certain government-related projects may only allow sharing via the client’s resources/storage (such as Box.com). The same structure and practices should still be followed within that resource. 

## Sharing the Repo

Once the release has been created, it can be downloaded from the project's Github page as both a TAR file and a ZIP file. These are generally both provided to the client in the external-facing project materials & deliverables folder in Sharepoint. Please ask your project manager for more specific instructions.

Note, if a client wishes for delivery through a different mechanism, a full copy of the versioned code should also be kept in Sharepoint for ease of internal reference, security requirements permitting.

## Sharing Large Files (e.g., Model Weights)

Large files (e.g., model weights) should not be stored in the repository. Therefore, to share large files, provide them to clients in the final shared directory (e.g., SharePoint) using the following method:  

1. Ensure you are sharing the correct version of the files.
2. Organize the files in a directory (locally) with the appropriate hierarchy and keep their original names. Prepare an instruction file describing how to use these files, for instance, refer to the readme in the code base; where to put these files; change the configuration file in the code base, etc.
3. Compress the entire directory to ZIP format, rename the ZIP file depending on the content and version number (if it is associated with a specific version of code delivery), e.g., `bearding_model_weight_v.1.2.3.zip`
4. Confirm with the project PM where the files should be uploaded. You may need to create these folders if they do not exist. Then, upload the file(s).
5. Notify the client that the file has been uploaded to the shared folder.