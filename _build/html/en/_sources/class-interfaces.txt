.. Stub file
   Added: September 21st, 2015
   Author: Bruno Skvorc <bruno@skvorc.me>

==========
Interfaces
==========

This document contains the descriptions for all interfaces used in the client.

.. php:namespace:: Swader\Diffbot\Interfaces

Api
===

.. php:interface:: Api

    The API interface is there as a contract for developing custom APIs, not unlike the :php:class:`Swader\\Diffbot\\Api\\Custom` class.

:hidden:`setTimeout`
""""""""""""""""""""

    .. php:method:: setTimeout($timeout = 30000)

        :param int $timeout: The timeout value in milliseconds. Defaults to 30000 (30 seconds)
        :returns: $this

    All Diffbot API endpoints support a timeout parameter which tells them after how many milliseconds to stop expecting a response from the page being processed.

:hidden:`call`
""""""""""""""

    .. php:method:: call()

        :returns: :php:class:`Swader\\Diffbot\\Entity\\EntityIterator`

    The call method should execute the remote call to the API. It must return an instance of :php:class:`Swader\\Diffbot\\Entity\\EntityIterator` containing the set of appropriate entities for the return value of said API. In custom APIs, these are usually :php:class:`Swader\\Diffbot\\Entity\\Wildcard` entities, unless otherwise specified via a custom implementation of :php:interface:`Swader\\Diffbot\\Interfaces\\EntityFactory`.

:hidden:`buildUrl`
""""""""""""""""""

    .. php:method:: buildUrl()

        :returns: string

    This method is called automatically when :php:meth:`Swader\\Diffbot\\Interfaces\\Api::call` is called. It builds the URL which is to be called by the HTTPClient in :php:meth:`Swader\\Diffbot\\Diffbot::setHttpClient`, and returns it. This method can be used to get the URL for the purposes of testing in third party API clients like Postman.

EntityFactory
=============

.. php:interface:: EntityFactory

    The EntityFactory interface is there as a contract for developing custom Entity Factories. For example, you may want to make sure that a call to an API returns specific entities rather than :php:class:`Swader\\Diffbot\\Entity\\Wildcard`, or some of the predefined ones like :php:class:`Swader\\Diffbot\\Entity\\Product`. A specific example would be having a custom API which processes a site with board game cards. Each card has a specific value at a specific location, and these values may correspond. Rather than manually process data in :php:class:`Swader\\Diffbot\\Entity\\Wildcard` entities after a call to this custom API, you might want to define a ``GameCard`` entity and give it fields and methods specific to the context. A custom entity factory is then used to bind the newly defined entity with the custom API.

.. @todo: tutorial about EntityFactory

:hidden:`createAppropriateIterator`
"""""""""""""""""""""""""""""""""""

    .. php:method:: createAppropriateIterator($response)

        :param GuzzleHttp\\Message\\ResponseInterface $response: The response received from the API call. Must be of the GuzzleHttp v5 type. Automatic if the Guzzle client is used, but version 5 only.
        :returns: :php:class:`Swader\\Diffbot\\Entity\\EntityIterator`

    Returns the entity iterator containing the appropriate entities as built by the contents of ``$response``.

