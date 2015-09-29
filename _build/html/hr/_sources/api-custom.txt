.. Stub file
   Added: September 7th, 2015
   Author: Bruno Skvorc <bruno@skvorc.me>

==========
Custom API
==========

The Custom API is user defined in the Diffbot UI.

For a tutorial on creating a Custom API in the Diffbot UI, see `here <http://www.sitepoint.com/analyze-sitepoint-author-portfolios-diffbot/>`__.

Custom API Class
================

.. php:namespace:: Swader\Diffbot\Api

.. php:class:: Custom

    When you have a Custom API ready on Diffbot's end, you instantiate the Custom API class and pass in the Custom API name, along with the URL to process. Everything from that point on is identical to the other APIs, except the fact that instead of specific entities being returned, all Custom API calls return an iterator of :php:class:`Swader\\Diffbot\\Entity\\Wildcard` entities.

:hidden:`__construct`
"""""""""""""""""""""

    .. php:method:: __construct($url, $name)

        :param string $url: The URL to process
        :param string $name: The name of the API

        The construct method is identical to the one in :php:class:`Swader\\Diffbot\\Abstracts\\Api` with one difference - it also needs the name of the Custom API in question, so that it can build the API URL to which the call will be dispatched when :php:meth:`Swader\\Diffbot\\Abstracts\\Api::call` is called::

            <?php

            require_once '../vendor/autoload.php';

            use Swader\Diffbot\Diffbot;

            $diffbot = new Diffbot($my_token);

            $url = 'http://sitepoint.com/author/bskvorc';
            $api = $diffbot->createCustomApi($url, "AuthorFolio");

            $result = $api->call();

            echo $result->getBio(); // "Bruno is a coder from Croatia..."

        In the example above, *AuthorFolio* is a custom API from `this tutorial <http://www.sitepoint.com/analyze-sitepoint-author-portfolios-diffbot/>`__, which processes a SitePoint author's portfolio. The ``getBio`` call works because of the magic methods in :php:class:`Swader\\Diffbot\\Abstracts\\Entity` which :php:class:`Swader\\Diffbot\\Entity\\Wildcard` inherits.

Wildcard Entity Class
=====================

.. php:namespace:: Swader\Diffbot\Entity

.. php:class:: Wildcard

    The Wildcard entity is returned when the type of a processed post does not match a type defined in the currently set EntityFactory (see :php:class:`Swader\\Diffbot\\Factory\\Entity` and :php:meth:`Swader\\Diffbot\\Diffbot::setEntityFactory`).

    It is nothing more than a *concretization* of :php:class:`Swader\\Diffbot\\Abstracts\\Entity` and as such contains no additional methods.

    In the example above, the ``getBio`` method is called on a Wilcard instance, returned by the call to the *AuthorFolio*. custom API.