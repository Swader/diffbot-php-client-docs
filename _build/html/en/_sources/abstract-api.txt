.. API Abstract class
   Added: September 8th, 2015
   Author: Bruno Skvorc <bruno@skvorc.me>

============
API Abstract
============

This page will describe the API Abstract class - the one which all the API classes extend to get some common functionality. Use this to build your own API class for custom APIs you defined in the Diffbot UI.

.. php:namespace:: Swader\Diffbot\Abstracts

.. php:class:: Api

:hidden:`__construct`
"""""""""""""""""""""""

    .. php:method:: __construct($url)

        :param string $url: The URL of the page to process
        :throws: InvalidArgumentException if the URL is invalid AND not the word "crawl"

        This class takes a single argument during construction, the URL of the page to process. Alternatively, the argument can be "crawl", if the API is to be used in conjunction with the :php:class:`Swader\\Diffbot\\Api\\Crawl` API.

:hidden:`setTimeout`
""""""""""""""""""""

    .. php:method:: setTimeout($timeout = 30000)

        :param int $timeout: Optional. The timeout, in milliseconds. Defaults to 30,000, a.k.a. 30 seconds
        :returns: $this
        :throws: InvalidArgumentException if the timeout value is invalid (negative or not an integer)

        Setting the timeout will define how long Diffbot will keep trying to fetch the API results. A timeout can happen for various reasons, from Diffbot's failure, to the site being crawled being exceptionally slow, and more.

        Usage::

            $api->setTimeout(40000);

:hidden:`call`
""""""""""""""

    .. php:method:: call()

        :returns: :php:class:`Swader\\Diffbot\\Entity\\EntityIterator` The return value will be an iterable collection of appropriate entities. Refer to each API's documentation for details on entities returned from each API call.

        When the API instance has been fully configured, this method executes the call.

        Usage::

            $result = $api->call();
            foreach ($result as $entity) { /* ... */ }


:hidden:`buildUrl`
""""""""""""""""""

    .. php:method:: buildUrl()

        :returns: string

        This method is called automatically when :php:meth:`Swader\\Diffbot\\Abstracts\\Api::call` is called. It builds the URL which is to be called by the HTTPClient in :php:meth:`Swader\\Diffbot\\Diffbot::setHttpClient`, and returns it. This method can be used to get the URL for the purposes of testing in third party API clients like `Postman <https://www.getpostman.com/>`_.

    Usage::

        $api-> // ... set up API
        $myUrl = $api->buildUrl();

