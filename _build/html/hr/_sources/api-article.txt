.. The Article API document describes everything there is to describe about processing Articles via Diffbot
   Added: September 8th, 2015
   Author: Bruno Skvorc <bruno@skvorc.me>

===========
Article API
===========

This API is used to turn content like blog posts, news articles, and other prose into JSON.

For examples of data that might be returned, please see http://diffbot.com and run the Article API demo.

The Article API part of the Diffbot PHP client consists of two main classes: the API class, and the Article Entity class. We'll describe them in order. Note that the API class extends :php:class:`Swader\\Diffbot\\Abstracts\\Api`, so be sure to read that first if you haven't already.

Article API Class
=================

.. php:namespace:: Swader\Diffbot\Api

.. php:class:: Article


:hidden:`__construct`
"""""""""""""""""""""

    .. php:method:: __construct($url)

        :param string $url: The URL of the page to process or the string "crawl"
        :returns: $this

    This class takes a single argument during construction, the URL of the page to process. Alternatively, the argument can be the string "crawl", if the API is to be used in conjunction with :php:class:`Swader\\Diffbot\\Api\\Crawl`.

:hidden:`setSentiment`
""""""""""""""""""""""

    .. php:method:: setSentiment($bool)

        :param bool $bool: Either ``true`` or ``false``
        :returns: $this

    This method sets the ``sentiment`` optional field value. This determines whether or not to return the sentiment score of the analyzed article text, a value ranging from -1.0 (very negative) to 1.0 (very positive). Sentiment analysis is powered by `Semantria <http://support.diffbot.com/automatic-apis/semantria-powered-sentiment-entity-extraction-and-other-text-analysis-features/>`__ for advanced features like keyword and entity extraction, but the basic sentiment analysis (score only) is enabled for everyone, even those without Semantria accounts.

        Usage::

            $url = 'http://www.sitepoint.com/diffbot-crawling-visual-machine-learning/';

            // ...

            $api->setSentiment(true);
            $result = $api->cal();

            // ...

            echo $result->sentiment; // -0.0979

:hidden:`setPaging`
"""""""""""""""""""

    .. php:method:: setPaging($bool = true)

        :param bool $bool: Either ``true`` or ``false``
        :returns: $this

    If set to false, Diffbot will not auto-concatenate several pages of a multi-page article into one. Defaults to true, max 20 pages.

    For more info about auto-concatenation, see `here <http://support.diffbot.com/automatic-apis/handling-multiple-page-articles/>`__.

    While practical, this is a less reliable method of concatenating long posts than finding out the number of pages manually and processing them each one by one. Not only does it often fail to recognize the next page links, but also if there's a chance that the series is longer than 20 parts, everything from 20 onward will remain ignored. This is a limitation of Diffbot, not the client, and there's little chance of it changing - concatenations longer than 20 pages would likely trigger timeouts as the page count becomes less and less trivial.

    If you need to process multiple pages of something, it is thus recommended you find out those links yourself, then pass them into Article API one by one and concatenate later. If you'd like to analyze the entire concatenated post after the fact, it's best to manually concat and then send the merged content into Diffbot as a `POST value <https://tldrify.com/bhr>`__ for processing.


        Usage::

            $url = 'http://www.some-seven-part-article.com/';

            // ...

            $api->setPaging(true);
            $result = $api->cal();

            // ...

            echo $result->numPages; // 7


:hidden:`setMaxTags`
""""""""""""""""""""

    .. php:method:: setMaxTags($max = 5)

        :param int $max: The number of tags to generate and return
        :returns: $this

    Set the maximum number of automatically-generated tags to return. By default a maximum of five tags will be returned. Tags are a built-in feature of Diffbot, and *could* generate different results on two different calls to the same URL provided enough time has passed, due to Diffbot's engine evolving over time as it processed more and more content.

    For an example of what the tags might look like, run the demo example at https://diffbot.com or see :php:meth:`Swader\\Diffbot\\Entity\\Article::getTags`.

