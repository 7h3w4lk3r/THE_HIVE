# ⭕ Authentication Mechanisms

Authentication is the process of verifying the identity of a given user or client. In other words, it involves making sure that they really are who they claim to be

## <mark style="color:red;">Vulnerabilities in password-based login</mark>

### <mark style="color:orange;">Username Enumeration</mark>

* Username enumeration via different responses when you try to brute-force and analysis each response:\
  &#x20;  \- If 'Response length' is too long\
  &#x20;  \- If 'Response Completed Time' is too long\
  &#x20;  \- Different 'Response Text'
* 'X-forward-for': a mechanism to identify real client IP in request header & a good way for bypass brute-force protection (IP block protection)

### <mark style="color:orange;">Many failed request & IP Block</mark>

List of payloads should alternates between a valid username and a invalid username.\
**Example**

```
USER-LIST: test/admin/test/admin/test
PASS-LIST: aaaa/ABCDE/bbbb/EFGHI/cccc
```

### <mark style="color:orange;">Account Locking</mark>

1. Find maximum you can try a username (EX: 3)
2. Create an username-list for username enumeration and repeat each username more than max-try-number(ex: test/test/test/test)
3. Start brute-force for username enumeration
4. Start brute-force for each user with a password-list
5. "Username Enumeration" method (Response Text different)

### <mark style="color:orange;">HTTP basic authentication</mark>

Find a Bug in implementation!\
**Example**\
If user's certificate send in HTTP header like `Authorization: Basic base64(username:password)`, you can brute-force it like all above solutions.

## <mark style="color:red;">Vulnerabilities in multi-factor authentication</mark>

Some useful two-factor authentication tokens:

* RSA token or keypad device
* Send SMS/Email verification codes

### <mark style="color:orange;">Bypassing</mark>

**Example**\
If username and password form in page-1 and two-factor authentication form in page-2:

* If username, password and 2FA is true, you got to a panel (EX: /my-account)
* In page-1 enter victim username and password and in page-2, change the path to panel URL

### <mark style="color:orange;">Flawed in logic</mark>

**Example**

* If in response page we have cookie like: `Set-Cookie: account=`<mark style="color:blue;">`test`</mark>
* Change username to victim like: `Set-Cookie: account=`<mark style="color:red;">`victim`</mark>

### <mark style="color:orange;">Brute-forcing 2FA verification codes</mark>

* Sometimes if you enter the wrong code twice in page-2, you will be logged out again and redirect to page-1 (Enter username and password). In this case you should save flow of request(EX: GET /login-1 --> POST /login-1 --> GET /login-2)
* Use 'Project Options/Session Handling Rules' in Burp Suite (macro) and do fallowing state:\
  1- In Burp, go to "Project options", "Sessions". In the "Session Handling Rules" panel, click "Add". The "Session handling rule editor" dialog opens \
  2- In the dialog, go to the "Scope" tab. Under "URL Scope", select the option "Include all URLs". Go back to the "Details" tab and under "Rule Actions", click "Add", "Run a macro"\
  3- Under "Select macro" click "Add" to open the "Macro Recorder". Select the following 3 requests(EX: GET /login-1 --> POST /login-1 --> GET /login-2)\
  4- Use 'Intruder' and brute-force verification-code parameter whit one 'Resource Pool'

## <mark style="color:red;">Vulnerabilities in other authentication mechanisms</mark>

### <mark style="color:orange;">Password reset poisoning via middleware</mark>

<mark style="color:purple;">**X-Forwarded-Host**</mark>: Host names and ports of reverse proxies(load balancer, CDNs) may differ from the origin server handling the request('Host' header), in that case the 'X-Forwarded-Host' request header is useful to determine which Host was originally used.

### <mark style="color:orange;">Changing user passwords</mark>

1.  Find change password request. HTTP request parameter like this:

    `username=VICTIM&current-password=777&new-password-1=123&new-password-2=123`
2.  Brute-force 'current-password' with victim username like:

    `username=VICTIM&current-password=`<mark style="color:yellow;">`FOO`</mark>`&new-password-1=PASS&new-password-2=DIFF-PASS`
3. Find valid password from grep-match a text
4. Login to victim account
