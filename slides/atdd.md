# Effective Tests

---

## Qualities of Tests

* Readability
* Stability
  * to changes
  * to execution
* Speed

---

## ATDD

* Acceptance
* Test 
* Driven
* Development

---

![](img/atdd.png)

---

## ATDD vs BDD

* BDD is focused on **business scenarios**
* BDD abstracts from interface
* BDD requires implementation for each step

---

## E2E Test Before Code

* to have a scope for the feature
* to plan interaction scenario
* to think about interface

---

## Can we predict future?

```js
I open page '/checkout'
I fill field 'First Name', 'Michael'
I fill field 'Last Name', 'Bodnarchuk'
I select option 'shipment', 'local collection'
I click 'Next'
I click 'Pay with Card'
I fill field 'Card number', '12334556789'
```

---

## Is this a test?

# YES!

(a failing test)

---

## How To Keep It Simple

* Keep the same code
* Keep semantic locators
* Focus on test scenario
* Hide implementation details

---

## How to Keep it Stable

* Pick stable parts of application
* Use element captions/ids/texts
* Retry failed steps
* Wait for changes on page

---

## How to Locate Elements

* CSS *(most common)*
* XPath *(most powerful)*
* Button | Link Texts
* Field Names *(most stable)*
* Field Labels *(most readable)*

---

## Locators

* Short
* Semantic
* Flexible on position

---

## ATDD Bonuses

* We focus on a persona, not on web elements
* We start with a simplest locators
* We start with business critical scenarios
* We get an effective test
* We never go untested