:hidden:`setDiscussion`
"""""""""""""""""""""""

    .. php:method:: setDiscussion($bool = true)

        :param bool $param: Either ``true`` or ``false``
        :returns: $this

    Whether or not to use the Discussion API to additionally process any detected comment or review threads in the article. Behaves as if the :php:class:`Swader\\Diffbot\\Api\\Discussion` was set to process the page, and merges the returned data with the Article API's results by means of a ``discussion`` field in the result. The field will have all the sub-fields of the usual :php:class:`Swader\\Diffbot\\Api\\Discussion` call; i.e. you will be able to access the :php:class:`Swader\\Diffbot\\Entity\\Discussion` entity and all its sub entities via the :php:meth:`Swader\\Diffbot\\Entity\\Article::getDiscussion` method.

Article Entity Class
====================

When the Article API is done processing an article (or several) the result will be an Article Entity (i.e. a collection of *one* Article Entities inside an instance of :php:class:`Swader\\Diffbot\\Entity\\EntityIterator`.

For an overview of the abstract class all Entities build on, see :php:class:`Swader\\Diffbot\\Abstracts\\Entity`.

Note that the Article entity can also be returned by the :php:class:`Swader\\Diffbot\\Api\\Analyze` API in "article" mode, or in default mode when processing a URL that contains an article (auto-determined).


.. php:namespace:: Swader\Diffbot\Entity

.. php:class:: Article

:hidden:`__construct`
"""""""""""""""""""""

    .. php:method:: __construct(array $data)

        :param array $data: The data from which to build the Article entity

        The Article entity's constructor needs the data to populate its properties (see getters below). This class is automatically instantiated after an :php:class:`Swader\\Diffbot\\Api\\Article` or :php:class:`Swader\\Diffbot\\Api\\Analyze` call. You probably won't ever need to manually create an instance of this class.

        In the case of the Article entity, the constructor differs from the abstract one (:php:meth:`Swader\\Diffbot\\Abstracts\\Api::__construct`) in that it also looks for the `discussion` key in the result, in order to build a :php:class:`Swader\\Diffbot\\Entity\\Discussion` sub-entity (see :php:meth:`Swader\\Diffbot\\Entity\\Article::getDiscussion`).

:hidden:`getType`
"""""""""""""""""

    .. php:method:: getType()

        :returns: string

        Will always return "article" for articles::

            // ... API setup ... //
            $result = $api->call();

            echo $result->getType(); // "article"

:hidden:`getText`
"""""""""""""""""

    .. php:method:: getText()

        :returns: string | null

        Returns the plaintext content of the processed article. HTML tags are stripped completely, images are removed. If the text property is missing in the result, returns ``null``.

:hidden:`getHtml`
"""""""""""""""""

    .. php:method:: getHtml()

        :returns: string

        Returns the full HTML content of the article. If the HTML property is missing in the result, returns ``null``.

:hidden:`getDate`
"""""""""""""""""

    .. php:method:: getDate()

        :returns: string

        Returns date as per `RFC 2616 <http://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.3>`_. Example date: "Wed, 18 Dec 2013 00:00:00 GMT". Note that this is `strtotime <http://php.net/manual/en/function.strtotime.php>`__ friendly for further conversions.

:hidden:`getAuthor`
"""""""""""""""""""

    .. php:method:: getAuthor()

        :returns: string | null

        Returns the name of the author as written on the page. If Diffbot was unable to figure out who the author is, ``null`` is returned.

