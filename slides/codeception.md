# Codeception

![](http://codeception.com/images/logo.svg)

---

## Codeception

* **full stack** testing framework
* modules to solve **90% of common tasks**
* **unified APIs** for all modules
* **~3.7M** installations on Packagist

---

## Acceptance Tests

* Check the real user experience (UI/UX)
* Work using browser through Selenium
* Require database, webserver
* JavaScript included

---

### Selenium Webdriver Example

```php
// Custom method
$I->loginAsAdmin();
// Navigate to the Media Library
$I->amOnPage( '/wp-admin/media-new.php' );
$I->waitForText( 'Upload New Media' );

// Add new file
$I->attachFile( 'input[type="file"]', 'team.jpg' );

// Wait for upload
$I->waitForElement( '.edit-attachment', 20 );

// Assertion
$I->see( 'Edit Media' );
```

