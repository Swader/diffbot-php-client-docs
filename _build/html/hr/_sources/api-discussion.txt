.. Discussion API documentation
   Added: October 7th, 2015
   Author: Bruno Skvorc <bruno@skvorc.me>

==============
Discussion API
==============

This API is used to turn content like product reviews, comments on posts and forum threads into JSON. This API can be unleashed onto a forum / comment thread directly, or onto a product page / article page containing comments / reviews.

The Discussion API part of the Diffbot PHP client consists of three main classes: the API class, the Discussion Entity class, and the Post Entity class. We'll describe them in order. Note that the API class extends :php:class:`Swader\\Diffbot\\Abstracts\\Api`, so be sure to read that first if you haven't already.

Discussion API Class
====================

.. php:namespace:: Swader\Diffbot\Api

.. php:class:: Discussion

Basic Usage::

    use Swader\Diffbot\Diffbot;

    $url = 'http://some-article-to-process.com';

    $diffbot = new Diffbot('my_token');
    $api = $diffbot->createDiscussionApi($url);

:hidden:`setMaxPages`
"""""""""""""""""""""

    .. php:method:: setMaxPages($max = 1)

        :param int $max: max number of pages to fetch
        :returns: $this

        Set the maximum number of pages in a thread to automatically concatenate in a single response. Default = 1 (no concatenation). Set maxPages=all to retrieve all pages of a thread regardless of length. Each individual page will count as a separate API call.

:hidden:`setSentiment`
""""""""""""""""""""""

    .. php:method:: setSentiment($bool)

        :param bool $bool: Either ``true`` or ``false``
        :returns: $this

            This method sets the ``sentiment`` optional field value. This determines whether or not to return the sentiment score of the analyzed posts (each individual post gets one), a value ranging from -1.0 (very negative) to 1.0 (very positive). Sentiment analysis is powered by `Semantria <http://support.diffbot.com/automatic-apis/semantria-powered-sentiment-entity-extraction-and-other-text-analysis-features/>`__ for advanced features like keyword and entity extraction, but the basic sentiment analysis (score only) is enabled for everyone, even those without Semantria accounts.

        Usage::

            $url = 'https://www.reddit.com/r/PHP/comments/3nl7g1/authentication_flow_in_a_microservice_architecture/';

            // ...

            $api->setSentiment(true);
            $result = $api->cal();

            // ...

            echo $result->getPosts()[0]->getSentiment(); // -0.0789



Discussion Entity Class
=======================

When the Discussion API is done processing a URL, the result will be a Discussion Entity (i.e. a collection of *one* Discussion Entities inside an instance of :php:class:`Swader\\Diffbot\\Entity\\EntityIterator`).

For an overview of the abstract class all Entities build on, see :php:class:`Swader\\Diffbot\\Abstracts\\Entity`.


.. php:namespace:: Swader\Diffbot\Entity

.. php:class:: Discussion

:hidden:`__construct`
"""""""""""""""""""""

    .. php:method:: __construct(array $data)

        :param array $data: The data from which to build the Discussion object

        The Article entity's constructor needs the data to populate its properties (see getters below). This class is automatically instantiated after a :php:class:`Swader\\Diffbot\\Api\\Discussion` call. You probably won't ever need to manually create an instance of this class.

        Like :php:class:`Swader\\Diffbot\\Entity\\Product` and :php:class:`Swader\\Diffbot\\Entity\\Article`, the Discussion entity also has its own custom constructor, looking for the ``posts`` key inside of the return data, in order to create some nested :php:class:`Swader\\Diffbot\\Entity\\Post` objects.

:hidden:`getType`
"""""""""""""""""

    .. php:method:: getType()

        :returns: string

        Will always return "discussion" for discussions::

            // ... API setup ... //
            $result = $api->call();

            echo $result->getType(); // "discussion"

:hidden:`getNumPosts`
"""""""""""""""""""""

    .. php:method:: getNumPosts()

        :returns: int

        Returns the number of posts found in the discussion. Only returns the number of posts in the fetched page range, so even if there are 100 posts over 20 pages, this method will return 5 if :php:meth:`Swader\\Diffbot\\Api\\Discussion::setMaxPages` is still set to 1.

:hidden:`getTags`
"""""""""""""""""

    .. php:method:: getTags()

        :returns: array

        Returns an array of tags/entities, generated from analysis of the extracted text and cross-referenced with DBpedia and other data sources. Note that these are *not* the meta tags as defined in the page's ``<head>``, but machine learned ones::

            // ... API setup ... //

            $url = 'https://www.reddit.com/r/PHP/comments/3nl7g1/authentication_flow_in_a_microservice_architecture/';

            // ...

            $result = $api->call();

            echo count($result->tags); // 5

            var_dump($result->getTags);

            /**

            Output:
                array (size=5)
                  0 =>
                    array (size=5)
                      'count' => int 5
                      'prevalence' => float 0.11
                      'score' => float 0.11
                      'label' => string 'User (computing)' (length=16)
                      'uri' => string 'http://dbpedia.org/resource/User_(computing)' (length=44)
                  1 =>
                    array (size=5)
                      'count' => int 4
                      'prevalence' => float 0.09
                      'score' => float 0.09
                      'label' => string 'Hypertext Transfer Protocol' (length=27)
                      'uri' => string 'http://dbpedia.org/resource/Hypertext_Transfer_Protocol' (length=55)
                  2 =>
                    array (size=5)
                      'count' => int 3
                      'prevalence' => float 0.07
                      'score' => float 0.07
                      'label' => string 'POST (HTTP)' (length=11)
                      'uri' => string 'http://dbpedia.org/resource/POST_(HTTP)' (length=39)
                  3 =>
                    array (size=5)
                      'count' => int 2
                      'prevalence' => float 0.04
                      'score' => float 0.04
                      'label' => string 'Object (computer science)' (length=25)
                      'uri' => string 'http://dbpedia.org/resource/Object_(computer_science)' (length=53)
                  4 =>
                    array (size=5)
                      'count' => int 2
                      'prevalence' => float 0.04
                      'score' => float 0.04
                      'label' => string 'Coupling' (length=8)
                      'uri' => string 'http://dbpedia.org/resource/Coupling' (length=36)
            **/

        Returns a maximum of 5.

