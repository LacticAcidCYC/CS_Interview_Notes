# CSRF (Cross-Site Request Forgery) Attacks & Protection

## 1. What is CSRF (also called 'one-click attack' or 'session riding')

Cross-Site Request Forgery (CSRF) is an attack outlined in the [OWASP Top 10](https://www.veracode.com/directory/owasp-top-10) whereby **a malicious website will send a request to a web application that a user is already authenticated against from a different website**. This way an attacker can access functionality in a target web application via the victim's already authenticated browser. Targets include web applications like social media, in-browser email clients, online banking and web interfaces for network devices.



## 2. CSRF Basic

- Malicious requests are sent from a site that a user visits to another site that the attacker believes the victim is validated against.
- The malicious requests are routed to the target site via the victim’s browser, which is authenticated against the target site.
- The vulnerability lies in the affected web application, not the victim’s browser or the site hosting the CSRF.



## 3. CSRF Attack Example

### (1) A user logged in the WebA and visit the pages on it. Simultaneously, WebA passed user's authentication and generate cookies in user's browser.

![pic1](http://ww1.sinaimg.cn/large/691e2a69ly1fi5hfhg98qj20dh04ea9x.jpg)



### (2) Now let’s say he happens to visit WebB.

![pic2](https://ws1.sinaimg.cn/large/691e2a69ly1fi5hiszzjij20ob04s0sp.jpg)



### (3)  It just so happens that this site is trying to attack people who uses WebA and has set up a CSRF attack on its site. For example, the attack will transfer $1,500.00 to account number 123456789. Somewhere on somemalicioussite.com, attackers have added this line of code:

```html
<iframe src="//www.veracode.com/%3Ca%20href%3D"http://examplebank.com/app/transferFunds?amount=1500&destinationAccount=123456789">http://examplebank.com/app/transferFunds?amount=1500&destinationAccount=..." >
```

![pic3](https://ws1.sinaimg.cn/large/691e2a69ly1fi5hoctu0yj20oh06saa3.jpg)



### (4) Upon loading that iframe, browser will send that request to WebA with the cookies generated in #1

![pic4](https://ws1.sinaimg.cn/large/691e2a69ly1fi5iea3blrj20op08mmxb.jpg)



### (5) WebA now cannot figure out the where this request came from. Because the browser visited it with the cookie message for authentication the request will be processed and send $1,500.00 to account 123456789.

![pic5](https://ws1.sinaimg.cn/large/691e2a69ly1fi5ik7sfgzj20pr0ctt8z.jpg)



## 4. Preventing Cross-Site Request Forgery (CSRF) Vulnerabilities

### (1) Only use JSON API

Using JavaScript to make AJAX request is limited when doing cross-site request. And you cannot just use form to submit JSON. Thus, only accepting JSON can probably avoid most of the CSRF attack.

### (2) Authenticate the 'Referer' field in HTTP request

According to the HTTP protocol, there is a field in HTTP head called `Referer`. The `Referer` request header contains the address of the previous web page from which a link to the currently requested page was followed. The `Referer` header allows servers to identify where people are visiting them from.

[mozilla](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Referer)

### (3) Append Tokens to each Request

The most common method to prevent Cross-Site Request Forgery (CSRF) attacks is to append [CSRF tokens](https://www.veracode.com/security/csrf-token) to each request and associate them with the user’s session. Such tokens should at a minimum be unique per user session, but can also be unique per request. By including a challenge token with each request, the developer can ensure that the request is valid and not coming from a source other than the user.



## Reference List

(1) [jianshu](https://www.jianshu.com/p/00fa457f6d3e)

(2) [veracode](https://www.veracode.com/security/csrf)