---
layout: post
title:  "Symfony: Force authentication on all paths"
date:   2015-07-30 12:00:00
categories: symfony
---

[Symfony firewall](http://symfony.com/doc/current/book/security.html)
can be configured to force authentication on every path in the application.
Create a login firewall with anonymous access whose patterns
match your login form paths. It must be first on the list of firewalls.
Then, set your security area to disallow anonymous.

Once you configure the firewall this way, all visitors will be forced to
the login form.

{% highlight yaml startinline %}
security:
    firewalls:
        # ensures anonymous access to the login forms
        login:
            pattern: ^.*/login$
            anonymous: true
        
        # force login form authentication on all other paths
        secured_area:
            pattern: ^/
            anonymous: false

            form_login:
                check_path: security_login_check
                login_path: security_login_form
                csrf_provider: security.csrf.token_manager

            logout:
                path: security_logout
                target: homepage

    access_control:
        - { path: ^/admin, roles: ROLE_ADMIN }
{% endhighlight %}
