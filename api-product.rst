.. Product API description
   Added: October 5th, 2015
   Author: Bruno Skvorc <bruno@skvorc.me>

===========
Product API
===========

The Product API is used to parse pages representing products. These can be anything from eBay auction pages and books on Amazon, to leashes and collars in "mom and pop's pet web shop".

The Product API will attempt to recognize some of the most popular product-related fields in any given product page, including but not limited to:

* price
* discount
* availability status
* stock level
* characteristics / stats (like smartphone capacity, battery life, network type...)
* reviews
* unique identification number like SKU / ISBN / MPN / UPC...
* and much more...

For a more thorough walk through the product API, see the `official docs and demo <http://www.diffbot.com/products/automatic/product/>`__.

The Product API part of the Diffbot PHP client consists of two main classes: the API class, and the Product Entity class. We'll describe them in order. Note that the API class extends :php:class:`Swader\\Diffbot\\Abstracts\\Api`, so be sure to read that first if you haven't already.

Product API Class
=================

.. php:namespace:: Swader\Diffbot\Api

.. php:class:: Product

Basic Usage::

    use Swader\Diffbot\Diffbot;

    $url = 'http://some-product-to-process.com';

    $diffbot = new Diffbot('my_token');
    $api = $diffbot->createProductApi($url);

:hidden:`setDiscussion`
"""""""""""""""""""""""

    .. php:method:: setDiscussion($bool = true)

        :param bool $bool: Either ``true`` or ``false``
        :returns: $this

        Whether or not to use the Discussion API to additionally process any detected comment or review threads on the product page. Behaves as if the :php:class:`Swader\\Diffbot\\Api\\Discussion` was set to process the page, and merges the returned data with the Product API's results by means of a ``discussion`` field in the result. The field will have all the sub-fields of the usual :php:class:`Swader\\Diffbot\\Api\\Discussion` call; i.e. you will be able to access the :php:class:`Swader\\Diffbot\\Entity\\Discussion` entity and all its sub entities via the :php:meth:`Swader\\Diffbot\\Entity\\Product::getDiscussion` method.

:hidden:`setColors`
"""""""""""""""""""

    .. php:method:: setColors($bool)

        :param bool $bool: Either ``true`` or ``false``
        :returns: $this

        If set to ``true``, the Product API will try to find out the color options of the product, if available. This feature is experimental and often fails even when color options are obvious.

:hidden:`setAvailability`
"""""""""""""""""""""""""

    .. php:method:: setAvailability($bool)

        :param bool $bool: Either ``true`` or ``false``
        :returns: $this

        If set to ``true``, Diffbot will attempt to find out whether or not the product in question is available / in stock.

:hidden:`setSize`
"""""""""""""""""

    .. php:method:: setSize($bool)

        :param bool $bool: Either ``true`` or ``false``
        :returns: $this

        If set to ``true``, Diffbot will attempt to find out which sizes the product is offered in. Similar to :php:meth:`Swader\\Diffbot\\Api\\Product::setColors`, this method is unreliable and highly experimental.


Product Entity Class
====================

When the Product API is done processing a product (or several) the result will be a Product Entity (i.e. a collection of *one* Product Entities inside an instance of :php:class:`Swader\\Diffbot\\Entity\\EntityIterator`).

For an overview of the abstract class all Entities build on, see :php:class:`Swader\\Diffbot\\Abstracts\\Entity`.

Note that the Product entity can also be returned by the :php:class:`Swader\\Diffbot\\Api\\Analyze` API in "product" mode, or in default mode when processing a URL that contains a product (auto-determined).

.. php:namespace:: Swader\Diffbot\Entity

.. php:class:: Product

:hidden:`__construct`
"""""""""""""""""""""

    .. php:method:: __construct(array $data)

        :param array $data: The data from which to build the Product entity

        The Product entity's constructor needs the data to populate its properties (see getters below). This class is automatically instantiated after a :php:class:`Swader\\Diffbot\\Api\\Product` or :php:class:`Swader\\Diffbot\\Api\\Analyze` call. You probably won't ever need to manually create an instance of this class.

        In the case of the Product entity, the constructor differs from the abstract one (:php:meth:`Swader\\Diffbot\\Abstracts\\Api::__construct`) in that it also looks for the `discussion` key in the result, in order to build a :php:class:`Swader\\Diffbot\\Entity\\Discussion` sub-entity (see :php:meth:`Swader\\Diffbot\\Entity\\Product::getDiscussion`).

:hidden:`getType`
"""""""""""""""""

    .. php:method:: getType()

        :returns: string

        Will always return "product" for products::

            // ... API setup ... //
            $result = $api->call();

            echo $result->getType(); // "product"

