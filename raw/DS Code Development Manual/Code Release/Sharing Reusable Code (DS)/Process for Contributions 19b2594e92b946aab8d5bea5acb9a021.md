# Process for Contributions

This page covers the process for submissions and review team composition.

## Submission Criteria

To avoid overly specific or difficult-to-use code, submissions will be reviewed based on the following criteria.

| Criteria | Description | Relative Importance |
| --- | --- | --- |
| Functionality | Does the code accomplish what it’s meant to? Is it (relatively) optimized? Are the results clear and easy to use in other codebases? | High |
| Design | Is the code understandable and sufficiently modular? Does it follow our [Coding Standards](../../Writing%20Code/Code%20Standards%20bb20e0f77b0f4d52b8d416a19ef1699b.md)? Do the inputs and outputs reasonably align with best practices? | High |
| Reusability | s there a clear use for this code for other projects? Who will benefit? This could be a vertical (LSHC, ER&I), a part of the project timeline (EDA, modeling), or more general (“anyone who uses pandas,” “this code makes Docker work on the DGX”). | High |
| Availability | Is this easily available in standard packages? Or is there something valuable to having an SFL version? | Medium |
| **Unit/Integration Tests** | As tests are incorporated into the SFL Code process, submissions will be expected to include testing appropriate to the code complexity. | Medium/Future |
| **Package Requirements** | Are the packages used mature enough? Are any `DeprecationWarnings` taken care of on initial PR? Anything too exotic needs sufficiently high “Reusability” to be considered for inclusion. | Medium |
| **Documentation** | Does the PR include an update to the repository README? This should be an entry in the Table of Contents and a linked description of the code and its purposes/use-cases. | Required for finalized submission |

## Submissions Process

1. Submit a PR for quality code to the [SFL-Code-Reuse repository](https://github.com/Deloitte-US-Consulting/SFL-Code-Reuse), either for new highly reusable code or a project-agnostic modification of existing code.
2. In Slack, tag the **@code-reusers** group and link your PR in the `#sfl_codereview_forum`
3. The code-reusers team will assign a member to review. The member cannot be associated with the development of the PR. The member will review the submission based on the [Submission Criteria](Process%20for%20Contributions%2019b2594e92b946aab8d5bea5acb9a021.md) 
4. The group will vote based on this evaluation if the PR should be included or excluded. Depending on the group’s working style, this will either be during a meeting or in a group-specific channel.
    
    <aside>
    ✅ **Approved**: The PR will be approved and the submitting developer will be asked to merge. The new addition will be announced in the `#sfl_codereview_forum`
    
    </aside>
    
    <aside>
    📝 **Revisions Required:** The group may offer the developer a chance to revise the code if there is a path to approval. This should take the format of a standard code review. The developer may choose to withdraw their request if the revisions are overly taxing.
    
    </aside>
    
    <aside>
    🚫 **Rejected:** The group may reject a piece of code outright if it fails any of the review categories. The PR should then be closed.\
    
    </aside>
    
    Regardless of the vote outcome, all feedback should be constructively passed on to the submitting developer, encouraging future submissions to the repository. All participants in this process should adhere to [Code Review](../../Code%20Review%20e43e415ced4c4cf5ab56f0d74d904cfc.md) practices to ensure a respectful and educational review process.
    
    We expect many submissions to not pass the review process. We aim to keep the repository useful through simplicity, so even excellent code may be rejected if it is not reusable in other projects.
    

## The Reusable Code Team

The `code_reusers` team is a group of developers responsible for reviewing PRs to the repository. This group should include

- 1 tech lead
- 1 PM or PC
- 2-4 developers

The technical members should represent the technical diversity within our team and be approved for code reviews. The PM or PC should be interested in the technical aspect of projects and have capacity to facilitate the review process. 

All members will be rotated every quarter based on anticipated project loads. When the `code_reusers` team is refreshed (once a quarter), the old and new teams should review functions in the repository, ensuring they are still relevant and compatible with standard code packages. Changes should be submitted through the submission process.

## Where should I put my code?

New code will be placed “where it would exist in an actual project” 

Docker in the docker folder, Python in the src folder, etc.

The src folder will be divided into submodules (plots, metrics, etc) as the repository expands. Submodules should contain code with similar functionality and requirements.

### Integrations with DE Templates and Other Repositories

At the time of writing, the Data Engineering Team is creating template repositories for a variety of tools (Terraform, Kubernetes, etc.). These repos are reviewed by the DE team and do not need review by this team.

However, we want to document this capability in the [SFL-Code-Reuse repo](https://github.com/Deloitte/SFL-Code-Reuse). When a new DE Template is approved, a PR should be submitted to this repository to update the DE Template section of the Table of Contents. This update should **not** include any code—this should only include a link to the repo and a description of its functionality. The new addition will be announced in the `sfl_codereview_forum`