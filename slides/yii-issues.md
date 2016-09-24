### Интеграция в Yii2

* Первый фреймворк, внедривший Codeception
* Плохая документация и примеры
* Использование антипаттернов и костылей
* Тестирование так и не стало мейнстримом

---

### Несколько сценариев в одном тесте

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

### "Универсальный" тест

Попытка написать один тест под разные модули.

```php
$I->seeLink('Logout (erau)');
$I->dontSeeLink('Login');
$I->dontSeeLink('Signup');
/** Uncomment if using WebDriver
 * $I->click('Logout (erau)');
 * $I->dontSeeLink('Logout (erau)');
 * $I->seeLink('Login');
 */
```


---

## И другие проблемы

* Дублирующиеся файлы (LoginCept)
* WTF??? → [Yii2-codeception](https://github.com/yiisoft/yii2-codeception)

---

### Мы это исправили!

![](img/yiimeetup_photo.jpg)

---

### Итог работы

* Переписаны тесты в yii-app-basic, yii-app-advanced
* Улучшен и дополнен модуль Yii2
* Тесты с фикстурами, почтой, аутентификацией

---

### Написана документация

codeception.com/for/yii

![](img/yii_for.png)