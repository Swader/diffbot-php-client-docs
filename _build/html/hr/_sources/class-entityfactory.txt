.. Entity Factory description
   Added: October 13th, 2015
   Author: Bruno Skvorc <bruno@skvorc.me>

=============
EntityFactory
=============

The EntityFactory builds the :php:class:`Swader\\Diffbot\\Entity\\EntityIterator` by providing it with a collection of entities returned by an API, and a Guzzle Response which to consume. It implements the :php:class:`Swader\\Diffbot\\Interfaces\\EntityFactory` interface.

The only reason to build your own version of the EntityFactory is to provide it with instructions on how to pair API return types and entities you developed by extending :php:class:`Swader\\Diffbot\\Abstracts\\Entity`.

For a concrete example of this, see `this tutorial <http://sitepoint.com/php>`__ on SitePoint, which demonstrates custom "AuthorFolio" and "SitePointArticle" entities automatically created by calls to a custom API.

.. php:namespace:: Swader\Diffbot\Factory

.. php:class:: Entity

:hidden:`createAppropriateIterator`
"""""""""""""""""""""""""""""""""""

    .. php:method:: createAppropriateIterator($response)

        :param GuzzleHttp\\Message\\ResponseInterface $response: The Guzzle response given by the Guzzle client after an API call's execution
        :returns: :php:class:`Swader\\Diffbot\\Entity\\EntityIterator`

        The only method publicly accessible, and the only method one needs to implement when building one's own EntityFactory, ``createAppropriateIterator`` does what it says - it takes the Guzzle response provided to it, and builds an EntityIterator - a collection of Entities fitting for an API's result.

