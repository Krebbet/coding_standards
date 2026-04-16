# Integration/E2E Tests

Within UI and UX, the lines between unit, integration, and E2E test are often fuzzy. This section will focus on integration tests as a mean to test a workflow within a website- which would also be considered an E2E test by some.  Simpler integration tests, which automatically test a large component comprised of smaller components, can be tested following the methodology given [here](Testing%20Components%20492b01f4cec348f8b00b7b02be3c316e.md).

# Selenium IDE

Perhaps the easiest way to create tests for a website is to use the [Selenium IDE](https://www.selenium.dev/selenium-ide/) web extension. This web extension allows you to record yourself clicking through your website.  Then, this recording can be 'played back'. The playback will open the webpage, click, and type just as you did during the recording.

You can also create assertions while recording for the test, for example you can assert that some certain text is displayed. It is also possible to store a value from within the webpage to a variable and use that in later assertions.

### Selenium IDE Docs

[https://www.selenium.dev/selenium-ide/docs/en/introduction/getting-started](https://www.selenium.dev/selenium-ide/docs/en/introduction/getting-started)

## Selenium IDE Tutorial

[https://www.youtube.com/watch?v=ZG3VFDMaAlk](https://www.youtube.com/watch?v=ZG3VFDMaAlk)