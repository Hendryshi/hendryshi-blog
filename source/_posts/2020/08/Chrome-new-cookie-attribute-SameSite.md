---
title: Chrome new cookie attribute SameSite
cover: https://img.hendryshi.com/i/2023/04/6431bd0d685d5.webp
tags:
  - JavaEE
  - Apache
  - Chrome
categories:
  - Tech
abbrlink: '41234836'
date: 2020-08-15 19:42:08
---

About chrome's new version 80: Cookie attribute "SameSite = none"

## **Context**
Recently we met a strange problem in our program: when users finished payment in site A and went back to site B, the cookie of site B was lost. While the site B use a anti-CSRF that always linked to a cookie. the user found an error message in the page said that "Cannot validate CSRF because your session is lost".

![cookie-error](https://img.hendryshi.com/i/2023/04/6431bd1e81340.webp)

After investigating this problem, we found that the chrome introduced a new cookie attribute "SameSite = None" after their upgrade of version 80 that will block the cookie if it's a cross-site request.

<!-- more -->

## **Introduction**

### *Cookie*
Cookies is a small piece of data that a server sends to the user's web browser. The browser may store it and send it back with later requests to the same server. Typically, it's used to tell if two requests came from the same browser - Keeping a user logged-in. For example, It remembers stateful information for the stateless HTTP protocol.

The first time browser post a request to the server, the server will return a response with a special cookie. Then the browser will use this cookie in every request if it posts to the same server. 

![cookie](https://img.hendryshi.com/i/2023/04/6431bd25523ce.webp)

### *Cookie attribute*
Cookies can be secured by properly setting cookie attributes. These attributes are:
- **Secure**: True/False
- **Domain**: 
- **Path**:
- **HTTPOnly**: True/False
- **Expires**:
- **SameSite**: Strict/Lax/None
  
### New Attribute SameSite = None
SameSite values can be set to :
    - **Strict**: cookie is sent only within the same site
    - **Lax**: sent within the same site and in Get request from other sites
    - **None**: sent in a secure context (HTTPS)  to clearly indicate that the cookie is sent in a third–party context.

![sameSite](https://img.hendryshi.com/i/2023/04/6431bd2f911c7.webp)

Before the upgrade of chrome if the attribute of the cookies was not specified it implicitly stated that the cookie was sent in all contexts. The update of Chrome has made explicit this behavior in introducing a new value SameSite=None. As a result cookies without an explicitly SameSite attribute are treated as `SameSite = Lax` and if they are from third party in POST they are rejected by the browser.

![crossSite](https://img.hendryshi.com/i/2023/04/6431bd3738232.webp)

{% note info modern no-icon %}
You must ensure that you pair SameSite=None with the Secure attribute together.
{% endnote %}

## **Solution**

### *Set directly in Chrome*
If you tap `chrome://flags` and search keyword `SameSite`. Then you can disable this sameSite default setting and it will solve the problem.

![solution1](https://img.hendryshi.com/i/2023/04/6431bd6da2e40.webp)

### *Configuration in the Apache Server*

We have not found any solution that we can configure in the server JBoss ESP 7.0. Either in the file `web.xml` or in the file `context.xml`. There has no tag of SameSite. Maybe the newest version of JBoss will introduce this new attribute.
However we found that in the apache configuration file we can set the header cookie like this. That will resolve our problem also.

```
    Header edit Set-Cookie ^(JSESSIONID.*)$ "$1; SameSite=None; Secure"
```
### *Set in the code directly*

The last solution is to set the cookie attribute directly in the code when the first time the server response the browser:

``` Java
    String cookieString="cookieName=cookieValue; Secure; SameSite=None"
    response.setHeader("Set-Cookie", cookieString);
```