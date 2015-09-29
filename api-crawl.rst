.. Documentation of Crawl API
   Added: September 7th, 2015
   Author: Bruno Skvorc <bruno@skvorc.me>

=========
Crawl API
=========

Diffbot has the ability to crawl entire domains and process all crawled pages. For a difference between crawling and processing `see here <http://support.diffbot.com/crawlbot/whats-the-difference-between-crawling-and-processing/>`__.

To programmatically create or update crawljobs, use this API.

A full tutorial on using this API can be found `here <http://www.sitepoint.com/crawling-searching-entire-domains-diffbot>`__, and a working app powered by it at http://search.sitepoint.tools.

Crawl API Class
===============

.. php:namespace:: Swader\Diffbot\Api

.. php:class:: Crawl

    The Crawl API is used to create new crawljobs or modify existing ones. The Crawl API is atypical, and as such does not extend :php:class:`Swader\\Diffbot\\Abstracts\\Api` unlike the more entity-specific APIs.

    Note that everything you can do with the Crawl API can also be done in the Diffbot UI.

:hidden:`__construct`
"""""""""""""""""""""

    .. php:method:: __construct($name = null, $api = null)

        :param string $name: [Optional] The name of crawljob to be created or modified.
        :param Swader\\Diffbot\\Interfaces\\Api $api: [Optional] The API to use while processing the crawled links.

        The ``$name`` argument is optional. If omitted, the second argument is ignored and the :php:meth:`Swader\\Diffbot\\Api\\Crawl::call` will return a list of all crawljobs on a given Diffbot token, with their information, in a :php:class:`Swader\\Diffbot\\Entity\\EntityIterator` collection of :php:class:`Swader\\Diffbot\\Entity\\JobCrawl` instances.

        The ``$api`` argument is also optional, but must be an instance of :php:class:`Swader\\Diffbot\\Interfaces\\Api` if provided::

            <?php

            // ... set up Diffbot

            $api = $diffbot->createArticleApi('crawl');
            $crawljob = $diffbot->crawl('myCrawlJob', $api);

            // ... crawljob setup
            // $crawljob->setSeeds( ... )

            $crawljob->call();

:hidden:`getName`
"""""""""""""""""

    .. php:method:: getName()

        :returns: string

        Returns the unique name of the crawljob. This name is later used to download datasets, or to modify the job.

:hidden:`setApi`
""""""""""""""""

    .. php:method:: setApi($api)

        :param Swader\\Diffbot\\Interfaces\\Api $api: An instance of :php:class:`Swader\\Diffbot\\Interfaces\\Api` to process all crawled links.
        :returns: $this

            The API cannot be modified after a crawljob has been created. This method is useless on existing crawljobs (see https://www.diffbot.com/dev/docs/crawl/api.jsp)

            The ``$api`` passed into this class will be used on Diffbot's end to process all the pages the crawljob provides. For example, if you set http://sitepoint.com as the seed URL (see :php:meth:`Swader\\Diffbot\\Api\\Crawl::setSeeds`), and an instance of the :php:class:`Swader\\Diffbot\\Api\\Article` API as the ``$api`` argument, all pages found on http://sitepoint.com will be processed with the Article API. The results won't be returned - rather, they'll be saved on Diffbot's servers for searching later (see :php:class:`Swader\\Diffbot\\Api\\Search`).

            The other APIs require a URL parameter in their constructor, but when crawling, it is crawlbot who is providing the URLs. To get around this requirement, use the string "crawl" instead of a URL when instantiating a new API for use with the Crawl API::

                // ...
                $api = $diffbot->createArticleApi('crawl');
                // ...

:hidden:`setSeeds`
""""""""""""""""""

    .. php:method:: setSeeds(array $seeds)

        :param array $seeds: An array of URLs (seeds) which to crawl for matching links
        :returns: $this

            By default Crawlbot will restrict spidering to the entire domain ("http://blog.diffbot.com" will include URLs at "http://www.diffbot.com")::

            // ...
            $crawljob->setSeeds(['http://sitepoint.com', 'http://blog.diffbot.com']);
            // ...

:hidden:`setUrlCrawlPatterns`
"""""""""""""""""""""""""""""

    .. php:method:: setUrlCrawlPatterns(array $pattern = null)

        :param array $pattern: [Optional] Array of strings to limit pages crawled to those whose URLs contain any of the content strings.
        :returns: $this

            You can use the exclamation point to specify a negative string, e.g. !product to exclude URLs containing the string "product," and the ^ and $ characters to limit matches to the beginning or end of the URL.

            The use of a urlCrawlPattern will allow Crawlbot to spider outside of the seed domain(s); it will follow all matching URLs regardless of domain::

            // ...
            $crawljob->setUrlCrawlPatterns(['!author', '!page']);
            // ...

