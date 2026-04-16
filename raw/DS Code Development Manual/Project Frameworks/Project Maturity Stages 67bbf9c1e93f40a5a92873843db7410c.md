# Project Maturity Stages

# Overview

This page describes the general expectations for projects at each of the three primary maturity stages.

## Definitions

A **conceptual code review** assesses if the code achieves the desired result, it bug free, and can be understood with a reasonable amount of effort. These reviews primarily focus on the what, as opposed to the how. 

A **detailed code review** assesses if the code achieves the desired result, is bug free, and follows software development best practices, such as extensibility, low coupling, reasonable efficiency etc. These reviews focus on both the what and the how. These typically take longer than conceptual code reviews. 

**Research code** is quickly written for the purposes of exploring a data set, building PoC models, etc. It is generally of a low quality: it is often not extensible and not tested, it may have confusing APIs, etc.  This type of code is typically given a conceptual review.

**Production code** is written for the purposes of creating a robust software product. It is necessarily of a high quality: it is tested, it is extensible, it is correctly formatted, it has intuitive apis, it is often object oriented, etc. This type of code is typically given a detailed review.

# Maturity stages

## Proof of Concept (PoC)

These projects are fast paced and aimed at proving out a concept.

**User Personas**

- Technical folks who want to continue the research
- Decision makers who need to make decisions about investing in data science products

**Code quality**

- Research code

**Review requirements**

- Conceptual

**Examples**

- Luxmed

**Service level agreement considerations**

- Not intended for deployment of any kind
- Adding reasonable features requires high effort

## Minimal Viable Product (MVP)

These projects have a more reasonable pace and are aimed at releasing a working product for limited use.

**User Personas**

- Small group of somewhat technical users, who will leverage the product for their day-to-day work.
- Decision makers who need to make decisions about scaling.

**Code quality**

- A mixture of research and production code; typically essential units are production code

**Review requirements**

- Conceptual review and detailed review, depending on the type of code

**Examples**

- Sleepme

**Service level agreement considerations**

- Intended for limited deployment
- Adding reasonable features requires medium to high effort
- Users may need to troubleshoot and solve problems

## General Availability (GA)

These projects are often extended and aimed at releasing a production product for wide use.

**User Personas** 

- Large group of non-technical users, who will leverage the product for their day-to-day work.

**Code quality**

- Production code

**Review requirements**

- Detailed review

**Examples**

- Pfizer Atlas

**Service level agreement considerations**

- Intended for wide deployment
- Adding reasonable features requires low to medium effort
- Users should not need to troubleshoot problems

## Multi-phase projects - Code Quality and Code Testing

Some projects will have a multiple phases, each of which corresponds to a different product deliverable.

![Untitled](Project%20Maturity%20Stages/Untitled.png)

- As a project moves through these stages the code quality, testing, and review requirements increase.
    - This increase tracks the increase in risk.
- GA phase of a multi-phase project is mostly software development; the data science work is already done at this point.
- Generally, there is a large code refactor when moving from MVP to GA.

# References

- [https://www.kdnuggets.com/2020/12/data-science-product-hard.html](https://www.kdnuggets.com/2020/12/data-science-product-hard.html)
- [https://uxplanet.org/the-5-stages-of-product-prototyping-ebb276004640](https://uxplanet.org/the-5-stages-of-product-prototyping-ebb276004640)
- [https://en.wikipedia.org/wiki/Technology_readiness_level](https://en.wikipedia.org/wiki/Technology_readiness_level)
- [https://blog.palantir.com/code-review-best-practices-19e02780015f?gi=59239b3714c9](https://blog.palantir.com/code-review-best-practices-19e02780015f?gi=59239b3714c9)
- [https://storage.googleapis.com/pub-tools-public-publication-data/pdf/aad9f93b86b7addfea4c419b9100c6cdd26cacea.pdf](https://storage.googleapis.com/pub-tools-public-publication-data/pdf/aad9f93b86b7addfea4c419b9100c6cdd26cacea.pdf)