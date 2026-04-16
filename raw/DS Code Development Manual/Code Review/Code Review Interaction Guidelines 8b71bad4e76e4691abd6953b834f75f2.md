# Code Review Interaction Guidelines

[Tips for developers](Code%20Review%20Interaction%20Guidelines%208b71bad4e76e4691abd6953b834f75f2.md)

[Tips for code reviewers](Code%20Review%20Interaction%20Guidelines%208b71bad4e76e4691abd6953b834f75f2.md)

[Resolving conflicts](Code%20Review%20Interaction%20Guidelines%208b71bad4e76e4691abd6953b834f75f2.md)

[When can the PR be approved](Code%20Review%20Interaction%20Guidelines%208b71bad4e76e4691abd6953b834f75f2.md) 

# Tips For Developers

The review process is meant to improve code quality through peer feedback. To maximize benefits from it and achieve the goal of cleaner and more understandable code and a more enjoyable user experience, the following are tips on handling reviewer comments:

- **Adopt a constructive mindset**
    
    Respond to the constructive aspects of the feedback and use it to help improve your code. If the feedback is not constructive or polite, explain and resolve this in a private discussion.
    
- **Take responsibility for clarity**
    
    If a reviewer does not understand parts of your code, it is likely that future users will not understand it either. You should try clarifying the code itself first (e.g., by simplifying it). Only use comments when the code itself cannot be clarified. An explanation in the PR by itself should be the last resort for clarification.
    
- **Consider the optimal approach**
    
    Remember the purpose of the code review is to improve code quality. Using a constructive mindset, take action on suggestions that you think would improve the code. However, sometimes it is possible that some clarity or discussion is needed to understand what the best approach is. If you are aware of additional context that may require a different approach from what the reviewer suggested, provide them with this information so that you and the reviewer can come to a more objective consensus.
    

# Tips For Code Reviewers

Sometimes a developer will push back on a code review. Either they will disagree with your suggestion or they will complain that you are being too strict in general. The following are tips on handling pushback from developers during the code review process

- **Who is right?**
    
    When a developer disagrees with your suggestion, first take a moment to consider if they are correct. Often, they are closer to the code than you are, so they might really have a better insight into certain aspects of it. If so, let them know that they are right and let the issue drop.
    
    However, developers are not always right. In this case, the reviewer should further explain why they believe that their suggestion is correct.
    
    Sometimes it takes a few rounds of explaining a suggestion before it really sinks in. Just make sure to always stay **polite** and let the developer know that you *hear* what they’re saying, you just don’t *agree*.
    

- **Upsetting Developers**
    
    Reviewers sometimes believe that the developer will be upset if the reviewer insists on an improvement. 
    
    Usually, if you are polite in your comments, developers actually don’t become upset at all. Upsets are usually more about the way comments are written than about the reviewer’s insistence on code quality.
    

- **Cleaning It Up Later**
    
    A common source of pushback is that developers (understandably) want to get things done. They don’t want to go through another round of review just to get this PR in. So they say they will clean something up in a later PR, and thus you should approve this PR now. 
    
    However, experience shows that usually unless the developer does the clean up immediately after the present PR, it never happens. 
    
    Thus, it is usually best to insist that the developer clean up their PR now, before the code is “done.”
    

- **Complaints About Strictness**
    
    If you previously had fairly lax code reviews and you switch to having strict reviews, this may lead to comments from some developers around the changed expectations. Improving the speed of your code reviews usually causes these complaints to fade away.
    
    Eventually, developers tend to see the value of strict code reviews as they see what great code they help generate.
    

# Resolving Conflicts

When coming to a consensus becomes especially difficult, it can help to have a face-to-face meeting or a video conference between the reviewer and the author, instead of just trying to resolve the conflict through code review comments.

If that doesn’t resolve the situation, first escalate the situation to the developer team, and then to the technical/team lead if needed.

**Don’t let a PR sit around because the author and the reviewer can’t come to an agreement.**

# When Can The PR Be Approved

The developer should address all the reviewer comments before the PR can be approved.

The reviewer should approve the PR promptly once the developer address all the important issues.