:hidden:`getParticipants`
"""""""""""""""""""""""""

    .. php:method:: getParticipants()

        :returns: int

        The number of unique participants in the discussion.

:hidden:`getNumPages`
"""""""""""""""""""""

    .. php:method:: getNumPages()

        :returns: int

        Returns the number of pages if the discussion is a multi-page one. Read about auto-concatenation `here <http://support.diffbot.com/automatic-apis/handling-multiple-page-articles/>`__ and study the :php:meth:`Swader\\Diffbot\\Api\\Discussion::setMaxPages` method for more details.

:hidden:`getNextPages`
""""""""""""""""""""""

    .. php:method:: getNextPages()

        :returns: array

        If the discussion is a multi-page one, returns the list of absolute URLs of the pages that follow after the one that was processed. If the discussion is a single-page one, an empty array is returned.

:hidden:`getNextPage`
"""""""""""""""""""""

    .. php:method:: getNextPage()

        :returns: string | null

        If the discussion is a multi-page one, returns the absolute subsequent page URL.

:hidden:`getProvider`
"""""""""""""""""""""

    .. php:method:: getProvider()

        :returns: string | null

        Returns the provider of the comment / review system. This will be something like "disqus", "facebook", etc. In cases of forums and similar all-encompassing systems like Reddit, this method will return null.

:hidden:`getRssUrl`
"""""""""""""""""""

    .. php:method:: getRssUrl()

        :returns: string | null

        Returns the RSS feed URL for the discussion, if available.

:hidden:`getConfidence`
"""""""""""""""""""""""

    .. php:method:: getConfidence()

        :returns: float | null

        A number from -1 to 1. Not sure what it does. Waiting for feedback from HQ. @todo find out what this is.

:hidden:`getPosts`
""""""""""""""""""

    .. php:method:: getPosts()

        :returns: array

        Returns an array of :php:class:`Swader\\Diffbot\\Entity\\Post` objects, each built around the data in every individual post of a discussion. For post accessor methods, see below.

Discussion Post Class
=====================

.. php:class:: Post

Every Discussion entity has children - its posts. Every Post is its own entity, and very similar to :php:class:`Swader\\Diffbot\\Entity\\Article`, sharing many of its methods.

:hidden:`getType`
"""""""""""""""""

    .. php:method:: getType()

        :returns: string

        Will always return "post" for posts.

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

:hidden:`getText`
"""""""""""""""""

    .. php:method:: getText()

        :returns: string | null

        Returns the plaintext content of the processed post. HTML tags are stripped completely, images are removed. If the text property is missing in the result, returns ``null``.

:hidden:`getHtml`
"""""""""""""""""

    .. php:method:: getHtml()

        :returns: string

        Returns the full HTML content of the post. If the HTML property is missing in the result, returns ``null``.

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

:hidden:`getAuthorUrl`
""""""""""""""""""""""

    .. php:method:: getAuthorUrl()

        :returns: string | null

        If the author's profile URL could be determined, this method will return it.


:hidden:`getTags`
"""""""""""""""""""""

    .. php:method:: getTags()

        :returns: array

        Returns an array of tags/entities, generated from analysis of the extracted text and cross-referenced with DBpedia and other data sources. Note that these are *not* the meta tags as defined by the author, but machine learned ones. Same thing as :php:meth:`Swader\\Diffbot\\Entity\\Article::getTags` and :php:meth:`Swader\\Diffbot\\Entity\\Discussion::getTags`.

:hidden:`getSentiment`
""""""""""""""""""""""

    .. php:method:: getSentiment()

        :returns: float | null

        Returns the sentiment score of the analyzed post text, a value ranging from -1.0 (very negative) to 1.0 (very positive). If sentiment score is absent (due to Diffbot being unable to determine it, or due to :php:meth:`Swader\\Diffbot\\Api\\Discussion::setSentiment` being set to ``false``, returns ``null``.

:hidden:`getVotes`
""""""""""""""""""

    .. php:method:: getVotes()

        :returns: int

        If a voting system exists and is easily discernible, Diffbot returns the number of upvotes on the post.

:hidden:`getId`
"""""""""""""""

    .. php:method:: getId()

        :returns: int

        Returns the ID of the post (usually the ordinary number of the post in the list of all posts, starting with 0 for the first one.

:hidden:`getParentId`
"""""""""""""""""""""

    .. php:method:: getParentId()

        :returns: int | null

        If the post is a reply, this is the ID of the post it replies to. If not, null.

:hidden:`getImages`
"""""""""""""""""""

    .. php:method:: getImages()

        :returns: array

        An array of images found in the post, with their details. The elements of the array are arrays like this one::

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

        The image data returned with this method is minimal. For fuller details about images, use the :php:class:`Swader\\Diffbot\\Api\\Image` API.


:hidden:`getPageUrl`
"""""""""""""""""""""

    .. php:method:: getPageUrl()

        :returns: string

        Returns the URL which was processed (thread URL in most cases)