:hidden:`getText`
"""""""""""""""""

    .. php:method:: getText()

        :returns: string | null

        Returns the plaintext content of the processed product page. HTML tags are stripped completely, images are removed. If the text property is missing in the result, returns ``null``.

:hidden:`getRegularPrice`
"""""""""""""""""""""""""

    .. php:method:: getRegularPrice()

        :returns: string

        Returns regular price as string, e.g. "$23.99" or "32 kn". If not found, returns offerPrice instead - see :php:meth:`Swader\\Diffbot\\Entity\\Product::getOfferPrice`.

:hidden:`getRegularPriceDetails`
""""""""""""""""""""""""""""""""

    .. php:method:: getRegularPriceDetails()

        :returns: array

        Separates regularPrice into components like currency, amount, and full string. If not found, serves as alias for :php:meth:`Swader\\Diffbot\\Entity\\Product::getOfferPriceDetails`.

        Usage::

            // ... API setup ... //
            $result = $api->call();

            var_dump($result->getRegularPriceDetails());

            /**

            array (size=3)
              'amount' => float 49.85
              'text' => string '£49.85' (length=7)
              'symbol' => string '£' (length=2)

            **/

:hidden:`getShippingAmount`
"""""""""""""""""""""""""""

    .. php:method:: getShippingAmount()

        :returns: string

        Returns shipping price as string, e.g. "$5.99".

:hidden:`getSaveAmount`
"""""""""""""""""""""""

    .. php:method:: getSaveAmount()

        :returns: string

        Returns difference between regular price and offer price, as string, e.g. "$5.99".

:hidden:`getSaveAmountDetails`
""""""""""""""""""""""""""""""

    .. php:method:: getSaveAmountDetails()

        :returns: array

        Separates saveAmount into components like currency, amount, and full string, much like :php:meth:`Swader\\Diffbot\\Entity\\Product::getRegularPriceDetails`. One of the array keys is also a flag indicating whether or not the save amount is a percentage value.

        Usage::

            // ... API setup ... //
            $result = $api->call();

            var_dump($result->getSaveAmountDetails());

            /**

            array (size=4)
              'amount' => float 13.5
              'text' => string '£13.50' (length=7)
              'symbol' => string '£' (length=2)
              'percentage' => boolean false

            **/

:hidden:`getProductId`
""""""""""""""""""""""

    .. php:method:: getProductId()

        :returns: string | null

        Diffbot-determined unique product ID. If upc, isbn, mpn or sku are identified on the page, productId will select from these values in the above order. Null if none found.

:hidden:`getUpc`
""""""""""""""""

    .. php:method:: getUpc()

        :returns: string | null

        UPC number, if found.

:hidden:`getMpn`
""""""""""""""""

    .. php:method:: getMpn()

        :returns: string | null

        MPN number, if found.

:hidden:`getIsbn`
"""""""""""""""""

    .. php:method:: getIsbn()

        :returns: string | null

        ISBN number, if found.

:hidden:`getSku`
"""""""""""""""""

    .. php:method:: getSku()

        :returns: string | null

        Returns Stock Keeping Unit -- store/vendor inventory number or identifier if available. If not, returns null.

:hidden:`getSpecs`
""""""""""""""""""

    .. php:method:: getSpecs()

        :returns: array

        If a specifications table or similar data is available on the product page, individual specifications will be returned in the specs object as name/value pairs. Names will be normalized to lowercase with spaces replaced by underscores, e.g. display_resolution.

        If no specs table is found, an empty array will be returned.


