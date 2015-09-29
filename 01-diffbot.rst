.. The overview file describes the purpose of the library
   Added: August 30th, 2015
   Author: Bruno Skvorc <bruno@skvorc.me>

=============
Diffbot Class
=============

The Diffbot class is the first instance a developer must create when using the client. It serves as a container for global settings, and as a factory for the various API endpoint classes.

.. php:namespace:: Swader\Diffbot

.. php:class:: Diffbot

    The Diffbot class takes a single optional argument, the ``$token``, which can be obtained `here <https://diffbot.com/pricing>`__. Instantiate like so::

        $diffbot = new Diffbot("my_token");

    Alternatively, set the token globally, and instantiate without passing in the parameter::

        Diffbot::setToken("my_token");
        $diffbot = new Diffbot();

    Note that if you instantiate without a global token set, and don't pass in a token while instantiating either, you'll get a :php:exc:`Swader\\Diffbot\\Exceptions\\DiffbotException` thrown.


:hidden:`setToken`
""""""""""""""""""

    .. php:staticmethod:: setToken($token)

        :param string $token: The token.
        :returns: void, or throws an `\\InvalidArgumentException <http://php.net/manual/en/class.invalidargumentexception.php>`_ if the token is invalid

        Useful for setting a default token for all future instances.

        Usage::

            Diffbot::setToken("my_token");


:hidden:`getToken`
""""""""""""""""""

    .. php:method:: getToken()

        :returns: null or string

        Returns either the instance token, or the globally defined one - or null if neither is defined

        Usage::

            echo $diffbot->getToken(); // "my_token"


:hidden:`setHttpClient`
"""""""""""""""""""""""

    .. php:method:: setHttpClient(GuzzleHttp\\Client $client)

        :param GuzzleHttp\\Client $client: The HTTP client.
        :returns: $this

        Allows changing of HTTP clients used to send requests to the Diffbot API. Generally useful only during testing, but some edge cases may arise. This method does not need to be called for Diffbot to be usable - it will default to a new instance of the regular GuzzleHttp\Client.

        Usage::

            $client = new GuzzleHttp\Client();
            $diffbot->setHttpClient($client);

:hidden:`getHttpClient`
"""""""""""""""""""""""

    .. php:method:: getHttpClient()

        Returns the currently set HTTP client. Can be changed via :php:meth:`Swader\\Diffbot\\Diffbot::setHttpClient`.

        :returns: GuzzleHttp\\Client

:hidden:`setEntityFactory`
""""""""""""""""""""""""""

    .. php:method:: setEntityFactory($factory)

        :param Swader\\Diffbot\\Interfaces\\EntityFactory $factory: A :php:class:`Swader\\Diffbot\\Interfaces\\EntityFactory` implementation.
        :returns: $this

        Allows for changing the entity factory in use when returning and processing Diffbot-provided data. A custom Entity Factory might, for example, return Author entities (also custom) for all calls to a custom API set up in a user's Diffbot account. This helps with getting fully consumable custom data right from the API source, rather than requiring additional processing.

        If not explicitly set, defaults to built-in :php:class:`Swader\\Diffbot\\Factory\\Entity`.

        Usage::

            $newEntityFactory = new \My\Custom\EntityFactory();

            $diffbot = new Diffbot('my_token');
            $diffbot->setEntityFactory($newEntityFactory);

            // @todo: Full tutorial about a custom Entity and EntityFactory

:hidden:`getEntityFactory`
""""""""""""""""""""""""""

    .. php:method:: getEntityFactory()

        :returns: :php:interface:`Swader\\Diffbot\\Interfaces\\EntityFactory`

        Returns the currently defined :php:interface:`Swader\\Diffbot\\Interfaces\\EntityFactory` instance. This method generally isn't needed outside of testing scenarios. See above for usage of the setter.

:hidden:`createProductApi`
""""""""""""""""""""""""""

    .. php:method:: createProductApi($url)

        :param string $url: URL which is to be processed, or the word "crawl"
        :returns: :php:class:`Swader\\Diffbot\\Api\\Product`

        The product API turns web shops, catalogs, etc. into structured JSON (think eBay, Amazon...). This method creates an instance of the :php:class:`Swader\\Diffbot\\Api\\Product` class. The method accepts a single string as a parameter: either a URL which to process, or the word "crawl" if used in conjunction with the :php:meth:`Swader\\Diffbot\\Diffbot::crawl` method (see below). For a detailed directory of available methods and in depth usage examples, see the :php:class:`Swader\\Diffbot\\Api\\Product` documentation.

        Usage::

            $api = $diffbot->createProductApi("http://www.amazon.com/Oh-The-Places-Youll-Go/dp/0679805273/");
            $result = $api->call();

            echo $result->offerPrice; // $11.99
            echo $result->getIsbn(); // 0679805273

