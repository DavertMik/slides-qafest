```
Story: Account Holder withdraws cash
 
As an Account Holder
I want to withdraw cash from an ATM
So that I can get money when the bank is closed
 
Scenario 1: Account has sufficient funds
Given the account balance is $100
 And the card is valid
 And the machine contains enough money
When the Account Holder requests $20
Then the ATM should dispense $20
 And the account balance should be $80
 And the card should be returned
 ```