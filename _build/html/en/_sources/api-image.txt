.. Image API documentation
   Added: October 7th, 2015
   Author: Bruno Skvorc <bruno@skvorc.me>

=========
Image API
=========

This API is used to turn content like image galleries, Instagram posts, or image-rich articles into JSON.

For examples of data that might be returned, please see http://diffbot.com and run the Image API demo.

The Image API part of the Diffbot PHP client consists of two main classes: the API class, and the Image Entity class. We'll describe them in order. Note that the API class extends :php:class:`Swader\\Diffbot\\Abstracts\\Api`, so be sure to read that first if you haven't already.

Image API Class
===============

.. php:namespace:: Swader\Diffbot\Api

.. php:class:: Image

Basic Usage::

    use Swader\Diffbot\Diffbot;

    $url = 'http://some-article-to-process.com';

    $diffbot = new Diffbot('my_token');
    $api = $diffbot->createImageApi($url);

:hidden:`setMentions`
"""""""""""""""""""""

    .. php:method:: setMentions($bool)

        :param bool $bool: Either ``true`` or ``false``
        :returns: $this

        If set to true, the Image API will attempt to identify other locations online where the image was used - similar to Google Image reverse search.

:hidden:`setFaces`
""""""""""""""""""

    .. php:method:: setFaces($bool)

        :param bool $bool: Either ``true`` or ``false``
        :returns: $this

        Highly experimental. Finds the x, y, height and width of coordinates of human faces.

:hidden:`setOcr`
""""""""""""""""

    .. php:method:: setOcr($bool)

        :param bool $bool: Either ``true`` or ``false``
        :returns: $this

        Highly experimental. If set, attempts to recognize and read text in the images.

Image Entity Class
==================

When the Image API is done processing a URL, the result will be an instance of :php:class:`Swader\\Diffbot\\Entity\\EntityIterator` containing one Image Entity instance for every image found.

For an overview of the abstract class all Entities (including Image) build on, see :php:class:`Swader\\Diffbot\\Abstracts\\Entity`.

Note that the Image entities can also be returned by the :php:class:`Swader\\Diffbot\\Api\\Analyze` API in "image" mode, or in default mode when processing a URL that is essentially an image.

.. php:namespace:: Swader\Diffbot\Entity

.. php:class:: Image

:hidden:`getType`
"""""""""""""""""

    .. php:method:: getType()

        :returns: string

        Will always return "image" for images::

            // ... API setup ... //
            $result = $api->call();

            echo $result->getType(); // "image"


:hidden:`getHeight`
"""""""""""""""""""

    .. php:method:: getHeight()

        :returns: int

        Height of image if resized by browser via CSS / JS. If not resized, serves as alias for :php:meth:`Swader\\Diffbot\\Entity\\Image::getNaturalHeight`.

:hidden:`getWidth`
""""""""""""""""""

    .. php:method:: getWidth()

        :returns: int

        Width of image if resized by browser via CSS / JS. If not resized, serves as alias for :php:meth:`Swader\\Diffbot\\Entity\\Image::getNaturalWidth`.

:hidden:`getNaturalHeight`
""""""""""""""""""""""""""

    .. php:method:: getNaturalHeight()

        :returns: int

        Raw image height, in pixels.

:hidden:`getNaturalWidth`
"""""""""""""""""""""""""

    .. php:method:: getNaturalWidth()

        :returns: int

        Raw image width, in pixels.

:hidden:`getUrl`
""""""""""""""""

    .. php:method:: getUrl()

        :returns: string

        URL of the image

:hidden:`getAnchorUrl`
""""""""""""""""""""""

    .. php:method:: getAnchorUrl()

        :returns: string | null

        URL the image links to, if any. Null if image isn't linked.

:hidden:`getXPath`
""""""""""""""""""

    .. php:method:: getXPath()

        :returns: string

        The XPath expression of the position of the image node in the DOM.

:hidden:`getMentions`
"""""""""""""""""""""

    .. php:method:: getMentions()

        :returns: array

        Returns an array of [title => "title", link => "link"] arrays for all posts where this image, or a similar one, was found. If not found, returns empty array.

:hidden:`getFaces`
""""""""""""""""""

    .. php:method:: getFaces()

        :returns: array | string

        Finds the x, y, height and width of coordinates of human faces, returns array of arrays with those keys. In most cases, does not work at all and is in heavy alpha mode. Do not rely on this method for anything. Returns empty string if nothing found.

:hidden:`getOcr`
""""""""""""""""

    .. php:method:: getOcr()

        :returns: string

        The text recognized in the picture. In most cases, does not work at all and is in heavy alpha mode. Do not rely on this method for anything. Returns empty string if nothing found.
