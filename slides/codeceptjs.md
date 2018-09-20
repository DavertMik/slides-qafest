# CodeceptJS

![](img/cjs-base.png)

[codecept.io](https://codecept.io)

---

## CodeceptJS

* **end to end** testing framework
* helpers for popular testing backend
* high-level **unified APIs** for all backends
* **~15K** week installations 

[codecept.io](https://codecept.io)

---

### Sample Test

```js
Scenario('todomvc', (I) => {
  I.amOnPage('http://todomvc.com/examples/react/');
  I.waitForElement('.new-todo');
  I.dontSeeElement('.todo-count');
  I.fillField('What needs to be done?', 'Write a guide');
  I.pressKey('Enter');
  I.see('Write a guide', '.todo-list');
  I.see('1 item left', '.todo-count');
  I.fillField('What needs to be done?', 'Write a test');
  I.pressKey('Enter');
  I.see('Write a test', '.todo-list');
  I.see('2 items left', '.todo-count');
  I.fillField('What needs to be done?', 'Write a code');
  I.pressKey('Enter');
  I.see('Write a code', '.todo-list');
  I.see('3 items left', '.todo-count');
});
```

---

![](img/todo.gif)

---

### Goals

* Focus on scenario not on implementation
* Easy to read and write
* Separate test code from support code

---

#### Reability & PageObjects 

```js
Scenario('post article', async (I, loginPage) => {  
  const user = await I.createUser('davert');
  loginPage.login(davert);
  // ..
  I.see('User logged in', loginPage.messageBox);
})

```

---

#### Write test in your language

```js
Scenario('пробую написать реферат', (Я) => {
    Я.на_странице('http://yandex.ru/referats');
    Я.вижу("Написать реферат по");
    Я.выбираю_опцию('Психологии');
    Я.кликаю("Написать реферат");
    Я.вижу("Реферат по психологии");
});
```

---

## Readability bonus

We don't define how the test is executed!

---

### Architecture

![](img/codeceptjs-backends.svg)

---

## Tradeoffs

* Relying on a framework
* Dealing with abstractions

---

## Anyway...

---

## We aim for 

* Readability
* Stability
* Speed
* **Maintainability**

---

## Maintainability in CodeceptJS

* Interactive shell for debug
* Step-by-step screenshot reports
* Allure reporter support
* Locator builder

```js
locate('//table')
  .find('a')
  .withText('Edit')
  .as('edit button')
```

---

## Questions

* **Michael Bodnarchuk @davert**
* Consultant at **SDCLabs** 
* Author of CodeceptJS: [codecept.io](https://codecept.io)
 
