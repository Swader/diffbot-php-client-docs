.. Search API documentation
   Added: October 8th, 2015
   Author: Bruno Skvorc <bruno@skvorc.me>

==========
Search API
==========

Diffbot's Search API allows you to search the extracted content of one or all of your Diffbot "collections." A collection is a discrete Crawlbot (:php:class:`Swader\\Diffbot\\Api\\Crawl`) or Bulk API job, and includes all of the web pages processed within that job.

In order to search a collection, you must first create that collection using either Crawlbot or the Bulk API. A collection can be searched before a crawl or bulk job is finished.

Whereas Crawlbot returns information about a specific crawljob, the Search API returns sets of matching documents from Diffbot's database, depending on provided query parameters.

The API consists of two parts: the API class used to make the call and return the results, and the SearchInfo class as an alternative result, providing metadata about the query and the complete resultset. We'll describe both, in order.

Note that the API class extends :php:class:`Swader\\Diffbot\\Abstracts\\Api`, so be sure to read that first if you haven't already.

Search API Class
================

.. php:namespace:: Swader\Diffbot\Api

.. php:class:: Search

This API class is a bit specific in that it only extends :php:class:`Swader\\Diffbot\\Abstracts\\Api` to inherit part of a single function - almost everything else is custom implemented, due to the highly specific nature of the API.

Basic usage::

    use Swader\Diffbot\Diffbot;

    $diffbot = new Diffbot('my_token');
    $search = $diffbot->search('author:"Miles Johnson" AND type:article');
    $result = $search->call();

    foreach ($result as $article) {
        echo $article->getTitle();
    }

    $info = $search->call(true);

    echo $info->getHits(); // 50

:hidden:`__construct`
"""""""""""""""""""""

    .. php:method:: __construct()

        :param string $q: Query string to run on the collection(s)

        The constructor takes a string like "foo AND bar AND title:baz". This would make the API search for documents containing both "foo" and "bar" in any of the fields, and "baz" in the title field.

:hidden:`setCol`
""""""""""""""""

    .. php:method:: setCol($col = null)

        :param string $col: [Optional] Name of collection to search
        :returns: $this

        If collection name is not provided, Search API will search all the collections under the currently active token.

:hidden:`setNum`
""""""""""""""""

    .. php:method:: setNum($num = 20)

        :param string|int $num: Number of results to return
        :returns: $this

        The ``$num`` param should either be a number, or the string "all" if you want the API to return all the results. Note that this may be quite a large payload if the search terms are broad, and you'd likely be better off paginating the result (see below).

:hidden:`setStart`
""""""""""""""""""

    .. php:method:: setStart($start = 0)

        :param int $start: The starting result number. Used during pagination.
        :returns: $this


:hidden:`buildUrl`
""""""""""""""""""

    .. php:method:: buildUrl()

        :returns: string

        This method is called automatically when :php:meth:`Swader\\Diffbot\\Abstracts\\Api::call` is called. It builds the URL which is to be called by the HTTPClient in :php:meth:`Swader\\Diffbot\\Diffbot::setHttpClient`, and returns it. This method can be used to get the URL for the purposes of testing in third party API clients like `Postman <https://www.getpostman.com/>`_.

    Usage::

        $api-> // ... set up API
        $myUrl = $api->buildUrl();

:hidden:`call`
""""""""""""""

    .. php:method:: call($info = false)

        :param bool $info: Either ``true`` or ``false``
        :returns: :php:class:`Swader\\Diffbot\\Entity\\SearchInfo` | :php:class:`Swader\\Diffbot\\Entity\\EntityIterator`

        When the API instance has been fully configured, this method executes the call.

        If the ``$info`` parameter passed into the method is ``false``, the return value will be an iterable collection (:php:class:`Swader\\Diffbot\\Entity\\EntityIterator`) of appropriate entities. Refer to each API's documentation for details on entities returned from each API call.

        If you pass in ``true``, you force info mode and get back a :php:class:`Swader\\Diffbot\\Entity\\SearchInfo` object related to the last call. Keep in mind that passing in ``true`` before calling a default ``call()`` will implicitly call the ``call()``, and then get the SearchInfo.

        So::

            $searchApi->call(); // gets entities
            $searchApi->call(true); // gets SearchInfo about the executed query



SearchInfo Entity Class
=======================

When the Search API is called with *info* mode forced, the API will return an info object, containing various properties useful for pagination and metadata.

.. php:namespace:: Swader\Diffbot\Entity

.. php:class:: SearchInfo

:hidden:`getType`
"""""""""""""""""

    .. php:method:: getType()

        :returns: string

        Will always return "searchInfo"::

            // ... API setup ... //
            $result = $api->call(true);

            echo $result->getType(); // "searchInfo"


:hidden:`getCurrentTimeUTC`
"""""""""""""""""""""""""""

    .. php:method:: getCurrentTimeUTC()

        :returns: int

        Current UTC time as timestamp

:hidden:`getResponseTimeMS`
"""""""""""""""""""""""""""

    .. php:method:: getResponseTimeMS()

        :returns: int

        Response time in milliseconds. Time it took to process the query on Diffbot's end.

:hidden:`getNumResultsOmitted`
""""""""""""""""""""""""""""""

    .. php:method:: getNumResultsOmitted()

        :returns: int

        Number of results skipped for any reason

:hidden:`getNumShardsSkipped`
"""""""""""""""""""""""""""""

    .. php:method:: getNumShardsSkipped()

        :returns: int

        Number of skipped shards (@todo find out what those are)

:hidden:`getTotalShards`
""""""""""""""""""""""""

    .. php:method:: getTotalShards()

        :returns: int

        Total number of shards (@todo find out what those are)

:hidden:`getDocsIncollection`
"""""""""""""""""""""""""""""

    .. php:method:: getDocsInCollection()

        :returns: int

        Total number of documents in collection. Should resemble the total number you got on the crawl job. (@todo: find out why not identical)

:hidden:`getHits`
"""""""""""""""""

    .. php:method:: getHits()

        :returns: int

        Number of results that match - NOT the number of *returned* results! Use this for pagination as a total result count.

:hidden:`getQueryInfo`
""""""""""""""""""""""

    .. php:method:: getQueryInfo()

        :returns: array

        Returns an assoc. array containing the following keys and example values::

            /**

            "fullQuery" => "type:json AND (author:\"Miles Johnson\" AND type:article)",
            "queryLanguageAbbr" => "xx",
            "queryLanguage" => "Unknown",
            "terms" => [
                [
                "termNum" => 0,
                "termStr" => "Miles Johnson",
                "termFreq" => 2621376,
                "termHash48" => 224575481707228,
                "termHash64" => 4150001371756911641,
                "prefixHash64" => 3732660069076179349
                ],
                [
                "termNum" => 1,
                "termStr" => "type:json",
                "termFreq" => 2621664,
                "termHash48" => 272064464231140,
                "termHash64" => 9877301297136722857,
                "prefixHash64" => 7586288672657224048
                ],
                [
                "termNum" => 2,
                "termStr" => "type:article",
                "termFreq" => 524448,
                "termHash48" => 210861560163398,
                "termHash64" => 12449358332005671483,
                "prefixHash64" => 7586288672657224048
                ]
            ]

            **/

        @todo: find out what hashes are, and to what the freq is relative
