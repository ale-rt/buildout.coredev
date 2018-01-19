PLIP 2092: Replace portal_skins-based login with a browserview-based one
========================================================================

PLIP ticket
    https://github.com/plone/Products.CMFPlone/issues/2092

Review by
    Alessandro Pisa (pisa@syslab.com)

Environment
    The PLIP was reviewed on Ubuntu 17.10 using python 2.7.14 and Google Chrome 62.0.3202.94 (64-bit).

Date
    November 27, 2017

Involved Packages and its pull requests
---------------------------------------

Plone Core

- https://github.com/plone/plone.login

Review steps
------------

- Check state of jenkins job on http://jenkins.plone.org/view/PLIPs/job/plip-2092-login/

- Set up the buildout using the PLIP's config::

  $ ./bin/buildout -c plips/plip-1483-retina-image-scales.cfg

- Ran single tests local for the PLIP's auto-checkout packages::

  $ bin/test -s plone.login
  $ bin/test -s plone.app.users
  $ bin/test -s Products.CMFPlone
  $ bin/test -s Products.PlonePAS
  $ bin/test -s plonetheme.barceloneta

- Review code

- Manual testing TTW


Notes and observations
----------------------

Automated testing
+++++++++++++++++

- tests of jenkins PLIP test job are failing (38 failures)
- tests of ``plone.login`` are passing.
- tests of ``plone.app.users`` are passing.
- tests of ``Products.CMFPlone`` are passing.
- tests of ``Products.PlonePAS`` are passing.
- tests of ``plonetheme.barceloneta`` are passing.

Manual testing
++++++++++++++

I created a fresh Plone instance and install the package ``plone.login``.
I saw the warning "This product cannot be uninstalled!".

As an anonymous user I tried to visit:

1. http://localhost:8080/Plone and clicked the Log In button
2. http://localhost:8080/Plone/login
3. http://localhost:8080/Plone/login_form
4. http://localhost:8080/Plone/@@site-controlpanel

I create a new Plone user "jsmith".
Authenticated as jsmith I tried to visit:

1. http://localhost:8080/Plone/login
2. http://localhost:8080/Plone/logout

All the tests behave properly, even if there are small differences.

For example, going to http://localhost:8080/Plone/login and entering the proper credentials redirects:

1. to the home page (with no visible notification for the user) when ``plone.login`` is installed
2. to the page "You are now logged in" when ``plone.login`` is installed

This is not necessarily bad.

Some URLs have no counterpart in the new implementation and will result in 404, e.g. http://localhost:8080/Plone/login_failed

The login page has a new link to a troubleshooting page: http://localhost:8080/Plone/@@login-help.
In the login help view the user can recover the password or the username.
It can also contact the site administrator.
Some tips previously present in the ``login_failed`` page are not available anymore (e.g. enable cookies).

The login help page is not really keyboard friendly.
If I have the focus on the "Email" field and I hit enter, the form is submitted with the "Reset password" action, which returns in a validation error.

The email styles are quite different for the two actions.
The login-help page is not very mobile friendly.

Code review
+++++++++++

Code style and readability are good, there are some flake8 errors and some nested ifs can be saved.

There are a lot of entry points to customize the login process.
It would be nice to have a smart way to customize the email templates.

The fact that the templates are not customizable with ``z3c.jbot`` is a point down IMHO.

Documentation
+++++++++++++

There is some good documentation, but it should be probably extended.

Conclusion
----------

The PLIP needs a green jenkins job and further manual testing.
I did not had the time to test external logins for example and various security settings combinations.
It would be nice to have it as an add on, at least for the moment.