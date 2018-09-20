<!-- data-separator-vertical="\n\n\n" data-background="#A0C66B" -->

## Plan for Today

1. Setup Laravel+Vue application
1. Install Selenium Server
1. Write acceptance tests
1. Learn about locators (CSS, XPath)
1. Dealing with JavaScript (selectize, angular, vue.js)
1. Using Recorder Extension
1. Page Object pattern
1. Learn about managing data

---

### Setup Project

Project: Ribbon https://github.com/DavertMik/ribbbon

#### Install Project & Run Server

```
git clone https://github.com/DavertMik/ribbbon.git
cd ribbon
composer update
cp .env.example .env
```



edit .env file:

```
DB_HOST=localhost
DB_DATABASE=ribbon
DB_USERNAME=root
DB_PASSWORD=

CACHE_DRIVER=file
SESSION_DRIVER=file
QUEUE_DRIVER=sync

MAIL_DRIVER=log
MAIL_HOST=localhost
MAIL_PORT=25
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
```

run commands

```
echo "create database ribbon" | mysql -uroot
php artisan migrate
php artisan db:seed
php artisan serve
```



#### Prepare Codeception

```
composer require --dev codeception/codeception
php vendor/bin/codecept bootstrap
```

* acceptance, ~~functional, unit~~ test suites created


Configure Codeception (tests/acceptance.suite.yml):

```yml
class_name: AcceptanceTester
modules:
    enabled:
        - WebDriver:
            url: http://127.0.0.1:8000
            browser: chrome
        - \Helper\Acceptance
```



## Open Ribbon Application 

```
https://localhost:8000
```

* clients
* projects
* tasks


### Prepare Selenium

**Download**: http://www.seleniumhq.org/download/

**Download** ChromeDriver: https://sites.google.com/a/chromium.org/chromedriver/downloads

for MacOS: `brew install chromedriver`

--- 
Start:

```
java -jar selenium-server-standalone-3.x.x.jar 
```

***

or headlessly, using Docker:

```
docker run --net=host selenium/standalone-chrome:3.x.x 
```

(optionally)

---

## First Test: "Create Task"


Try Codeception in action

```
./vendor/bin/codecept console acceptance
```


* Prepare initial data:
 * Register
 * Create Client
 * Create Project


```
./vendor/bin/codecept g:cest acceptance Tasks
```


Make a first test

```php
<?php
class TaskCest
{
    public function createTask(AcceptanceTester $I)
    {
        $I->amOnPage('/projects/2');
        $I->click('New Task');        
        $I->seeElement('.new-task');
    }
}
```


* Find which elements are needed to create a task
* Find how to locate those elements.
* What is expected result?


### Locators

|      |     |
|---|---|
| CSS | `$I->seeElement('.new-task')` |
| XPath | `$I->seeElement('//table/tr/td[2]')` |
| Fuzzy | `$I->click('Hello')` |
| Strict | `$I->click(['xpath' => '//nav/a[2]']);` |



```php
public function createTask(AcceptanceTester $I)
{
    $I->amOnPage('/projects/2');
    $I->click('New Task');
    $I->seeElement('.new-task');
    $I->fillField('.new-task .first', 'Please fix that');
    $I->fillField('.new-task textarea', 'This is very important task');
    $I->click('Save', '.new-task');
    $I->waitForElementNotVisible('.new-task');
    $I->see('Please fix that', '.task-list');
}
```


* **HINT** Use the simplest locator as possible
* **HINT** Rely on the most stable locators
* **HINT** Add special classes to anonymous elements


* Edit `resources/views/ins/project/partials/forms`
* Add lc- style locators.
* Rewrite test:

```php
public function createTask(AcceptanceTester $I)
{
    $I->amOnPage('/projects/2');
    $I->click('New Task');
    $I->seeElement('.new-task');
    $I->fillField('.new-task .lc-title', 'Please fix that');
    $I->fillField('.new-task .lc-description', 'This is very important task');
    $I->click('Save', '.new-task');
    $I->waitForElementNotVisible('.new-task');
    $I->see('Please fix that', '.task-list');
}
```

---

## Running Chrome Headlessly

Update `acceptance.suite.yml`

```
modules:
    enabled:
        - WebDriver:
            url: http://127.0.0.1:8000
            browser: chrome
            capabilities:
              chromeOptions:
                args: ["--headless", "--disable-gpu"]
```

---

## Write More Tests

