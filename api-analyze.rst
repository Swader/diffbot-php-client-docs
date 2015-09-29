.. Documentation of Analyze API
   Added: September 25th, 2015
   Author: Bruno Skvorc <bruno@skvorc.me>

===========
Analyze API
===========

This API is a sort of "catch all" for all other API types in that it automatically determines the type of content being processed, and applies the appropriate API call to it.

This API will return entities matching the determined content type. For example, if you run Analyze API on a URL like ``www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/``, the content type will be determined as "article" and it'll be exactly as if you had called the Article API (:php:class:`Swader\\Diffbot\\Api\\Article`) on it.

Analyze API Class
=================

.. php:namespace:: Swader\Diffbot\Api

.. php:class:: Analyze

:hidden:`setDiscussion`
"""""""""""""""""""""""

    .. php:method:: setDiscussion($bool = true)

        :param bool $bool: Either ``true`` or ``false``
        :returns: $this

        If set to false, will not extract article comments in a Discussion entity embedded in the Article / Product entity. By default, it will.

:hidden:`setMode`
"""""""""""""""""

    .. php:method:: setMode($mode)

        :param string $mode: "article", "product", "image" or "auto"
        :returns: $this

        By default the Analyze API will fully extract all pages that match an existing Automatic API -- articles, products or image pages. Set mode to a specific page-type (e.g., mode=article) to extract content only from that specific page-type. All other pages will simply return the default Analyze fields.

Usage with defaults::

    use Swader\Diffbot\Diffbot;

    $url = "www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/";

    $diffbot = new Diffbot("my_token");
    $api = $diffbot->createAnalyzeApi($url);

    $result = $api->call();

    echo $result->getAuthorUrl(); // "http://www.sitepoint.com/author/bskvorc/"

    echo $result->getDiscussion()->getNumPosts(); // 7
    echo $result->getDiscussion()->getProvider(); // Disqus

Usage with discussion off::

    use Swader\Diffbot\Diffbot;

    $url = "www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/";

    $diffbot = new Diffbot("my_token");
    $api = $diffbot->createAnalyzeApi($url);

    $api->setDiscussion(false);
    $result = $api->call();

    echo $result->getAuthorUrl(); // "http://www.sitepoint.com/author/bskvorc/"

    var_dump($result->getDiscussion()); // null

Usage with non-matching mode::

    use Swader\Diffbot\Diffbot;

    $url = "www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/";

    $diffbot = new Diffbot("my_token");
    $api = $diffbot->createAnalyzeApi($url);

    $api->setMode("image");
    $result = $api->call();

    echo $result->getAuthorUrl(); // null
    var_dump($result->getDiscussion()); // null


In the last example above, no data is available due to a mismatch in mode - using image parsing on an article entity does not produce any useful information.