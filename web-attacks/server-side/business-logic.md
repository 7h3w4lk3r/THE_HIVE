# ⭕ Business Logic

Business logic vulnerabilities are flaws in the design and implementation of an application that allow an attacker to elicit unintended behavior. This potentially enables attackers to manipulate legitimate functionality to achieve a malicious goal.

## <mark style="color:red;">Excessive trust in client-side controls (without server-side validation)</mark>

Change and test all parameters of HTTP requests

## <mark style="color:red;">Failing to handle unconventional input</mark>

### <mark style="color:orange;">Find bugs</mark>

1- Are there any limits that are imposed on the data?\
2- What happens when you reach those limits?\
3- Is any transformation or normalization being performed on your input?

<mark style="color:yellow;">**Example**</mark>

Consider a funds transfer between two bank accounts. This functionality will almost certainly check whether the sender has sufficient funds before completing the transfer:

```php
$transferAmount = $_POST['amount'];
$currentBalance = $user->getBalance();

if ($transferAmount <= $currentBalance) {
    // Complete the transfer
} else {
    // Block the transfer: insufficient funds
} 
```

**Solve**\
sent -$1000 to the victim's account, this might result in them receiving $1000 from the victim instead

<mark style="color:yellow;">**Example (Integer Over Flow)**</mark>

1- Send a very big integer for number of your order for example\
`productId=10&redir=PRODUCT&quantity=`<mark style="color:green;">**`999999999999`**</mark>\
`2-` The price has exceeded the maximum value permitted for an integer in the back-end programming language (2,147,483,647 $). As a result, the value has looped back around to the minimum possible value (-2,147,483,648 $).\
4-Try to change minimum price to cheap value (20 $)\
5- Buy your expensive orde

<mark style="color:yellow;">**Example**</mark>

1- Attacker has a mail server. Its domain is: attacker.com\
2- Target site, is a '/admin' path for users that has a foo.com email like: bar@foo.com\
3- Max length of email address is 255 character. Attacker can create an email like: aaaaa...aaaaa@foo.com\[255-char-len].attacker.com\
4- Server of target site send confirm email to 'aaa...aaa@foo.com.attacker.com' but it save this email for Attacke: aaaaa...aaaaa@foo.com\
5- Then Attacker can access to '/admin' path in his dashboard

> <mark style="color:red;">**Notice**</mark>
>
> * Trusted users won't always remain trustworthy
> * Users won't always supply mandatory input
> * Users won't always follow the intended sequence
> * Providing an encryption method (For example check and find formula of each cookie and try to create admin cookie)
