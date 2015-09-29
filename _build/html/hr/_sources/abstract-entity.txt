.. Entity Abstract class
   Added: September 20th, 2015
   Author: Bruno Skvorc <bruno@skvorc.me>

===============
Entity Abstract
===============

This page will describe the Entity Abstract class. This class is the root of all Entity classes. Entity classes are used as containers for return values from various API endpoints. For example, the Article API will return an Article Entity, the Discussion API will return a Discussion Entity, and so on.

It is important to note that an API class will *never* return an Entity class directly. Rather, it will return an :php:class:`Swader\\Diffbot\\Entity\\EntityIterator`, an iterable container with all the Entities inside. The container, however, is configured in such a way that executing *get* methods on it directly will forward those calls to the first Entity in its dataset. Thus, in instances where

.. php:namespace:: Swader\Diffbot\Abstracts

.. php:class:: Entity

:hidden:`__construct`
"""""""""""""""""""""

    .. php:method:: __construct(array $data)

        This class takes a single argument during construction, an array of data. This data is then turned into gettable information by means of getters, both direct and magic. Some getters do additional processing of the data in order to make it more useful to the user.

        :param array $data: The data

:hidden:`getData`
"""""""""""""""""

    .. php:method:: getData()

        Returns the raw data passed into the Entity by the parent API class. This will be an associative array (see Usage below).

        :returns: array

        Usage::

            // ...

            $data = $article->getData();

            echo $data['title'];
            echo $data['author'];

            // etc.

:hidden:`__call`
""""""""""""""""

    .. php:method:: __call()

        Magic method for resolving undefined getters and only getters. If the method being called starts with ``get``, the remainder of its name will be turned into a key to search inside the `$data` property (see ``getData``). Once the call is identified as a *getter* call, ``__get`` is invoked (see below).

        :returns: mixed
        :throws: BadMethodCallException if the prefix of the method is not ``get``

:hidden:`__get`
"""""""""""""""

    .. php:method:: __get()

    This method is called automatically when ``__call`` is called. It looks for the property being asked for inside the ``$data`` property of the current class, or returns null if not found.

    :returns: string

    Usage::

        $api-> // ... set up API
        $myUrl = $api->buildUrl();