:hidden:`getTags`
"""""""""""""""""""""

    .. php:method:: getTags()

        :returns: array

        Returns an array of tags/entities, generated from analysis of the extracted text and cross-referenced with DBpedia and other data sources. Note that these are *not* the meta tags as defined by the author, but machine learned ones::

            // ... API setup ... //

            // URL: "http://www.sitepoint.com/diffbot-crawling-visual-machine-learning" //

            $result = $api->call();

            echo count($result->tags); // 5

            var_dump($result->tags);

            /** Output:
            array (size=5)
              0 =>
                array (size=4)
                  'count' => int 1
                  'score' => float 0.62
                  'label' => string 'Machine learning' (length=16)
                  'uri' => string 'http://dbpedia.org/resource/Machine_learning' (length=44)
              1 =>
                array (size=4)
                  'count' => int 4
                  'score' => float 0.61
                  'label' => string 'Web crawler' (length=11)
                  'uri' => string 'http://dbpedia.org/resource/Web_crawler' (length=39)
              2 =>
                array (size=4)
                  'count' => int 4
                  'score' => float 0.59
                  'label' => string 'Lexical analysis' (length=16)
                  'uri' => string 'http://dbpedia.org/resource/Lexical_analysis' (length=44)
              3 =>
                array (size=4)
                  'count' => int 7
                  'score' => float 0.54
                  'label' => string 'Uniform resource locator' (length=24)
                  'uri' => string 'http://dbpedia.org/resource/Uniform_resource_locator' (length=52)
              4 =>
                array (size=5)
                  'count' => int 2
                  'score' => float 0.52
                  'label' => string 'JavaScript' (length=10)
                  'rdfTypes' =>
                    array (size=3)
                      0 => string 'http://dbpedia.org/ontology/ProgrammingLanguage' (length=47)
                      1 => string 'http://dbpedia.org/ontology/Software' (length=36)
                      2 => string 'http://dbpedia.org/ontology/Work' (length=32)
                  'uri' => string 'http://dbpedia.org/resource/JavaScript' (length=38)
            **/

        Returns a maximum of 5 by default, though this can be changed in :php:meth:`Swader\\Diffbot\\Api\\Article::setMaxTags`.

:hidden:`getNumPages`
"""""""""""""""""""""

    .. php:method:: getNumPages()

        :returns: int

        Returns the number of pages if the article is a multi-page one. Read about auto-concatenation `here <http://support.diffbot.com/automatic-apis/handling-multiple-page-articles/>`__ and study the :php:meth:`Swader\\Diffbot\\Api\\Article::setPaging` method for more details.

:hidden:`getNextPages`
""""""""""""""""""""""

    .. php:method:: getNextPages()

        :returns: array

        If the article is a multi-page one, returns the list of URLs of the pages that follow after the one that was processed. If the article is a single-page one, an empty array is returned.

:hidden:`getSentiment`
""""""""""""""""""""""

    .. php:method:: getSentiment()

        :returns: float | null

        Returns the sentiment score of the analyzed article text, a value ranging from -1.0 (very negative) to 1.0 (very positive). If sentiment score is absent (due to Diffbot being unable to determine it, or due to :php:meth:`Swader\\Diffbot\\Api\\Article::setSentiment` being set to ``false``, returns ``null``.

:hidden:`getDiscussion`
"""""""""""""""""""""""

    .. php:method:: getDiscussion()

        :returns: :php:class:`Swader\\Diffbot\\Entity\\Discussion` | null

        Returns the :php:class:`Swader\\Diffbot\\Entity\\Discussion` found on the article's page (comments section). See :php:meth:`Swader\\Diffbot\\Api\\Article::setDiscussion` for details and below for usage::

            use Swader\Diffbot\Diffbot;

            $url = "www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/";

            $diffbot = new Diffbot("my_token");
            $api = $diffbot->createArticleApi($url);

            $result = $api->call();

            echo $result->getDiscussion()->getNumPosts(); // 7
            echo $result->getDiscussion()->getProvider(); // Disqus

        For other methods exposed on the :php:class:`Swader\\Diffbot\\Entity\\Discussion` entity, see its documentation.

:hidden:`getImages`
"""""""""""""""""""

    .. php:method:: getImages()

        :returns: array

        An array of images found in the article, with their details. The elements of the array are arrays like this one::

            /**

            array (size=7)
              'height' => int 512
              'diffbotUri' => string 'image|3|-851701004' (length=18)
              'naturalHeight' => int 727
              'width' => int 749
              'primary' => boolean true
              'naturalWidth' => int 1063
              'url' => string 'http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2014/07/140624455201.png' (length=79)

            **/

        Unlike the :php:class:`Swader\\Diffbot\\Api\\Discussion` API which returns details about discussion posts even when used with the :php:class:`Swader\\Diffbot\\Api\\Article` API, the image data returned with this method is minimal. For fuller details about images, use the :php:class:`Swader\\Diffbot\\Api\\Image` API.

:hidden:`getVideos`
"""""""""""""""""""

    .. php:method:: getVideos()

        :returns: array

        Essentially identical to the above ``getImages``, but for videos. Arrays in the resulting array will look like this::

            /**
            [
                "diffbotUri": "video|3|-1138675744",
                "primary": true,
                "url": "http://player.vimeo.com/video/22439234"
            ]
            **/