* Move login logic to `_before()`
* Add `changePriority` test
* Add `deleteTask` Test

---

## JS Issues

Enhance Application with Selectize.js

```
git branch selectize
git checkout selectize
git pull origin selectize
git checkout master
git merge selectize
```


Run change priority test

```
./vendor/bin/codecept run tests/acceptance/TasksCest:changePriority
```


### Failed :( 

**selectOption** doesn't work with non-native selects.

* Make a custom method to set value in `selectize` selects. 
* Use [Selectize API](https://github.com/selectize/selectize.js/blob/master/docs/api.md) to change value. 
* Use `executeJS` to execute JS code
* Update `AcceptanceTester` to include `selectizeOption` method


```php
public function selectizeOption($css, $value)
{
    $this->executeJS("$('$css')[0].selectize.setValue('$value')");
}
```

---


## JS Issues Part 2: Single Page Apps

* EventLoop: Rendering in SPA 
* Waiting for Loop to finish. [Example in AngularJS](github.com/Codeception/Codeception/blob/2.2/src/Codeception/Module/AngularJS.php#L112)
* `nextTick` in Vue. See [JS Fiddle](https://jsfiddle.net/2vxdh6nw/).


* **HINT** write `waitForNextTick` method in AcceptanceTester to wait for Vue.nextTick() 
* **HINT** write `waitForAjaxRequests` in JQuery

```php
$I->waitForJS("return $.active == 0;", 60);
```

* Advanced: implement `waitForNextTick`, `waitForAjaxRequests` into Helper\Acceptance::afterStep, following AngularJS example.

---

## Follow Tests with Recorder

Enable `Codeception\Extension\Recorder` in `acceptance.suite.yml`:

```yaml
extensions:
    enabled:
        - Codeception\Extension\Recorder:
          delete_successful: false
```

And run the tests!

---

## Refactoring

Using Page Objects

```
./vendor/bin/codecept g:page Project
```


### Reusing Test Code

* Helpers - implement custom steps ([facebook/webDriver]() API)
* Actor - group common steps into actions
* PageObject - group common actions for a page


#### Helper

* Located in `tests/_support/Helper`
* Custom modules (can extend Codeception Modules)
* When Codeception Modules are not enough


#### Actor

* represents `$I`
* located in `tests/_support` 
* for acceptance tests it is **AcceptanceTester**
* includes proxy methods for modules (`tests/_support/_generated`)
* includes custom actions


```php
class AcceptanceTester extends \Codeception\Actor
{
    // proxy method to modules
    use _generated\AcceptanceTesterActions;

    public function logIn()
    {
        $this->amOnPage('/login');
        $this->submitForm('.special-form form', [
            'email' => 'admin@admin.com',
            'password' => 'adminsadmins'
        ]);
    }
}
```


#### PageObjects

* represent a page on a site
* contains locators (elements) of a page
* contains scenarios can be done on a page 



```php
namespace Page\Acceptance;

class Project
{
    // inject $I
    public function __construct(\AcceptanceTester $I)
    {
        $this->acceptanceTester = $I;
    }

    // locators
    public $taskList = '.task-list';

    // data
    public $defaultData = ['title' => 'taskXXX'];

    // logic
    public function createNewTask($title, $description, $priotity = null)
    {
      // ....
    }
}
```


## Delete Task Refactored

* use PageObject to create new task
* stpore locators in PageObject
* store default data in PageObject
* implement method to get dynamic locators



```php
/**
 * @depends createTask
 */
public function deleteTask(AcceptanceTester $I, \Page\ProjectPage $project)
{
    $taskName = $project->addNewTask(2);
    $taskElement = $project->getTaskElement($taskName);
    $I->moveMouseOver($taskElement);
    $I->seeElement("$taskElement {$project->deleteButton}");
    $I->click("$taskElement {$project->deleteButton}");
    $I->waitForElementVisible($project->popup);
    $I->click($project->popupConfirmButton);
    $I->waitForElementNotVisible($project->popup);
    $I->dontSee($taskName);
}
```


## Get Task ID from a Page

```php
public function getTaskElement($taskName)
{
    $class = $this->acceptanceTester->grabAttributeFrom(
      Locator::contains($this->taskList . ' li', $taskName), 
      'class');

    if (preg_match('~task-(\d+?)~', $class, $matches)) {
        codecept_debug('Task Id: ' . $matches[1]);
        return $matches[1];
    }
    return null;
}
```


## Hide Implementation Complexity

```php
/**
 * @depends createTask
 */
public function deleteTask(AcceptanceTester $I, \Page\ProjectPage $project)
{
    $taskName = $project->addNewTask($this->currentProject);
    $projectPage->deleteTask($taskName);
    $I->dontSee($taskName);
}
```

---

## Lifehacks


### Codeception\Util\Locator

* `combine` - merge 2 locators
* `contains` - element that contains test
* `elementAt` - get nth element in scope
* `firstElement` - get the first element in scope
* `find` - get element by locator and attributes
* `href` - get link by href
* `lastElement` - get last element in scope
* `tabIndex` - get element by tab position


### Element At Position


```php
$I->click('Edit', Locator::elementAt('#data-grid tr', 3));
```

![](img/grid.png)


## Other Locators

* Locate element with text inside

```php
Locator::contains('div[contenteditable]',  'hello world');
```

* Combine different selectors

```php
$I->see('Title', Locator::combine('h1','h2','h3'));

```

* [and more](http://codeception.com/docs/reference/Locator)

---

## Session Snapshots

Don't login for each test

```php
if ($I->loadSessionSnapshot('login')) {
    return;
} // logged in
$I->amOnPage('/login');
$I->submitForm('#loginForm', [
    'login' => $name, 
    'password' => $password
]);
// saving snapshot
$I->saveSessionSnapshot('login');

```

---


### Wait For Elements

* waitForElement
* smartWait
* performOn


### SmartWait

* works with Id/CSS/XPath/Strict locators
* replaces `waitForElement`
* add `wait: 5` to config
* doesn't work with fuzzy locators

---

### Multi-Session Testing

```php
$joe = $I->haveFriend('joe');
$joe->does(function($I) {
   $I->amOnPage('/'); 
});
```

---

## Data Management

* Using Laravel ORM (private)
* Using DB module (private)
* Using REST API (public)


#### Practice: Create Client and Project for tests

Add REST+PhpBrowser to modules list

```yaml
- REST:
    depends: PhpBrowser
    url: 'http://127.0.0.1:8000'
```

* Use `$I->sendPOST()` to send requests to API
* Use `$I->sendPOST('/login', [])` to login


* Implement `haveClient` method using REST API in AcceptanceTester
* Implement `haveProject` method using REST API in AcceptanceTester


```php
public function haveClient($name)
{
    $this->sendPOST('/login', [
        'email' => 'admin@admin.com',
        'password' => '123456789'
    ]);
    $this->sendPOST('/api/clients', ['user_id' => 1, 'name' => $name]);
    $this->seeResponseCodeIs(200);
}
```


### Cleanup

* move `haveClient`, `haveProject` to `\Helper\Acceptance`
* use `_after` hook to cleanup the created instances

---

## Selenium Limitations

* Download 
* Receiving Emails (use [MailCatcher](https://github.com/captbaritone/codeception-mailcatcher-module) instead)
* Headers

---

## Codeception Limitations

* Not everything from Webdriver is implemented
* Implement complex actions using WebDriver protocol


### Implementing Custom Drag&Drop

Follow [Java Example](https://stackoverflow.com/questions/14210051/how-to-automate-drag-drop-functionality-using-selenium-webdriver-java)

```java
Actions builder = new Actions(driver);

builder.keyDown(Keys.CONTROL)
   .click(someElement)
   .click(someOtherElement)
   .keyUp(Keys.CONTROL);
```


Add to `_support\Helper\Acceptance`:

```php
public function myDragAndDrop($from, $to)
{
    // locating webdriver element (first)
    $fromEl = $this->getModule('WebDriver')->_findElements($from)[0];
    $toEl = $this->getModule('WebDriver')->_findElements($to)[0];
    /** @var $webdriver \Facebook\WebDriver\Remote\RemoteWebDriver  **/
    $webdriver = $this->getModule('WebDriver')->webDriver;
    $actions = new WebDriverActions($webdriver);
    $actions->keyDown(WebDriverKeys::CONTROL)
        ->click($fromEl)
        ->click($toEl)
        ->keyUp(WebDriverKeys::CONTROL)
        ->perform();
}
```

---

## Practice: Test TodoMVC application

http://todomvc.com/examples/react/#/


### Tasks

* create todo
* edit todo
* delete todo
* clear completed
* filter todos

---

### Free Ride