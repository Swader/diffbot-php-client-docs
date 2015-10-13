.. Description of the EntityIterator
   Added: October 13th, 2015
   Author: Bruno Skvorc <bruno@skvorc.me>

==============
EntityIterator
==============

The EntityIterator is a collection object containing the appropriate entities (:php:class:`Swader\\Diffbot\\Abstracts\\Entity`) of each API.

For example, executing a Product API call on a URL with a product will actually return an EntityIterator instance with a single element instance - a :php:class:`Swader\\Diffbot\\Entity\\Product`. However, the EntityIterator also serves as a proxy to its first element, so accessing a property or a getter on the EntityIterator directly, will in fact access it on the first element. This allows for less verbose constructs. Compare::

    $result = $api->call();
    echo $result->getAuthor();

And::

    $result = $api->call();
    foreach ($result as $entity) {
        echo $entity->getAuthor();
    }

Assuming we called the Product API, the above snippets are identical logically, because the Product API only returns a single Product entity.

As evident above, the EntityIterator also acts as an array, and thus can be fully iterated through for when APIs return sets of entities rather than just one (see :php:class:`Swader\\Diffbot\\Api\\Image`).

.. php:namespace:: Swader\Diffbot\Entity

.. php:class:: EntityIterator


:hidden:`__construct`
"""""""""""""""""""""

    .. php:method:: __construct(array $objects, $response)

        :param array $objects: An array of entities returned by the API
        :param GuzzleHttp\\Message\\ResponseInterface $response: The original Response object returned by the API, useful for getting raw data if you need to additionally process results

        The EntityIterator is automatically constructed by :php:class:`Swader\\Diffbot\\Factory\\Entity` - you'll almost never need to instantiate it yourself. It needs an array of objects, which is an array of Entities (:php:class:`Swader\\Diffbot\\Abstracts\\Entity`) through which one can then iterate when processing results, and a Guzzle Response object which one can use to process the raw return data. See below.

:hidden:`getResponse`
"""""""""""""""""""""

    .. php:method:: getResponse()

        :returns: GuzzleHttp\\Message\\ResponseInterface

        Returns the original Guzzle Response object returned by the Guzzle Client after the API call::

            $result = $api->call();
            var_dump($result->getResponse()->json());

