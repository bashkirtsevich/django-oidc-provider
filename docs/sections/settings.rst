.. _settings:

Settings
########

Customize django-oidc-provider so that it fits your project's needs.

OIDC_LOGIN_URL
==============

OPTIONAL. ``str``. Used to log the user in. By default Django's ``LOGIN_URL`` will be used. `Read more in the Django docs <https://docs.djangoproject.com/en/1.11/ref/settings/#login-url>`_

``str``. Default is ``/accounts/login/`` (Django's ``LOGIN_URL``).

SITE_URL
========

OPTIONAL. ``str``. The OP server url.

If not specified, it will be automatically generated using ``request.scheme`` and ``request.get_host()``.

For example ``http://localhost:8000``.

OIDC_AFTER_USERLOGIN_HOOK
=========================

OPTIONAL. ``str``. A string with the location of your function. Provide a way to plug into the process after the user has logged in, typically to perform some business logic.

Default is::

    def default_hook_func(request, user, client):
        return None

Return ``None`` if you want to continue with the flow.

The typical situation will be checking some state of the user or maybe redirect him somewhere.
With ``request`` you have access to all OIDC parameters. Remember that if you redirect the user to another place then you need to take him back to the authorize endpoint (use ``request.get_full_path()`` as the value for a "next" parameter).

OIDC_AFTER_END_SESSION_HOOK
===========================

OPTIONAL. ``str``. A string with the location of your function. Provide a way to plug into the log out process just before calling Django's log out function, typically to perform some business logic.

Default is::

    def default_after_end_session_hook(request, id_token=None, post_logout_redirect_uri=None, state=None, client=None, next_page=None):
        return None

Return ``None`` if you want to continue with the flow.

OIDC_CODE_EXPIRE
================

OPTIONAL. ``int``. Code object expiration after been delivered.

Expressed in seconds. Default is ``60*10``.

OIDC_EXTRA_SCOPE_CLAIMS
=======================

OPTIONAL. ``str``. A string with the location of your class. Default is ``oidc_provider.lib.claims.ScopeClaims``.

Used to add extra scopes specific for your app. OpenID Connect RP's will use scope values to specify what access privileges are being requested for Access Tokens.

Read more about how to implement it in :ref:`scopesclaims` section.

OIDC_IDTOKEN_EXPIRE
===================

OPTIONAL. ``int``. ID Token expiration after been delivered.

Expressed in seconds. Default is ``60*10``.

OIDC_IDTOKEN_PROCESSING_HOOK
============================

OPTIONAL. ``str`` or ``(list, tuple)``.

A string with the location of your function hook or ``list`` or ``tuple`` with hook functions.
Here you can add extra dictionary values specific for your app into id_token.

The ``list`` or ``tuple`` is useful when you want to set multiple hooks, i.e. one for permissions and second for some special field.

The function receives a ``id_token`` dictionary and ``user`` instance
and returns it with additional fields.

Default is::

    def default_idtoken_processing_hook(id_token, user):

        return id_token

OIDC_IDTOKEN_SUB_GENERATOR
==========================

OPTIONAL. ``str``. A string with the location of your function. ``sub`` is a locally unique and never reassigned identifier within the Issuer for the End-User, which is intended to be consumed by the Client.

The function receives a ``user`` object and returns a unique ``string`` for the given user.

Default is::

    def default_sub_generator(user):

        return str(user.id)

OIDC_IDTOKEN_INCLUDE_CLAIMS
==============================

OPTIONAL. ``bool``. If enabled, id_token will include standard claims of the user (email, first name, etc.).

Default is ``False``.

OIDC_SESSION_MANAGEMENT_ENABLE
==============================

OPTIONAL. ``bool``. Enables OpenID Connect Session Management 1.0 in your provider. See the :ref:`sessionmanagement` section.

Default is ``False``.

OIDC_UNAUTHENTICATED_SESSION_MANAGEMENT_KEY
===========================================

OPTIONAL. Supply a fixed string to use as browser-state key for unauthenticated clients. See the :ref:`sessionmanagement` section.

Default is a string generated at startup.

OIDC_SKIP_CONSENT_EXPIRE
========================

OPTIONAL. ``int``. How soon User Consent expires after being granted.

Expressed in days. Default is ``30*3``.

OIDC_TOKEN_EXPIRE
=================

OPTIONAL. ``int``. Token object (access token) expiration after being created.

Expressed in seconds. Default is ``60*60``.

OIDC_USERINFO
=============

OPTIONAL. ``str``. A string with the location of your function. See the :ref:`scopesclaims` section.

The function receives a ``claims`` dictionary with all the standard claims and ``user`` instance. Must returns the ``claims`` dict again.

Example usage::

    def userinfo(claims, user):

        claims['name'] = '{0} {1}'.format(user.first_name, user.last_name)
        claims['given_name'] = user.first_name
        claims['family_name'] = user.last_name
        claims['email'] = user.email
        claims['address']['street_address'] = '...'

        return claims

.. note::
    Please **DO NOT** add extra keys or delete the existing ones in the ``claims`` dict. If you want to add extra claims to some scopes you can use the ``OIDC_EXTRA_SCOPE_CLAIMS`` setting.

OIDC_GRANT_TYPE_PASSWORD_ENABLE
===============================
OPTIONAL. A boolean whether to allow the Resource Owner Password
Credentials Grant. https://tools.ietf.org/html/rfc6749#section-4.3

.. important::
    From the specification:
    "Since this access token request utilizes the resource owner's
    password, the authorization server **MUST** protect the endpoint
    against brute force attacks (e.g., using rate-limitation or
    generating alerts)."

    There are many ways to implement brute force attack prevention. We cannot
    decide what works best for you, so you will have to implement a solution for
    this that suits your needs.

OIDC_TEMPLATES
==============
OPTIONAL. A dictionary pointing to templates for authorize and error pages.
Default is::

    {
        'authorize': 'oidc_provider/authorize.html',
        'error': 'oidc_provider/error.html'
    }

See the :ref:`templates` section.

The templates that are not specified here will use the default ones.
