# Checklist

## The Web Application Hacker's Handbook

Web Application Hacker's Handbook Task checklist as a Github-Flavored Markdown file

### Contents

* [Recon and analysis](https://gist.github.com/gbedoya/10935137#recon-and-analysis)
* [Test handling of access](https://gist.github.com/gbedoya/10935137#test-handling-of-access)
* [Test handling of input](https://gist.github.com/gbedoya/10935137#test-handling-of-input)
* [Test application logic](https://gist.github.com/gbedoya/10935137#test-application-logic)
* [Assess application hosting](https://gist.github.com/gbedoya/10935137#assess-application-hosting)
* [Miscellaneous tests](https://gist.github.com/gbedoya/10935137#miscellaneous-tests)

### Task Checklist

#### Recon and analysis

* [ ] &#x20;Map visible content
* [ ] &#x20;Discover hidden & default content
* [ ] &#x20;Test for debug parameters
* [ ] &#x20;Identify data entry points
* [ ] &#x20;Identify the technologies used
* [ ] &#x20;Map the attack surface

#### Test handling of access

* [ ] &#x20;Authentication
* [ ] &#x20;Test password quality rules
* [ ] &#x20;Test for username enumeration
* [ ] &#x20;Test resilience to password guessing
* [ ] &#x20;Test any account recovery function
* [ ] &#x20;Test any "remember me" function
* [ ] &#x20;Test any impersonation function
* [ ] &#x20;Test username uniqueness
* [ ] &#x20;Check for unsafe distribution of credentials
* [ ] &#x20;Test for fail-open conditions
* [ ] &#x20;Test any multi-stage mechanisms
* [ ] &#x20;Session handling
* [ ] &#x20;Test tokens for meaning
* [ ] &#x20;Test tokens for predictability
* [ ] &#x20;Check for insecure transmission of tokens
* [ ] &#x20;Check for disclosure of tokens in logs
* [ ] &#x20;Check mapping of tokens to sessions
* [ ] &#x20;Check session termination
* [ ] &#x20;Check for session fixation
* [ ] &#x20;Check for cross-site request forgery
* [ ] &#x20;Check cookie scope
* [ ] &#x20;Access controls
* [ ] &#x20;Understand the access control requirements
* [ ] &#x20;Test effectiveness of controls, using multiple accounts if possible
* [ ] &#x20;Test for insecure access control methods (request parameters, Referer header, etc)

#### Test handling of input

* [ ] &#x20;Fuzz all request parameters
* [ ] &#x20;Test for SQL injection
* [ ] &#x20;Identify all reflected data
* [ ] &#x20;Test for reflected XSS
* [ ] &#x20;Test for HTTP header injection
* [ ] &#x20;Test for arbitrary redirection
* [ ] &#x20;Test for stored attacks
* [ ] &#x20;Test for OS command injection
* [ ] &#x20;Test for path traversal
* [ ] &#x20;Test for script injection
* [ ] &#x20;Test for file inclusion
* [ ] &#x20;Test for SMTP injection
* [ ] &#x20;Test for native software flaws (buffer overflow, integer bugs, format strings)
* [ ] &#x20;Test for SOAP injection
* [ ] &#x20;Test for LDAP injection
* [ ] &#x20;Test for XPath injection

#### Test application logic

* [ ] &#x20;Identify the logic attack surface
* [ ] &#x20;Test transmission of data via the client
* [ ] &#x20;Test for reliance on client-side input validation
* [ ] &#x20;Test any thick-client components (Java, ActiveX, Flash)
* [ ] &#x20;Test multi-stage processes for logic flaws
* [ ] &#x20;Test handling of incomplete input
* [ ] &#x20;Test trust boundaries
* [ ] &#x20;Test transaction logic

#### Assess application hosting

* [ ] &#x20;Test segregation in shared infrastructures
* [ ] &#x20;Test segregation between ASP-hosted applications
* [ ] &#x20;Test for web server vulnerabilities
* [ ] &#x20;Default credentials
* [ ] &#x20;Default content
* [ ] &#x20;Dangerous HTTP methods
* [ ] &#x20;Proxy functionality
* [ ] &#x20;Virtual hosting mis-configuration
* [ ] &#x20;Bugs in web server software

#### Miscellaneous tests

* [ ] &#x20;Check for DOM-based attacks
* [ ] &#x20;Check for frame injection
* [ ] &#x20;Check for local privacy vulnerabilities
* [ ] &#x20;Persistent cookies
* [ ] &#x20;Caching
* [ ] &#x20;Sensitive data in URL parameters
* [ ] &#x20;Forms with autocomplete enabled
* [ ] &#x20;Follow up any information leakage
* [ ] &#x20;Check for weak SSL ciphers

### References

{% embed url="http://mdsec.net/wahh/tasks.html" %}
