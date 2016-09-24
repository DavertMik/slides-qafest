# Best Practices

---

## Функциональные тесты

* Тестируют работу приложения без реального UI.
* Эмулируют запросы-ответы к приложению
* Требуют Yii2 модуль
* Интегрируются с ActiveRecord, Mailer, Fixtures
* Описаны в виде сценариев

---

```php
// входим как админ
$I->amLoggedInAs(User::findByUsername('admin'));
// используем внутренний роут Yii2
$I->amOnRoute('tasks/new');
// отправляем форму (эмулируем запрос к приложению)
$I->submitForm($this->formId, [
    'TaskForm[title]' => 'Fix bug',
    'TaskForm[priroity]' => 'urgent',
]);
// проверяем значения в базе
$I->seeRecord('common\models\Task', [
    'task' => 'Fix bug'
]);
```

---

## Модульные тесты

* Тестируют компоненты системы в изоляции
* Без `Yii::$app`
* Без использования ActiveRecord
* С использованием Dependency Injection

---

### Примеров не будет

* Модульные тесты одинаковы везде
* Yii2 не способствует изоляции компонентов

---

### Интеграционные тесты

* Давайте называть их `unit` 😇
* Тестируют компоненты системы
* Используют БД, другие компоненты

---


```php
// tests/unit/PasswordResetRequestFormTest.php

class PasswordResetRequestFormTest extends \Codeception\Test\Unit
{

    // предоставляет доступ к модулям Codeception
    // 
    // $this->tester == $I
    public $tester;
    
    // или $this->getModule('Yii2')

}
```

---

```php
// вгружаем фикстуры
$this->tester->haveFixtures([
    'user' => [
        'class' => UserFixture::className(),
        'dataFile' => codecept_data_dir() . 'user.php'
    ]
]);
// получаем запись из фикстур
$userFixture = $this->tester->grabFixture('user', 0);

$model = new PasswordResetRequestForm();
$model->email = $userFixture['email'];
```

---

```php
$user = User::findOne(
  ['password_reset_token' => $userFixture['password_reset_token']]
);
$model->sendEmail();
// проверяем что письмо было отправлено
$this->tester->seeEmailIsSent();
// берем отправленное письмо 
$emailMessage = $this->tester->grabLastSentEmail();

// проверяем его
expect($emailMessage)->isInstanceOf('yii\mail\MessageInterface');
expect($emailMessage->getTo())
  ->hasKey($model->email);
expect($emailMessage->getFrom())
  ->hasKey(Yii::$app->params['supportEmail']);
```

---

## Приемочные тесты

* Проверяют работу системы вцелом
* Опираются на UI (в том числе JavaScript)
* Требуют БД, веб-сервер
* Работают через браузер, Selenium

---

```php
$I->amOnPage(Url::toRoute('/site/home'));
$I->see('My Company');
$I->seeLink('About');
$I->click('About');
$I->see('This is the About page.');
```

---

## PageObjects 

* выносим в них повторяющиеся элементы
* содержат локаторы элементов страницы
* логика работы со страницей
* вгружаются автоматически через DI

---

## Cest Files

* **Рекомендуемый** формат для сценарных тестов
* Автозагрузка PageObject'ов
* `@before`, `@after` хуки

---

## Пример реального теста

``` php
/**
 * @before login
 */
public function viewTask(AcceptanceTester $I, \Page\Task $taskPage)
{    
  // создать начальные данные
  $this->planId = $taskPage->createTask(['name' => 'task'.sq(1)]); 
  $I->amOnPage('/tasks'); // перейти на страницу
  $I->see('Displaying 1-1 of 1 result.','.summary'); // проверить наличие
  $I->click($taskPage->viewButton); // использовать PageObject
  $I->see('Task'.sq(1).' Description', 'h2'); 
}
```


---

### API тесты

* Могут работать как функиональные
* Используют Yii2 + REST модули
* Позволяют тестировать JSON/XML ответы

---

 GET /api/tickets/3

```json
{
  "ticket": {
    "id": 3,
    "from": "web",
    "description": "Lorem ipsum...",
    "priority": "important",
    "priority_value": 1,
    "report": {
      "user_agent": "Mozilla...",
      "url": "/tasks",      
      "window": "1280x525",
      "resolution": "1600x1200"
    },
    "reporter_info": {
      "name": "davert",
      "email": "davert@codeception.com",
    }
    "created_at": "2016-08-21T20:16:37Z",
    "updated_at": "2016-09-11T15:13:47Z"    
  }
}
```

---

```php
$I->sendGET('/api/tickets/3');
$I->seeResponseCodeIs(HttpCode::OK); // 200
$I->seeResponseIsJson();
// проверка данных
$I->seeResponseContainsJson([
'ticket' =>
  'id' => 3,
  'from' => 'web'
  'report' => [
    'url' => '/tasks'
  ]
]);

```

---


```php
// проверка структуры
$I->seeResponseMatchesJsonType([
'ticket' => [
  'id' => 'integer',
  'description' => 'string|null',
  'priority' => 'string',
  'created_at' => 'string:date',
  'reporter_info' => [
    'email' => 'string:email'
  ]
]]);

```

---

```php
// проверка наличия конкретного элемента
$I->seeResponseJsonMatchesJsonPath(
  '$ticket.report.url'
);
// или XPath
$I->seeResponseJsonMatchesXpath(
  '//ticket/report/url'
);

```

---


# Принципы хороших тестов

---

## Разделение ответственности

* делайте тесты атомарными
* тест должен быть сосредоточен на одной фиче/баге
* не дублируйте логику в acceptance => functional тестах
* вместо комментариев сделайте отдельный тест

---

## Разделение функциональности

Общие методы выносятся в actor class

```php
// внутри AcceptanceTester
public function loginAsUser()
{
    $this->amOnPage('/login');
    $this->fillField('Username', 'john_user');
    $this->fillField('Password', 'kitten');
    $this->click('Sign in');
}

// внутри теста
$I->loginAsUser();
```

---

## Куда что выносить

* **Tester**: действия необходимые для всех тестов
* **PageObject**: логика работы со страницей 
* **StepObject**: действия включающие несколько страниц
* **Helper**: реализация своих шагов

