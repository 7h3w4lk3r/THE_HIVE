# ⭕ Access Control (Authorization)

Access control (or authorization) is the application of constraints on who can perform attempted actions or access resources that they have requested. In the context of web applications, access control is dependent on authentication and session management:

* <mark style="color:purple;">**Authentication**</mark> identifies the user and confirms that they are who they say they are.
* <mark style="color:purple;">**Session Management**</mark> identifies which subsequent HTTP requests are being made by that same user.
* <mark style="color:purple;">**Access Control**</mark> determines whether the user is allowed to carry out the action that they are attempting to perform.

## <mark style="color:red;">Categories</mark>

* <mark style="background-color:red;">**Vertical Access Controls**</mark> Different types of users have access to different application functions
* <mark style="background-color:red;">**Horizontal Access Controls**</mark> Different users have access to a subset of resources of the same type
* <mark style="background-color:red;">**Context-dependent Access Controls**</mark> Prevent a user performing actions in the wrong order
* <mark style="background-color:red;">**Location-based Access Aontrol**</mark> These access controls can often be circumvented by the use of web proxies, VPNs, or manipulation of client-side geolocation mechanisms

### <mark style="color:red;">Broken access controls</mark>

* [ ] Find hidden path like "/admin" in robots.txt file or JavaScript codes.
* [ ] Find hidden field, cookie, or query string parameter.
* [ ] Change request field in body for example "RoleID".
* [ ] Platform misconfiguration\
  Use non-standard HTTP request headers "**X-Original-URL**" and "**X-Rewrite-URL**" like:\
  `X-Original-URL: /admin/deleteUser`
* [ ] In some cases, an application does detect when the user is not permitted to access the resource, and returns a redirect to the login page. However, the response containing the redirect might still include some sensitive data belonging to the targeted user, so the attack is still successful.
* [ ] Insecure Direct Object references (IDOR): When an application uses user-supplied input to access objects directly and an attacker can modify the input to obtain unauthorized access\
  \- IDOR vulnerability with direct reference to database objects (horizontal privilege escalation)\
  \- IDOR vulnerability with direct reference to static files
* [ ] Referer-based Access Control: The "**Referer**" header is generally added to requests by browsers to indicate the page from which a request was initiated\
  \- Set a truth "Referer" header if the server check it. Then try to change something like session in cookie
