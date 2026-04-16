# Testing Components

# Why test components?

Components are reusable functions or classes that define a piece of a website. These can be very small components defining something as small as a box on a page, in which case testing them could be considered a unit test. These can also be large components, for example defining an entire page (likely by composing many smaller components) which could be considered an Integration or even E2E test.

If ReactJS code is organized well into components, then creating accommodating tests for each is elegant and effective. It can be done for each smaller component and serve as unit tests, leaving E2E and integration tests to Selenium. Alternatively, both large and small components can be tested to provide unit and integration testing across the project.

# Jest & React Testing Library

React testing library provides some handy functions to simplify the creation of tests.  In particular, it will provide us functions such as render, fireEvent, waitFor, and screen that let us render our component and interact with the [DOM](https://en.wikipedia.org/wiki/Document_Object_Model).

## Example - Top App Bar

Here is a simple top app bar component built with RMWC, it has the SFL Logo, a configurable title, and a home button that links to the main webpage. 

![The top bar component stylized in the lovely SFL Pink](Testing%20Components/Untitled.png)

The top bar component stylized in the lovely SFL Pink

```jsx
import React from 'react';
import { TopAppBarActionItem, SimpleTopAppBar, TopAppBarFixedAdjust, TopAppBar, TopAppBarRow, TopAppBarSection, TopAppBarTitle, TopAppBarNavigationIcon } from "@rmwc/top-app-bar";
import '@rmwc/top-app-bar/styles';
import '@rmwc/icon/styles';
import '@material/top-app-bar/dist/mdc.top-app-bar.css';
import '@material/icon-button/dist/mdc.icon-button.css';
import '@material/ripple/dist/mdc.ripple.css';

class SFLTopBar extends React.Component {
    constructor(props) {
        super(props);
        if (props.title != null) {
            this.state = {
                title: props.title
            }
        }
        else{
            this.state = {
                title: ""
            };
        }   
    }

    render() {
        return (
            <div>
                <TopAppBar>
                    <TopAppBarRow>
                        <TopAppBarSection alignStart>
                            {//sfl_logo.png will need to be placed in your 'public' folder}
														<img style={{ 'maxHeight': '80%' }} src="sfl_logo.png" />
                            <TopAppBarTitle>{this.state.title}</TopAppBarTitle>
                            <TopAppBarSection alignEnd>
                                <a href="/"><TopAppBarActionItem icon="home" /></a>
                            </TopAppBarSection>
                        </TopAppBarSection>
                    </TopAppBarRow>
                </TopAppBar>
                <TopAppBarFixedAdjust />
            </div>
        );
    }
}

export default SFLTopBar;
```

We will create a test file in the same folder as the component itself.  It is possible to put tests elsewhere in the project, but having them co-located with the code that they test makes it easier to look through the both of them should the test fail.

On my machine, I have named this component `TopBar.js` so the associated test will be called `TopBar.test.js`

![Co-location of component and test file](Testing%20Components/Untitled%201.png)

Co-location of component and test file

Here is the contents of the test file, there are three separate tests included in this file

- Test to see if the top bar title is rendered
- Test to see if an image is rendered (SFL Logo)
- Test to see if the home icon is rendered and links to the root webpage

Each one of these starts by rendering the component, and then using `expect` to make assertions about the DOM.

```jsx
import React from 'react'
import { render, fireEvent, waitFor, screen} from '@testing-library/react'
import '@testing-library/jest-dom/extend-expect'
import SFLTopBar from './TopBar'

test('Top Bar Renders Title', async () => {
    const { container } = render(
        
        <SFLTopBar title="testtitlelol">
        </SFLTopBar>

    );
    //Check that we have rendered an element with this text
    expect(screen.getByText("testtitlelol"));
});

test('Top Bar Renders SFL Logo', async () => {
    const { container } = render(
        
        <SFLTopBar title="testtitlelol">
        </SFLTopBar>

    );
    //Check that we have rendered an image (there's only one in this component, so this is sufficient)
    expect(screen.getByRole('img'));
});

test('Top Bar Renders Home Button', async () => {
    const { container } = render(
        
        <SFLTopBar title="testtitlelol">
        </SFLTopBar>

    );
    //Check that we have rendered the home icon button
    expect(screen.getByRole('button', { name: /home/i }));

    //Check that the link goes to the homepage
    expect(screen.getByRole('link', { name: /home/i })).toHaveAttribute('href', '/');
});
```

After setting up your tests, you can run them with `npm test` from the project's root directory. This will run all tests in the project.