:hidden:`createArticleApi`
""""""""""""""""""""""""""

    .. php:method:: createArticleApi($url)

        :param string $url: URL which is to be processed, or the word "crawl"
        :returns: :php:class:`Swader\\Diffbot\\Api\\Article`

        The article API turns online news posts, blog articles, etc. into structured JSON. This method creates an instance of the :php:class:`Swader\\Diffbot\\Api\\Article` class. The method accepts a single string as a parameter: either a URL which to process, or the word "crawl" if used in conjunction with the :php:meth:`Swader\\Diffbot\\Diffbot::crawl` method (see below). For a detailed directory of available methods and in depth usage examples, see the :php:class:`Swader\\Diffbot\\Api\\Article` documentation.

        Usage::

            $api = $diffbot->createArticleApi("http://techcrunch.com/2012/05/31/diffbot-raises-2-million-seed-round-for-web-content-extraction-technology/");
            $result = $api->call();

            echo $result->publisherCountry; // United States
            echo $result->getAuthor(); // Sarah Perez


:hidden:`createImageApi`
""""""""""""""""""""""""

    .. php:method:: createImageApi($url)

        :param string $url: URL which is to be processed, or the word "crawl"
        :returns: :php:class:`Swader\\Diffbot\\Api\\Image`

        The image API finds images in a post and returns them as JSON. This method creates an instance of the :php:class:`Swader\\Diffbot\\Api\\Image` class. The method accepts a single string as a parameter: either a URL which to process for images, or the word "crawl" if used in conjunction with the :php:meth:`Swader\\Diffbot\\Diffbot::crawl` method (see below). For a detailed directory of available methods and in depth usage examples, see the :php:class:`Swader\\Diffbot\\Api\\Image` documentation. Note that unlike Product and Article, the Image API can return several Image entities (see usage below). If not iterated through, the result refers to the first image only.

        Usage::

            $api = $diffbot->createImageApi("http://smittenkitchen.com/blog/2012/01/buckwheat-baby-with-salted-caramel-syrup/");
            $result = $api->call();

            echo $result->naturalHeight; // 333

            foreach ($result as $image) {
                echo $result->title;
                echo $result->getXPath();
            }

:hidden:`createAnalyzeApi`
""""""""""""""""""""""""""

    .. php:method:: createAnalyzeApi($url)

        :param string $url: URL which is to be processed, or the word "crawl"
        :returns: :php:class:`Swader\\Diffbot\\Api\\Analyze`

        The analyze API tries to autodetect the content it's dealing with (image, product, article...) and extracts it into structured JSON. This method creates an instance of the :php:class:`Swader\\Diffbot\\Api\\Analyze` class. The method accepts a single string as a parameter: either a URL which to process, or the word "crawl" if used in conjunction with the :php:meth:`Swader\\Diffbot\\Diffbot::crawl` method (see below). The Analyze API is the default API used during :php:meth:`Swader\\Diffbot\\Diffbot::crawl` mode.

        Usage::

                $api = $diffbot->createAnalyzeApi("http://techcrunch.com/2012/05/31/diffbot-raises-2-million-seed-round-for-web-content-extraction-technology/");
                $result = $api->call();

                echo $result->publisherCountry; // United States
                echo $result->getAuthor(); // Sarah Perez


