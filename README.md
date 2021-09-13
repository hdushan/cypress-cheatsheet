<!--
  Title: Cypress Cheat Sheet
  Description: Cypress selectors, assertions, and other helpful code snippets.
  Author: Hans Dushanthakumar
  -->

# Cypress Cheat Sheet

## Test syntax

```javascript
describe('This is my test', () => {
  before(() => {
    console.log('Runs once before all tests')
  })

  beforeEach(() => {
    console.log('Runs before each test')
    cy.visit('http://www.google.com')
  })

  it('works fine', () => {
    cy.get('input[name="q"]').type('hello')
  })

  afterEach(() => {
    console.log('Runs after each test')
  })

  after(() => {
    console.log('Runs once after all tests')
  })
})
```

## Visit page

```javascript
cy.visit('http://www.google.com')
```

## Selectors

### Select element by css selector

```javascript
cy.get('button.green')
```

### Select element containing text

```javascript
cy.contains('Submit')
```

### Select element using the "data-cy" attribute (cypress-recommended tag)

```javascript
cy.get('[data-cy="my-test-button"]').invoke('text').should('equal', 'Test')
```

## Interacting with page elements

### Type text into a text-box

```javascript
cy.get('input').type('hello')
```

### Type special chars into a text-box

```javascript
cy.get('input').type('I am pressing enter now {enter}')
```

### Double click

```javascript
cy.get('[data-cy="my-test-button"] > :nth-child(2)').dblclick()
```

### Preventing from opening in new tab

```javascript
cy.get('a').invoke('removeAttr', 'target').click()
```

### Check/Un-check check-boxes

```javascript
cy.get('[data-cy="my-test-checkbox"]').check();
cy.get('[data-cy="my-test-checkbox"]').uncheck();
```

### Select from drop-down by text

```javascript
cy.get('[data-cy="my-test-option-dropdown"]').select('Option 1')
```

### Trigger event on an element

```javascript
cy.get('[data-cy="my-test-option-dropdown"]').trigger('mouseover')
```

### Trigger event on an element at a certain coordinate (number of x,y pixels from the top left corner of the element)

```javascript
cy.get('[data-cy="my-test-option-dropdown"]').trigger('mouseover', 10, 20)
```

### Do a drag-drop

```javascript
/* Coming soon */
```

## Asserts

### Assert text of an element

```javascript
cy.get('h1').invoke('text').should('equal', 'Test')
cy.get('h1').eq(2).should('have.text', 'Test') /* Use 'eq(n)' to pick the (n-1)th  matching element */
cy.get('h1').eq(2).invoke('text').should('equal', 'Test')
cy.get('h1').should('contain', 'Test')
cy.get('h1').should('not.contain', 'Junk')
```

### Multiple assertions on element

```javascript
cy.get('h1').should('contain', 'Test').should('be.visible')
cy.get('h1').should('contain', 'Test').and('be.visible')
```

### Assert value of an element

```javascript
cy.get('h1').should('have.attr', 'value', 'Test')
cy.get('h1').eq(1).should('have.attr', 'value', 'Test')   /*If we want to use the 2nd h1 when there are more than one h1's*/
```

### Assertion using "expect" syntax

```javascript
cy.get('[data-cy="my-test-button"]') as('my-button')
cy.get('@my-button').then( $button_element => {
  expect($button_element.text()).to.equal('Test')
})
```

### Using "wrap" to wrap an element back to a form that can use "should" syntax

```javascript
cy.get('[data-cy="my-test-button"]').then( $button_element => {
  cy.wrap($button_element).should('exist')
})
```

### Assert number of matching elements

```javascript
cy.get('[data-cy="my-test-buttons"]').should('have.length', 2)
```

### Assert length of text in element

```javascript
cy.get('[data-cy="my-test-button"]').invoke('text').should('have.length', 4)
```

### Assert that element exists/does not exist

```javascript
cy.get('[data-cy="peekaboo"]').should('exist')
cy.get('[data-cy="peekaboo"]').should('not.exist')
```

### Assert that element is visible / not visible

```javascript
cy.get('[data-cy="peekaboo"]').should('be.visible')
cy.get('[data-cy="peekaboo"]').should('not.be.visible')
```

### Assert that element has a certain class

```javascript
cy.get('[data-cy="classy"]').should('have.class', 'my-class')
```

### Assert that element has a certain style

```javascript
cy.get('[data-cy="classy"]').should('have.css', 'background-color', 'red')
```

## Aliasing

```javascript
cy.get('[data-cy="my-test-button"]') as('my-button');
cy.get('@my-button').invoke('text').should('equal', 'Test')
```

## Pause execution to allow time to debug

```javascript
cy.get('[data-cy="my-test-option-dropdown"]').trigger('mouseover').debug()
// OR
cy.get('[data-cy="my-test-option-dropdown"]').trigger('mouseover').then(() => {
  debugger
)
```

## Read environment variable

```javascript
/* Prefix the environment variable name with 'CYPRESS_'. For eg, CYPRESS_TEST_VAR=Test */
Cypress.env('TEST_VAR')
```

## Simulate a logged-in user by manually setting a cookie to have a populated token