:hidden:`setUrlCrawlRegex`
""""""""""""""""""""""""""

    .. php:method:: setUrlCrawlRegex($regex)

        :param string $regex: a regular expression string
        :returns: $this

            Specify a regular expression to limit pages crawled to those URLs that match your expression. This will override any urlCrawlPattern value.

            The use of a urlCrawlRegEx will allow Crawlbot to spider outside of the seed domain; it will follow all matching URLs regardless of domain.

:hidden:`setUrlProcessPatterns`
"""""""""""""""""""""""""""""""

    .. php:method:: setUrlProcessPatterns(array $pattern = null)

        :param array $pattern: [Optional] array of strings to search for in URLs
        :returns: $this

            Only URLs containing one or more of the strings specified will be processed by Diffbot. You can use the exclamation point to specify a negative string, e.g. !/category to exclude URLs containing the string "/category," and the ^ and $ characters to limit matches to the beginning or end of the URL.

:hidden:`setUrlProcessRegex`
""""""""""""""""""""""""""""

    .. php:method:: setUrlProcessRegex($regex)

        :param string $regex: Regular expression string
        :returns: $this

            Specify a regular expression to limit pages processed to those URLs that match your expression. This will override any urlProcessPattern value.

:hidden:`setPageProcessPatterns`
""""""""""""""""""""""""""""""""

    .. php:method:: setPageProcessPatterns(array $pattern = null)

        :param array $pattern: [Optional] Array of strings
        :returns: $this

            Specify strings to look for in the HTML of the pages of the crawled URLs. Only pages containing one or more of those strings will be processed by the designated API. Very useful for limiting processing to pages with a certain class present (e.g. ``class=article``) to further narrow down processing scope and reduce expenses (fewer API calls).

:hidden:`setMaxHops`
""""""""""""""""""""

    .. php:method:: setMaxHops($input = -1)

        :param int $input: [Optional] Maximum number of hops
        :returns: $this

            Specify the depth of your crawl. A maxHops=0 will limit processing to the seed URL(s) only -- no other links will be processed; maxHops=1 will  process all (otherwise matching) pages whose links appear on seed URL(s); maxHops=2 will process pages whose links appear on those pages; and so on. By default, Crawlbot will crawl and process links at any depth.

:hidden:`setMaxToCrawl`
"""""""""""""""""""""""

    .. php:method:: setMaxToCrawl($input = 100000)

        :param type $param: [Optional] Maximum number of URLs to spider
        :returns: $this

            Note that spidering (crawling) does not affect the API quota, and reducing this will only contribute to the length of a crawljob (it will be done faster if the limit is reached sooner). For a difference between crawling and processing `see here <http://support.diffbot.com/crawlbot/whats-the-difference-between-crawling-and-processing/>`__.

:hidden:`setMaxToProcess`
"""""""""""""""""""""""""

    .. php:method:: setMaxToProcess($input = 100000)

        :param type $param: [Optional] Maximum number of URLs to process
        :returns: $this

            Useful for limiting the number of API calls made, thus reducing / limiting expenses. For a difference between crawling and processing `see here <http://support.diffbot.com/crawlbot/whats-the-difference-between-crawling-and-processing/>`__.

:hidden:`notify`
""""""""""""""""

    .. php:method:: notify($string)

        :param string $string: Email or URL
        :returns: $this
        :throws: InvalidArgumentException

            If input is email address, end a message to this email address when the crawl hits the maxToCrawl or maxToProcess limit, or when the crawl completes.

            If input is URL, you will receive a POST with X-Crawl-Name and X-Crawl-Status in the headers, and the full JSON response in the POST body.

            This method can be called once with an email and another time with a URL in order to define both an email notification hook and a URL notification hook. An InvalidArgumentException will be thrown if the argument isn't a valid string (neither a URL nor an email address).

:hidden:`setCrawlDelay`
"""""""""""""""""""""""

    .. php:method:: setCrawlDelay($input = 0.25)

        :param float $input: delay between crawljob repeat executions, in floating point days
        :returns: $this





JobCrawl Class
==============

    The JobCrawl class is a container of information about a crawljob.

.. php:namespace:: Swader\Diffbot\Entity

.. php:class:: JobCrawl

