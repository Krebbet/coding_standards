# Code Reviewer Standards

The primary purpose of code review at SFL Scientific is to make sure that we deliver high quality code to the clients under the functional specification and timeline for each project.

The code review process is also an important part of mutual learning, where both code reviewers and developers can gain exposure to different coding techniques in an interactive manner, allowing convergence towards optimal coding practices for the company.

Note that **code reviewers take responsibility for the quality of the code that they review**; code with existing issues that do not meet quality standards should not be approved. Instead, the developer and code reviewer (with involvement of the tech lead when necessary, especially if there is a time crunch involved) should work together to ensure optimal code quality.

# Coverage and Speed

New reviewers often ask at least one of the following

- “How much of the code should I review?”
- “When should I do code review?”
- “How long should I spend on this?”

## Every Line

Look at *every* line of code that you have been assigned to review. Some things like data files, generated code, or large data structures you can scan over sometimes, but don’t scan over a human-written class, function, or block of code and assume that what’s inside of it is okay.

If it’s too hard for you to read the code and this is slowing down the review, then you should let the developer know that and wait for them to clarify it before you try to review it.

If you can’t understand the code, it’s very likely that the clients won’t either.

## Check Against SFL Standards

Core items to check for are present in code standards.

[Code Standards](../Writing%20Code/Code%20Standards%20bb20e0f77b0f4d52b8d416a19ef1699b.md) 
More general guidelines for designing good code is available under design philosophy

[Design Philosophy](../Writing%20Code/Design%20Philosophy%203def412c5fc644da9e067f9b614ea616.md)

## Quickly, with urgency

Code reviews should be performed quickly, with urgency.

**Why**

When code reviews are slow, several things happen:

- The velocity of the team as a whole is decreased
- Developers start to protest the code review process
- Code quality can be impacted

Please view [here](../Code%20Review%20e43e415ced4c4cf5ab56f0d74d904cfc.md) for details on SFL's Code Review Timing Expectations.

**Key Code Review Timing Takeaways**

- PRs should be reviewed as soon as possible — at most within 24 hours.
- The entire PR lifecycle from creation to merge should generally take less than 36 hours.
- All exceptions to these timings should consider overall impact to a project's timelines and be discussed with PMs.

# How to write code review comments

## Courtesy

In general, it is important to be courteous and respectful while also being very clear and helpful to the developer whose code you are reviewing. One way to do this is to be sure that you are always making comments about the *code* and never making comments about the *developer*. You don’t always have to follow this practice, but you should definitely use it when saying something that might otherwise be upsetting or contentious. For example:

Bad:

Why did **you** use threads here when there’s obviously no benefit to be gained from concurrency?

Good:

B: The concurrency model here is adding complexity to the system without any actual performance benefit that I can see. Because there’s no performance benefit, it’s best for this code to be single-threaded instead of using multiple threads.

## Explain Why

One thing you’ll notice about the “good” example from above is that it helps the developer understand why you are making your comment. You don’t always need to include this information in your review comments, but sometimes it’s appropriate to give a bit more explanation around your intent, the best practice you’re following, or how your suggestion improves code health.

### Differentiate Blocking and Non-Blocking Issues using NB and B

Reviewers should always feel free to leave comments expressing that something could be better; however, a change will not always be required to accept the PR. To make this explicit, use the following notation:

- **B = Blocking:** start a comment with B if a change is required for the PR to be accepted.
- **NB = Non-blocking:** start a comment with NB if you would like to suggest how to improve the code or notebook, but are not requiring a change.

## Giving Guidance

**In general, it is the developer’s responsibility to fix a PR, not the reviewer’s.**

This doesn’t mean the reviewer should be unhelpful, though. In general you should strike an appropriate balance between pointing out problems and providing direct guidance. Pointing out problems and letting the developer make a decision often helps the developer learn and makes it easier to do code reviews. However, sometimes direct instructions, suggestions, or even code are more helpful. Such direct instructions should be given when they can help the developer fix the code more efficiently, for instance, when the reviewer knows an existing function or package that the developer is apparently not aware of.

One important goal of code reviews is improving the skills of developers so that they require less and less change requests over time.

## Good Things

If you see something nice in the PR, tell the developer, especially when they addressed one of your comments in a great way. Examples: developer cleaned up a messy algorithm, added exemplary test coverage, or you as the reviewer learned something from the PR.

Code reviews often just focus on mistakes, but they should offer encouragement and appreciation for good practices as well. It’s sometimes even more valuable, in terms of mentoring, to tell a developer what they did right than to tell them what they did wrong.