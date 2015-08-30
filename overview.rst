.. The overview file describes the purpose of the library
   Added: August 30th, 2015
   Author: Bruno Skvorc <bruno@skvorc.me>

========
Overview
========

Diffbot
=======

`Diffbot`_ is a visual machine learning AI which processes renders of web pages to generate structured JSON entities.

In other words, you give Diffbot a URL and it returns human-readable data about it, but doesn't rely on what it finds in the source code - rather, it reads the renders like humans do, visually extracting the human-directed content to provide reliable information about what's actually being said on the page being processed. In that effect, it is relatively untrickable by over-optimized SEO meta content.

Diffbot exposes its services via a set of API endpoints.

To read more about Diffbot, see the `official documentation <https://www.diffbot.com/dev/docs/>`_, or some of the following tutorials:

* `Crawling with Visual Machine Learning <http://www.sitepoint.com/diffbot-crawling-visual-machine-learning>`_
* `Analyze SitePoint Author Portfolios with Diffbot <http://www.sitepoint.com/analyze-sitepoint-author-portfolios-diffbot/>`_
* `Collections and Merged APIs <http://www.sitepoint.com/diffbot-repeated-collections-merged-apis/>`_
* `Other tutorials <http://www.sitepoint.com/tag/diffbot/>`_

Diffbot PHP Client
==================

The Diffbot PHP Client is the official PHP wrapper for the API endpoints Diffbot provides.

By using the PHP client, the developer can interact with both the APIs and the returned entities in an object oriented manner, rather than parse JSON and extract data manually. The PHP client uses Guzzle to issue requests to the API. It is currently built on top of Guzzle 5, and there are no immediate plans to transition to the feature-lacking version 6.

Quickstart
~~~~~~~~~~

Install via Composer::

    composer require swader/diffbot-php-client

Create a Diffbot instance, provide a token, specify the URL you want to process, and use all this to create an instance of the API endpoint::

    $diffbot = new Diffbot('my_token');
    $url = 'http://www.sitepoint.com/diffbot-crawling-visual-machine-learning/';
    $articleApi = $diffbot->createArticleAPI($url);

Configure the API call with some setters (all will be explained in this documentation) and issue the call::

    $processedArticle = $articleApi->setDiscussion(false);

Consume the resulting data entity any way you see fit::

    echo $processedArticle->author; // Bruno Skvorc

.. _`Diffbot`: https://diffbot.com