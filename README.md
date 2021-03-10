# Cypress Cheat Sheet

## Test syntax

```javascript
describe('This is my test', () => {
  beforeEach(() => {
    cy.visit('http://www.google.com');
  });
  it('works fine', () => {
    cy.get('input[name="q"]').type('hello');
  });
});
```

## Visit page

```javascript
cy.visit('http://www.google.com');
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
cy.get('[data-cy=“my-test-button”]').invoke('text').should('equal', 'Test')
```

## Interacting with page elements

### Type text into a text-box

```javascript
cy.get('input').type('hello');
```

### Type special chars into a text-box

```javascript
cy.get('input').type('I am pressing enter now {enter}');
```

### Double click

```javascript
cy.get('[data-cy=“my-test-button”] > :nth-child(2)').dblclick();
```

### Check/Un-check check-boxes

```javascript
cy.get('[data-cy=“my-test-checkbox”]').check();
cy.get('[data-cy=“my-test-checkbox”]').uncheck();
```

### Select from drop-down by text

```javascript
cy.get('[data-cy=“my-test-option-dropdown”]').select('Option 1');
```

### Trigger event on an element

```javascript
cy.get('[data-cy=“my-test-option-dropdown”]').trigger('mouseover');
```

### Trigger event on an element at a certain coordinate (number of x,y pixels from the top left cornet of the element)

```javascript
cy.get('[data-cy=“my-test-option-dropdown”]').trigger('mouseover', 10, 20);
```

### Do a drag-drop

```javascript
/* Coming soon */
```

## Asserts

### Assert text of an element

```javascript
cy.get('h1').invoke('text').should('equal', 'Test');
cy.get('h1').eq(2).invoke('text').should('equal', 'Test');  /*If we want to use the 3rd h1 when there are more than one h1's*/
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
cy.get('h1').should('have.attr', 'value', 'Test');
cy.get('h1').eq(1).should('have.attr', 'value', 'Test');   /*If we want to use the 2nd h1 when there are more than one h1's*/
```

### Assertion using expect syntax

```javascript
cy.get('[data-cy=“my-test-button”]') as('my-button');
cy.get('@my-button').then( $button_element => {
  expect($button_element.text()).to.equal('Test');
} );
```

### Using wrap to wrap an element back to a form that can use should syntax

```javascript
cy.get('[data-cy=“my-test-button”]').then( $button_element => {
  cy.wrap($button_element).should('exist');
} );
```

### Assert number of matching elements

```javascript
cy.get('[data-cy=“my-test-buttons”]').should('have.length', 2)
```

### Assert length of text in element

```javascript
cy.get('[data-cy=“my-test-button”]').invoke('text').should('have.length', 4)
```

### Assert that element exists/does not exist

```javascript
cy.get('[data-cy=“peekaboo”]').should('exist')
cy.get('[data-cy=“peekaboo”]').should('not.exist')
```

### Assert that element is visible / not visible

```javascript
cy.get('[data-cy=“peekaboo”]').should('be.visible')
cy.get('[data-cy=“peekaboo”]').should('not.be.visible')
```

### Assert that element has a certain class

```javascript
cy.get('[data-cy=“classy”]').should('have.class', 'my-class')
```

### Assert that element has a certain style

```javascript
cy.get('[data-cy=“classy”]').should('have.css', 'background-color', 'red')
```

## Aliasing

```javascript
cy.get('[data-cy=“my-test-button”]') as('my-button');
cy.get('@my-button').invoke('text').should('equal', 'Test')
```

## Pause execution to allow time to debug

```javascript
cy.get('[data-cy=“my-test-option-dropdown”]').trigger('mouseover').debug();
cy.get('[data-cy=“my-test-option-dropdown”]').trigger('mouseover').then(() => {
  debugger;
);
```

## Read environment variable

```javascript
/*Prefix the env var with 'CYPRESS_'. For eg, CYPRESS_TEST_VAR=Test*/
Cypress.env('TEST_VAR');
```

## Misc

### Cypress code completion in VS Code in file

```javascript
/// <reference types=“Cypress” />
```

### Cypress code completion in VS Code for whole project

```javascript
/*In file jsconfig.json in root folder of project*/
{
  “Include”: [
    “./node_modules/cypress”,
    “cypress/**/*.js”
  ]
}
```
