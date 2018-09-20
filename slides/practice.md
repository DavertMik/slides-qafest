## Practice

---

#### Booking.com

![](img/booking.png)

---

## Install CodeceptJS


```
npm install codeceptjs webdriverio --save-dev
```



Install Selenium Server + ChromeDriver:

```
[sudo] npm install -g selenium-standalone
selenium-standalone install
selenium-standalone start
```


---

## Bootstrap Project

```
node node_modules/.bin/codeceptjs init
```

* Select: WebDriverIO
* URL: http://booking.com

---

`codecept.json`

```json
{
  "tests": "./*_test.js",
  "timeout": 10000,
  "output": "./output",
  "helpers": {
    "WebDriverIO": {
      "url": "http://booking.com",
      "browser": "chrome"
    }
  },
  "include": {
    "I": "./steps_file.js"
  },
  "bootstrap": false,
  "mocha": {},
  "name": "demo-codecept-booking"
}
```

---


### Generate TypSecipt Definitions

```
node node_modules/.bin/codeceptjs def
```

(optional)


---

## First Test

```
node node_modules/.bin/codeceptjs gt
```


```js
Feature('Book');

Scenario('test something', (I) => {
  I.amOnPage('/');
  pause();
});

```

---

```js
I.fillField('ss', 'Tallinn')
I.click('li[data-label="Tallinn, Harjumaa, Estonia"]');
```

---

```js
/// <reference path="./steps.d.ts" />
Feature('Booking');

Scenario('Book a hotel', (I) => {
  I.amOnPage('/');
  I.fillField('ss', 'Tallinn');
  I.waitForVisible('.c-autocomplete__list');
  I.click(
    locate('li')
    .withAttr({ 'data-label': "Tallinn, Harjumaa, Estonia" })
  );
  pause();
  // I.click('li[data-label="Tallinn, Harjumaa, Estonia"]');
});
```

---

(skip this step ->)

## ~Use MomentJS to fill calendar~

```
npm i moment --save
```

```js
moment = require('moment');

const checkinDate = moment().add(3, 'months');
const checkoutDate = checkinDate.add(3, 'days');

I.fillField('checkin_monthday', checkinDate.format('DD'));
I.fillField('checkin_month', checkinDate.format('MM'));
I.fillField('checkin_year', checkinDate.format('YY'));
```

---

## Open first hotel

```js
  const hotelNames = await I.grabTextFrom('.sr-hotel__name');
  const hotelName = hotelNames[0];
  I.say(`I want to book at ${hotelName}`);
  within('//*[@id="hotellist_inner"]/div[1]', () => {
    I.see(hotelName);
    I.dontSee(hotelNames[1]);    
    I.click(hotelName);
  });
  I.switchToNextTab();
  I.see(hotelName, 'h2');
```

---

## Next Tasks

* Refactor to PageObjects
* Try different cities
* Finish booking

---

# Questions?

* Me: Michael Bodnarchuk
* Project: CodeceptJS
* Website: [codecept.io](http://codecept.io)
* Twitter: `@codeceptjs`
