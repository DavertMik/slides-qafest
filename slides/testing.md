## Testing Business Expectations

---

### Why Do We Test 

* To ensure software works as expected
* To discover bugs in software (before users)
* To measure performance 
* To seek for security issues

---

> We automate tests to execute them at any time

---

### What Tests We Can Automate

* **To ensure software works as expected**
* ~~To discover bugs in software (before users)~~
* **To measure performance**
* ~~To seek for security issues~~

---

### Automated Testing

* To establish trust
* For constant changes
* To stabilize current codebase

---

### No Automated Testing When

* Features need to be delivered as fast as possible
* Project won't be changed in future
* Management is not interested in automated testing

---

### What Is Hard To Test

* Asynchronous interactions (JavaScript, Queues)
* 3rd party APIs
* Real data
* Captchas

---

## End to End Tests

* Check the real user experience (UI/UX)
* Work using browser
* Require real webserver, database, etc

---

## What to Test

---

### Priority First

* Crucial **business scenarios**
* Algorithms, functions with complex logic
* Everything that is hard to test manually

---

### What is in a Test

* Arrange (Given)
* Act (When)
* Assert (Then)

---

### Start With General


```gherkin
Feature: customer registration

Background:
  Given I am unregistered customer

Scenario: registering successfully
  When I register
  Then I should be registered
```

---

## Add Details

```gherkin
Scenario: registering successfully
  When I register with
    | Name      | davert              |
    | Email     | davert@sdclabs.com  |
    | Password  | 123456              |

  Then I should be registered
  And I receive confirmation email

```

---

## How to Test

---

### End to End and Unit testing

* **End to End**: test from the public interface
* **Unit**: test from the source code

---

## Why End to End Testing

* When internal codebase is unstable and fast growing
* When we have public interface for testing
* When we need to check real UI/UX