:hidden:`getImages`
"""""""""""""""""""

    .. php:method:: getImages()

        :returns: array

        An array of images found on the product page, with their details. The elements of the array are arrays like this one::

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

        Unlike the :php:class:`Swader\\Diffbot\\Api\\Discussion` API which returns details about discussion posts even when used with the :php:class:`Swader\\Diffbot\\Api\\Product` API, the image data returned with this method is minimal. For fuller details about images, use the :php:class:`Swader\\Diffbot\\Api\\Image` API.

:hidden:`getPrefixCode`
"""""""""""""""""""""""

    .. php:method:: getPrefixCode()

        :returns: string | null

        Country of origin as identified by UPC/ISBN, e.g. "United Kingdom". Null if not present.

:hidden:`getProductOrigin`
""""""""""""""""""""""""""

    .. php:method:: getProductOrigin()

        :returns: string

        If available, two-character ISO country code where the product was produced (e.g. "gb"). Null if not present.

:hidden:`getPriceRange`
"""""""""""""""""""""""

    .. php:method:: getPriceRange()

        :returns: array | null

        If the product is available in a range of prices, the minimum and maximum values will be returned. The lowest price will also be returned as the offerPrice (see :php:meth:`Swader\\Diffbot\\Entity\\Product::getOfferPrice`). If no range is detected, returns null.

:hidden:`getQuantityPrices`
"""""""""""""""""""""""""""

    .. php:method:: getQuantityPrices()

        :returns: array | null

        If the product is available with quantity-based discounts, all identifiable price points will be returned. The lowest price will also be returned as the offerPrice (see :php:meth:`Swader\\Diffbot\\Entity\\Product::getOfferPrice`). If no range is detected, returns null.

:hidden:`isAvailable`
"""""""""""""""""""""

    .. php:method:: isAvailable()

        :returns: bool | null

        Tries to determine whether or not the product is available / in stock. Returns boolean if determined, or null if not.

:hidden:`getOfferPrice`
"""""""""""""""""""""""

    .. php:method:: getOfferPrice()

        :returns: string

        Returns price as string, e.g. "£49.85" or "32 kn".


:hidden:`getOfferPriceDetails`
""""""""""""""""""""""""""""""""

    .. php:method:: getOfferPriceDetails()

        :returns: array

        Separates offerPrice into components like currency, amount, and full string.

        Usage::

            // ... API setup ... //
            $result = $api->call();

            var_dump($result->getOfferPriceDetails());

            /**

            array (size=3)
              'amount' => float 49.85
              'text' => string '£49.85' (length=7)
              'symbol' => string '£' (length=2)

            **/

:hidden:`getSize`
"""""""""""""""""

    .. php:method:: getSize()

        :returns: array | null

        If product is available in different sizes, returns array of those sizes. Highly experimental and often unreliable. This field is optional, and needs to be set on the API. See :php:meth:`Swader\\Diffbot\\Api\\Product::setSize`.

:hidden:`getColors`
"""""""""""""""""""

    .. php:method:: getColors()

        :returns: array | null

        If the product is available in multiple colors, returns the color options. Highly experimental and often unreliable. This field is optional, and needs to be set on the API. See :php:meth:`Swader\\Diffbot\\Api\\Product::setColors`.

:hidden:`getBrand`
""""""""""""""""""

    .. php:method:: getBrand()

        :returns: string

        The brand of the product, as determined by Diffbot.

:hidden:`getDiscussion`
"""""""""""""""""""""""

    .. php:method:: getDiscussion()

        :returns: :php:class:`Swader\\Diffbot\\Entity\\Discussion` | null

        Returns the :php:class:`Swader\\Diffbot\\Entity\\Discussion` found on the product's page (review section). See :php:meth:`Swader\\Diffbot\\Api\\Product::setDiscussion` for details and below for usage::

            use Swader\Diffbot\Diffbot;

            $url = "http://www.sportsdirect.com/slazenger-plain-polo-shirt-mens-542006?colcode=54200601";

            $diffbot = new Diffbot("my_token");
            $api = $diffbot->createProductApi($url);

            $result = $api->call();

            echo $result->getDiscussion()->getNumPosts(); // 10
            echo $result->getDiscussion()->getParticipants(); // 10

        For other methods exposed on the :php:class:`Swader\\Diffbot\\Entity\\Discussion` entity, see its documentation.
