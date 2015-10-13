.. Stub file
   Added: September 21st, 2015
   Author: Bruno Skvorc <bruno@skvorc.me>

======
Traits
======

All the traits used in the Diffbot PHP client are described in this one document.

.. php:namespace:: Swader\Diffbot\Traits

DiffbotAware
============

.. php:trait:: DiffbotAware

    The DiffbotAware trait is there to make the API classes spawned by Diffbot aware of their parent, so that common configuration values and other factories can be accessed even after an API class has been instantiated.

    Unless you're implementing your own API class which *doesn't* extend the :php:class:`\\Swader\\Diffbot\\Abstracts\\Api`, you won't need this.

:hidden:`registerDiffbot`
"""""""""""""""""""""""""

.. php:method:: registerDiffbot($d)

    :param \\Swader\\Diffbot\\Diffbot $d: :php:class:`Swader\\Diffbot\\Diffbot` - an instance of the Diffbot main class to inject into children, like instances of various API classes.
    :returns: $this

StandardApi
===========

.. php:trait:: StandardApi

    The StandardApi trait contains some methods common to most, if not all, API classes. These methods are setters for fields which appear in every Diffbot API: links, breadcrumb, meta and querystring. More information available under `optional fields <https://www.diffbot.com/dev/docs/article/>`__ in various API doc files.

:hidden:`setLinks`
""""""""""""""""""

.. :php:method:: setLinks($bool)

    :param bool $bool: Either ``true`` or ``false``
    :returns: $this

    Sets the ``links`` optional field to true, forcing the API to return all links found in the processed web page

:hidden:`setMeta`
"""""""""""""""""

.. :php:method:: setMeta($bool)

    :param bool $bool: Either ``true`` or ``false``
    :returns: $this

    Sets the ``meta`` optional field to true, forcing the API to return metadata (i.e. the meta tags in the HTML source)

:hidden:`setBreadcrumb`
"""""""""""""""""""""""

.. php:method:: setBreadcrumb($bool)

    :param bool $bool: Either ``true`` or ``false``
    :returns: $this

    Sets the ``breadcrumb`` optional field to true. The API then returns a top-level array (breadcrumb) of URLs and link text from page breadcrumbs.

:hidden:`setQuerystring`
""""""""""""""""""""""""

.. php:method:: setQuerystring($bool)

    :param bool $bool: Either ``true`` or ``false``
    :returns: $this

    Sets the ``querystring`` optional field to true. The API then returns any key/value pairs present in the URL querystring. Items without a discrete value will be returned as true.

StandardEntity
""""""""""""""

.. php:trait:: StandardEntity

    The StandardEntity trait is here to add some common methods to the various entities. These make sense only in the standard entities, i.e. the data formats returned by Diffbot, which is why they aren't in the abstract :php:class:`\\Swader\\Diffbot\\Abstracts\\Entity` class. You probably won't need this trait unless you define a :php:class:`\\Swader\\Diffbot\\Api\\Custom` API which offers fields of the same names as those returned by the getters in this trait.

:hidden:`getLang`
"""""""""""""""""

    .. php:method:: getLang()

        :returns: string

        Returns the language code of the detected language of the processed content. The code returned is a two-character ISO 639-1 code: http://en.wikipedia.org/wiki/List_of_ISO_639-1_codes

:hidden:`getHumanLanguage`
""""""""""""""""""""""""""

    .. php:method:: getHumanLanguage()

        :returns: string

        Alias method for ``getLang()`` above.

:hidden:`getPageUrl`
"""""""""""""""""""""

    .. php:method:: getPageUrl()

        :returns: string

        Returns the URL which was processed

:hidden:`getResolvedPageUrl`
""""""""""""""""""""""""""""

    .. php:method:: getResolvedPageUrl()

        :returns: string

        Returns page URL which was resolved by redirects, if any. Will often be identical to result from ``getPageUrl`` above.

:hidden:`getTitle`
""""""""""""""""""

    .. php:method:: getTitle()

        :returns: string

        Returns the title of the document which was processed.

:hidden:`getLinks`
""""""""""""""""""

    .. php:method:: getLinks()

        :returns: array | null

        Returns an array of all links found on the processed page. Links will be simple string elements in an indexed array. If the :php:meth:`Swader\\Diffbot\\Traits\\StandardApi::setLinks` method was not called, will return ``null``.


:hidden:`getMeta`
"""""""""""""""""

    .. php:method:: getMeta()

        :returns: array | null

        Returns an array containing the full contents of page meta tags, including sub-arrays for OpenGraph tags, Twitter Card metadata, schema.org microdata, and -- if available -- oEmbed metadata. If the :php:meth:`Swader\\Diffbot\\Traits\\StandardApi::setMeta` method was not called, will return ``null``.

:hidden:`getBreadcrumb`
"""""""""""""""""""""""

    .. php:method:: getBreadcrumb()

        :returns: array | null

        Returns a top-level array (breadcrumb) of URLs and link text from page breadcrumbs. If the :php:meth:`Swader\\Diffbot\\Traits\\StandardApi::setBreadcrumb` method was not called, will return ``null``.


:hidden:`getQueryString`
""""""""""""""""""""""""

    .. php:method:: getQueryString()

        :returns: array | null

        Returns any key/value pairs present in the URL querystring. Items without a discrete value will be returned as true. If the :php:meth:`Swader\\Diffbot\\Traits\\StandardApi::setQuerystring` method was not called, will return ``null``.

:hidden:`getDiffbotUri`
"""""""""""""""""""""""

    .. php:method:: getDiffbotUri()

        :returns: string

        A unique identifier of the entity in Diffbot's database. Useful for filtering out duplicates, caching, etc.

