# Bad Practices

---

## Long-Long-Scenarios

```php
$I->amGoingTo('try to login with empty credentials');
$loginPage->login('', '');
$I->expectTo('see validations errors');
$I->see('Username cannot be blank.');
$I->see('Password cannot be blank.');
$I->amGoingTo('try to login with wrong credentials');
$loginPage->login('admin', 'wrong');
$I->expectTo('see validations errors');
$I->see('Incorrect username or password.');
$I->amGoingTo('try to login with correct credentials');
$loginPage->login('admin', 'admin');
$I->expectTo('see user info');
$I->see('Logout (admin)');
```

---

## Solution

* Tests should be atomic
* One feature per tests
* CRUD is 4 tests, not one
* **Don't use Cept format**

---

## Universal Test

```php
$I->seeLink('Logout');
$I->dontSeeLink('Login');
$I->dontSeeLink('Signup');
/** Uncomment if using WebDriver
 * $I->click('Logout (erau)');
 * $I->dontSeeLink('Logout (erau)');
 * $I->seeLink('Login');
 */
```

---

## Solution

* Choose the way tests are executed
* Despite of similar modules those tests are different.
* Use acceptance testing for happy path scenarios

---

## Support code inside test

```php
$actualUrl = $I->executeJS("return location.href");

// conditionals inside tests are bad as well..
if ($actualUrl == $stagingServer) {
  $I->haveInDatabase('user_settings', ['id' => 1, 'notifications' => 1]);
  $I->loginAsSomebody('My_login', 'My_passwor');
} else {
  $I->loginAsSuperAdmin();
}
$I->click('Menu');
// .....
```

---

## Solution

* Move complex logic to Helper class
* Use Extensions or GroupObjects to hook the events
* Have no conditionals in tests
