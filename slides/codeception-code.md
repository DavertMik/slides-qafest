## Пример реального теста

``` php
<?php
public function viewPlan(AcceptanceTester $I, \Page\Plan $planPage)
{    
  // создать начальные данные
  $this->planId = $planPage->createPlan(['name' => 'plan'.sq(1)]); 
  $I->amOnPage('/plans'); // перейти на страницу
  $I->expect('only one result is matched'); // комментарий
  $I->see('Displaying 1-1 of 1 result.','.summary'); // проверить наличие
  $I->click($planPage->viewButton); // использовать PageObject
  $I->see('plan'.sq(1).' Details', 'h1'); 
}
  ```