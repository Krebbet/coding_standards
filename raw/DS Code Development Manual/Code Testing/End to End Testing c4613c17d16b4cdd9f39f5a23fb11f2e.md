# End to End Testing

This is a landing page for all notion pages regarding End to End testing. These pages serve as a comprehensive guide on End to End testing, including guidance on when E2E testing should be performed, best practices for performing E2E tests, and instructions on how to perform E2E testing (for both the developer and the tester). 

The purpose of End to End (E2E) Testing is to assess the overall quality of a final product in a manner that may be experienced by the customer. Any binding issues found during E2E testing must be addressed prior to code delivery to the client. E2E is not a review of the minutia - quality of the code, proof reading… if such issues are visible during E2E testing, then the project must be passed back to the development team for a thorough review. See the pages below for specific guidance on E2E testing:

# Preparing Code for E2E Testing

[Performing E2E Testing (Tester) ](End%20to%20End%20Testing/Performing%20E2E%20Testing%20(Tester)%202034b5a40b894d1d84f259aa4be71b80.md)

[Preparing for Testing (Developer)](End%20to%20End%20Testing/Preparing%20for%20Testing%20(Developer)%203bcdb3653f4249f1b0aefff041e822c4.md)

[Creating a Linux Testing Environment](End%20to%20End%20Testing/Creating%20a%20Linux%20Testing%20Environment%20695798a0d87f4c80bd02364c96a306b7.md)

[Creating a Windows Testing Environment](End%20to%20End%20Testing/Creating%20a%20Windows%20Testing%20Environment%2017734a35f16443e3a935a2ad825b262b.md)

# Performing E2E Testing

# E2E Testing Overview & Guidlines

# Creating Testing Environments

To ensure an E2E test is performed fully from scratch in an environment similar to that of the end-user, a testing environment can be created for the target platform. 

If needed, ensure that access and resources are provided by the client for this E2E testing to be done. Both developer and tester will have to spin up a fresh testing environment to perform E2E tests. The documentation in the codebase should be sufficient for the tester to understand all the assumptions and requirements to independently set up an instance.

Instructions on creating such testing environments on AWS are available below.