```javascript
/* This assumes that a cookie 'user_token' is set on successful login */
cy.setCookie('user_token', '<value of token of a loggen-in user>')

/* Cypress clears cookies by default between tests. To NOT clear a specific cookie, add the below in `support/index.js` */
Cypress.Cookies.defaults({preserve: 'trello_token'})

/* To preserve cookies only for tests in a specifc spec file, add this to the `beforeEach` in that spec file, and then do a setCookie in the first test */
Cypress.Cookies.preserveOnce('trello_token')
```

## Overwrite the "cy.log" command to log to terminal instead of browser console

```javascript
/* Define a "log" task in the "plugins/index.js" file as below */
module.exports = (on, config) => {
  on('task', {
    log: message => {
      console.log(message)
      return null
    },
  })
}

/* Overwrite the "cy.log" command in the "support/commands.js" file as below */
Cypress.Commands.overwrite('log', (subject, message) =>
  cy.task('log', message)
)

/* Once the above 2 changes are done, any call to "cy.log" will now log to terminal */
cy.log('Test')
```

## Testing an API & handling Network requests

### Send a http request

```javascript
cy.request({
  method: 'POST',
  url: '/url/endpoint'
  body: (
    parameter1: 'First parameter'
  )
})
```

### Test a REST API

```javascript
cy.request({
  method: 'POST',
  url: '/url/endpoint',
  headers: {
  'x-api-key': Cypress.env('apiKey'),
  'Content-Type': 'application/json',
  }, 
  body: (
    parameter1: 'First parameter'
  ),
  failOnStatusCode: false, // set this only if you are expecting a error response (ie 4xx or 5xx http response codes)
}).then( 
  response => {
    expect(response.status).to.eq(403)
    expect(response.body.data.attributes).to.have.property('authorization_url')
    expect(response.body.data).to.have.property('id')
    expect(response.body.data.attributes).to.have.property('result', 'Success')
    expect(response.body.message).to.eq('Failed')
    expect(response.body.data.id).to.be.a('string')
  }
)
```

### Intercept a browser request, and wait for that request to complete before moving on

```javascript
cy.intercept({
  method: 'GET',
  url: '/path/endpoint'
}).as('requestToWaitFor') // alias this 

cy.wait('@requestToWaitFor')  // wait for the alised intercept
  .its('response.statusCode') // assert status code
  .should('eq', 200)

//OR

cy.wait('@requestToWaitFor')  // wait for the alised intercept
  .then( (importantRequest) => { // make multiple assertions
    expect(importantRequest.response.statusCode).to.eq(200)
    expect(importantRequest.request.body.parameter1).to.eq('First parameter')
  })

cy.get('#elementId').click() // This will be run only after the wait above
```

### Intercept a GraphQL request sent by the browser and extract something from its response

```javascript
/* Add a command as below in the "support/commands.js" file */
Cypress.Commands.add('interceptMyGraphqlRequest', () => {
  cy.intercept('POST', /^https:\/\/api.*\/mywebsite\/graphql$/, req => {
    if (req.body.operationName.includes('myOperationName')) {
      req.alias = 'myGraphqlRequest'
    }
  })
})

/* And in your spec where you want to start intercepting, add the below line right before the step that results in the graphQL request being sent out */
cy.interceptMyGraphqlRequest()

/* To wait for the request and process its response */
cy.wait('@myGraphqlRequest').then(graphqlRequestDetails => {
  const myField = graphqlRequestDetails.response.body.data.result.fieldThatICareAbout;
  expect(myField).to.match(/\d+/)
})
```

### Intercepting and sending a stubbed response

```javascript
// Add this intercept statement before performing an action that results in this request
cy.intercept({
  method: 'GET',
  url: '/path/endpoint'
}, {
  body: [] // return empty array as body
  // OR
  fixture: 'name_of_fixture_json_file_in_fixtures_folder' // Note: dont add '.json' at end
  // OR
  forceNetworkError: true // to force a network error for this request
}).as('requestToSendAStubbedResponseFor') // alias this 

// If more manipulation is needed for the stubbing, use this
cy.intercept({
  method: 'GET',
  url: '/path/endpoint'
}, (request) => {
  console.log(request)
  request.reply( (response) => {
    console.log(response) // Log actual response
    response.body[1].parameter1 = 'Manipulated parameter 1 in response'
    return response
  })
}).as('requestToSendAStubbedResponseFor') // alias this 
```

## Run a task, for example, initialize a database before the test

```javascript
// Create a file in the `plugins` folder, with the implementation of the reset in a file `initializeDatabase.js`
module.exports.initData = (parameter = empty) => {
  // Do stuff to DB using 'parameter'
};

// Inside the `plugins/index.js` file, add a task as below:
const { initializeData } = require('./initializeDatabase')

module.exports = (on, config) => {
  on('task', {
    initData: initializeData
  })
} 

// Now in the spec file, run this task as below
cy.task('initData', {
  myData: 'Test'
})
```

## Misc

### Cypress code completion in VS Code in file

```javascript
/// <reference types="Cypress" />
```

### Cypress code completion in VS Code for whole project

```javascript
/*In file jsconfig.json in root folder of project*/
{
  "Include": [
    "./node_modules/cypress",
    "cypress/**/*.js"
  ]
}
```