:hidden:`createDiscussionApi`
"""""""""""""""""""""""""""""

    .. php:method:: createDiscussionApi($url)

        :param string $url: URL which is to be processed, or the word "crawl"
        :returns: :php:class:`Swader\\Diffbot\\Api\\Discussion`

        The discussion API turns online comments, forum topics or pages of reviews into structured JSON. Think Amazon review section, Youtube comments, article Disqus comments, etc. This method creates an instance of the :php:class:`Swader\\Diffbot\\Api\\Discussion`. The method accepts a single string as a parameter: either a URL which to process, or the word "crawl" if used in conjunction with the :php:meth:`Swader\\Diffbot\\Diffbot::crawl` method (see below). Like the Image API above, this one also returns several :php:class:`Swader\\Diffbot\\Api\\Discussion` entities per call, if available, along with other data - see usage below.

        Usage::

            $api = $diffbot->createDiscussionApi("http://boards.straightdope.com/sdmb/showthread.php?t=740315");
            $result = $api->call();

            echo $result->numPosts; // 43
            echo $result->getParticipants(); // 23

            foreach ($result as $post) {
                echo $post->getAuthor();
                echo $post->votes;
            }

:hidden:`createCustomApi`
"""""""""""""""""""""""""

    .. php:method:: createCustomApi($url, $name)

        :param string $url: URL which is to be processed, or the word "crawl"
        :param string $name: Name of the custom API as defined in the `Diffbot UI <https://diffbot.com/dev>`_
        :returns: :php:class:`Swader\\Diffbot\\Api\\Custom`

        Diffbot customers can define Custom APIs. For a tutorial on doing this, see `here <http://www.sitepoint.com/analyze-sitepoint-author-portfolios-diffbot/>`__. What it comes down to, is that you can tell Diffbot how to recognize certain areas of a web page, and have it translate that into JSON for you if none of the standard APIs do the trick. This allows for much more lightweight and specific calls, resulting in a quicker turnaround and (usually) more precise data. This method creates an instance of the :php:class:`Swader\\Diffbot\\Api\\Custom`. The method accepts two parameters: either a URL which to process, or the word "crawl" if used in conjunction with the :php:meth:`Swader\\Diffbot\\Diffbot::crawl` method (see below), and the name of the custom API to use. Unlike other APIs, this one has no specific entity to return and instead returns a :php:class:`Swader\\Diffbot\\Entity\\Wildcard` entity which matches *anything*.

        Usage::

            $api = $api->createCustomApi("http://sitepoint.com/author/bskvorc", "AuthorFolio");
            $result = $api->call();

            echo $result->bio; // Bruno is a coder from Croatia with Master's Degrees in...

:hidden:`crawl`
"""""""""""""""

    .. php:method:: crawl($name = null, Swader\\Diffbot\\Api $api = null)

        :param string $name: Name of the new crawljob. If omitted, activates read only mode and returns joint data about all defined crawljobs for the current Diffbot token.
        :param Swader\\Diffbot\\Api $api: Instance of the API to process the crawled URLs. If omitted, defaults to :php:class:`Swader\\Diffbot\\Api\\Analyze`.
        :returns: :php:class:`Swader\\Diffbot\\Api\\Crawl`

        The crawl method is used to create new Crawlbot job (crawljob). To find out more about Crawlbot and what, how and why it does what it does, see `here <https://www.diffbot.com/dev/docs/crawl/>`__. I also recommend reading the `Crawlbot API docs <https://www.diffbot.com/dev/docs/crawl/api.jsp>`_ and the `Crawlbot support topics <http://support.diffbot.com/topics/crawlbot/>`_ just so you can dive right in without being too confused by the code below.

        In a nutshell, the Crawlbot crawls a set of seed URLs for links (even if a subdomain is passed to it as seed URL, it still looks through the entire main domain and all other subdomains it can find) and then processes all the pages it can find using the API you define (or opting for Analyze API by default). The result of the call is a collection of :php:class:`Swader\\Diffbot\\Entity\\JobCrawl` objects, each with details about a defined job. To actually get data obtained by crawling and processing, use the :php:meth:`Swader\\Diffbot\\Diffbot::search` API.

        Here's how you can create a crawljob (see detailed :php:class:`Swader\\Diffbot\\Api\\Search` for a step by step guide with explanations)::

            $url = 'crawl';
            $articleApi = $diffbot->createArticleAPI($url)->setDiscussion(false);

            $crawl = $diffbot->crawl('mycrawl_01', $articleApi);

            $crawl->setSeeds(['http://sitepoint.com']);

            $job = $crawl->call();

            // See JobCrawl class to find out which getters are available
            dump($job->getDownloadUrl("json")); // outputs download URL to JSON dataset of the job's result

:hidden:`search`
""""""""""""""""

    .. php:method:: search($q)

        :param string $q: The query to execute against the Search API
        :returns: :php:class:`Swader\\Diffbot\\Api\\Search`

        The Search API is used to search through sets of crawled and processed data obtained through the use of the Crawl or Bulk API. It accepts a simple string query, and returns an array of all matching entities. For a live example of crawl + search implemenation, see `here <http://search.sitepoint.tools>`_, and for a full walkthrough of the Search API, see the :php:class:`Swader\\Diffbot\\Api\\Search` docs.

        Usage::

            $search = $diffbot->search('author:"Miles Johnson" AND type:article');
            $result = $search->call();

            foreach ($result as $article) {
                echo $article->getTitle();
            }

