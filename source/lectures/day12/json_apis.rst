********************************
Extracting Data from the Web, II
********************************

.. rst-class:: left
.. container::

    Scraping web pages is tedious and inherently brittle.

    If the owner of the website updates their layout, your code breaks.

    But there is another way to get information from the web in a more
    normalized fashion.


Web Services
============

.. rst-class:: left
.. container::

    A web service is described by the W3C as::

        "a software system designed to support interoperable machine-to-machine
        interaction over a network"

    In general, a web service provides a defined set of functionality and
    returns structured data in response to well-formed requests.

Early Web Services
==================

RSS
---

RSS is one of the earliest forms of Web Services

.. rst-class:: build

* First known as ``RDF Site Summary``
* Became ``Really Simple Syndication``
* More at http://www.rss-specification.com/rss-specifications.htm

.. rst-class:: build
.. container::

    A single web-based *endpoint* provides a dynamically updated listing of
    content

    RSS is implemented in pure HTTP.  The return value is an XML document

    **Atom** is a competing, but similar standard.

.. nextslide:: RSS Format

An RSS document might look something like this:

.. code-block:: xml

    <?xml version="1.0" encoding="UTF-8" ?>
    <rss version="2.0">
    <channel>
      <title>RSS Title</title>
      <description>This is an example of an RSS feed</description>
      <link>http://www.someexamplerssdomain.com/main.html</link>
      <lastBuildDate>Mon, 06 Sep 2010 00:01:00 +0000 </lastBuildDate>
      <pubDate>Mon, 06 Sep 2009 16:45:00 +0000 </pubDate>
      <ttl>1800</ttl>

      <item>
        <title>Example entry</title>
        <description>Here is some text containing an interesting description.</description>
        <link>http://www.wikipedia.org/</link>
        <guid>unique string per item</guid>
        <pubDate>Mon, 06 Sep 2009 16:45:00 +0000 </pubDate>
      </item>
      ...
    </channel>
    </rss>

.. nextslide:: Python RSS Tools

In python, the best tool for consuming RSS, Atom and other related feeds is
`feedparser`_.  It's not part of the standard library, but it is very well
supported and commonly used.

.. _feedparser: https://pythonhosted.org/feedparser/


XML-RPC
-------

RSS provides a pre-defined data set, can we also allow *calling procedures* to
get more dynamic data?

.. rst-class:: build
.. container::

    We can!  Enter XML-RPC (Remote Procedure Call)

    .. rst-class:: build

    * Provides a set of defined procedures which can take arguments
    * Requests are made via HTTP GET, by passing an XML document
    * Responses are sent to the client as an XML document

.. nextslide::

The Python standard library provides modules both for producing *and* for
consuming XML-RPC services.

.. rst-class:: build
.. container::

    Services can be built by registering functions or even entire class objects
    with the ``SimpleXMLRPCServer`` class from the `SimpleXMLRPCServer`_
    library.

    .. _SimpleXMLRPCServer: http://docs.python.org/2/library/simplexmlrpcserver.html

    The `xmlrpclib`_ module provides client methods to consume XML-RPC
    services.

    .. _xmlrpclib: http://docs.python.org/2/library/xmlrpclib.html

    It is worth noting that there are a number of `known vulnerabilities`_ in
    the XML standard and that the ``xmlrpclib`` is **not** secured against
    them. When interacting with XML-RPC online, it behooves you to pay close
    attention to the sources of your data.

.. _known vulnerabilities: http://docs.python.org/2/library/xml.html#xml-vulnerabilities

.. nextslide:: Play with XML-RPC

Though it is much less common to see XML-RPC these days, you can still run into
such services.

You have a :ref:`brief XML-RPC walkthrough assignment <xmlrpc_assignment>` that
will help you to familiarize yourself with this form of Web Services.


SOAP
----

.. rst-class:: build

* XML-RPC allows introspection
* XML-RPC forces you to introspect to get information
* **Wouldn't it be nice to get that automatically?**

* XML-RPC provides data types
* XML-RPC provides only *certain* data types
* **Wouldn't it be nice to have an extensible system for types?**

* XML-RPC allows calling methods with parameters
* XML-RPC only allows calling methods, nothing else
* **wouldn't it be nice to have contextual data as well?**

.. nextslide::

**Enter SOAP: Simple Object Access Protocol**

.. rst-class:: build
.. container::

    SOAP extends XML-RPC in a couple of useful ways:

    .. rst-class:: build

    * It uses the Web Services Description Language (WSDL) to provide meta-data
      about an entire service in a machine-readable format (Automatic
      introspection)

    * It establishes a method for extending available data types using XML
      namespaces (extensible type system)

    * It provides a wrapper around method calls called the **envelope**, which
      allows the inclusion of a **header** with system meta-data that can be
      used by the application (contextual data)


SOAP in Python
--------------

There is no standard library module that supports SOAP directly.

.. rst-class:: build

* The best-known and best-supported module available is **Suds**
* The homepage is https://fedorahosted.org/suds/
* It can be installed using ``easy_install`` or ``pip install``

.. rst-class:: build
.. container::

    **HOWEVER**

    The source for ``suds`` has not seen a commit since `February 13, 2012`_,
    and the last release of the package to PyPI was `even longer ago, in
    2010`_.

.. _February 13, 2012: https://fedorahosted.org/suds/changeset/712
.. _even longer ago, in 2010: https://pypi.python.org/pypi/suds.

.. nextslide::

There *is* an updated version of this package available. It's called
`suds-jurko`_ after the username of the developer who stepped up to lead
development forward.

.. rst-class:: build
.. container::

    I cannot speak to the quality of this package, but it does show a lot of
    activity, with the most recent commits coming in the last few days, and the
    most recent release coming in January 2014.

    .. _suds-jurko: https://pypi.python.org/pypi/suds-jurko/0.6

    I'm not going to bother providing an exercise in SOAP. I've never had an
    interaction with a SOAP service that was a positive experience, and if you
    have any other option, you should avoid it.

    If you do have to use a SOAP web service, prepare yourself for some
    significant time spent trying to get things to work properly.


Afterword
---------

SOAP (and XML-RPC) have some issues.

* XML is pretty damned inefficient as a data transfer medium
* Why should I need to know method names?
* If I can discover method names at all, I have to read a WSDL to do it?

Moreover, for SOAP ``suds`` is the best we have, and it hasn't been updated
since Sept. 2010 by its official owner. What does that say about the utility of
the standard? (At least as perceived by the Python community?)


Modern Web Services
===================

.. rst-class:: left

So, if not XML, then what data format *should* one use to provide responses to
Web Service calls?


JSON
----

JavaScript Object Notation:

.. rst-class:: build

* a lightweight data-interchange format
* easy for humans to read and write
* easy for machines to parse and generate

.. rst-class:: build
.. container::

    JSON is based on two structures:

    * the JavaScript object: ``{ string: value, ...}``
    * the JavaScript array: ``[value, value, ]``

    These both end up looking quite suspiciously like code we are familiar with.

.. nextslide::

In addition JSON provides a few basic data types (see http://json.org/):

.. rst-class:: build

* **string**: unicode, anything but ", \\ and control characters
* **number**: any number, but json does not use octal or hexadecimal
* **object, array**: (we've seen these above)
* **true**: boolean true value
* **false**: boolean false value
* **null**: represents no value, the same as Python's ``None``

.. ifslides::

    .. rst-class:: build
    .. container::
    
        Note that the boolean values *must* be typed as all lowercase letters.
        This is JavaScript, not Python. It's an easy mistake to make.

        Also note that there is no representation of a date type in JSON.

.. ifnotslides::

    .. note::

        Boolean values *must* be typed as all lowercase letters. This is
        JavaScript, not Python. It's an easy mistake to make.

    .. note::

        There is no representation of a date type in JSON.

.. nextslide::

**No date type? OMGWTF??!!1!1**

.. rst-class:: build
.. container::

    You have a couple of options for how to deal with dates and time in JSON.

    .. container::

        Option 1 - Unix Epoch Time (number):

        .. code-block:: pycon

            >>> import time
            >>> time.time()
            1358212616.7691269

    .. container::

        Option 2 - ISO 8661 (string):

        .. code-block:: pycon

            >>> import datetime
            >>> datetime.datetime.now().isoformat()
            '2013-01-14T17:18:10.727240'


JSON in Python
--------------

You can encode Python to json, and decode json back to Python:

.. code-block:: pycon

    >>> import json
    >>> array = [1,2,3]
    >>> json.dumps(array)
    >>> '[1, 2, 3]'
    >>> orig = {'foo': [1,2,3], 'bar': u'my resumé', 'baz': True}
    >>> encoded = json.dumps(orig)
    >>> encoded
    '{"baz": true, "foo": [1, 2, 3], "bar": "my resum\\u00e9"}'
    >>> decoded = json.loads(encoded)
    >>> decoded == orig
    True

Notice that by default, unicode strings are converted to ASCII-compatible
escape characters.

.. nextslide:: Conversion Limitations

Also note that encoding a Python ``tuple`` to JSON and then decoding it back to
Python **cannot produce a tuple**:

.. rst-class:: build
.. container::

    .. code-block:: pycon

        >>> foo = (1, 2, 3)
        >>> encoded = json.dumps(foo)
        >>> encoded
        '[1, 2, 3]'
        >>> bar = json.loads(encoded)
        >>> bar
        [1, 2, 3]
        >>> bar == foo
        False
        >>>

    This is just the way that it is due to the fact that JavaScript does not have
    any data equivalent of the immutable tuple type in Python.

.. nextslide:: Efficiency with JSON

In addition to ``loads`` and ``dumps``, json provides ``load`` and ``dump``.
The difference between these methods is in what argument they accept as the
value to be decoded or encoded.

.. ifnotslides::

    The ``json.loads`` method takes a ``unicode`` object. But ``json.load``
    accepts any object which is **file-like**, meaning that it has a
    ``.read()`` method. This method is optimized so that very large input
    streams can be handled more efficiently.

    The ``json.dumps`` method directly returns a string.  But ``json.dump``
    requires a *second positional argument* which must be a **file-like**
    object, something with a ``.write()`` method.  The value that would
    normally have been returned from the method will instead be written to the
    provided object.

.. ifslides::

    .. rst-class:: build

    * ``json.loads()`` takes a ``unicode`` or ``string`` object
    * ``json.load()`` takes a **file-like** object (has ``.read()``)

      * this is optimized to be efficient with very large input streams

    * ``json.dumps()`` directly returns a string
    * ``json.dump(json, buf)`` takes a *second positional argument*

      * A **file-like** object (has ``.write()``)
      * The ``JSON`` encoded string that would have been returned will be
        written to that stream instead.

.. nextslide:: Customizing Conversions

Both ``json.loads`` and ``json.dumps`` can take an optional ``cls`` argument.
This allows you to pass a subclass of the ``json.JSONEncoder`` or
``json.JSONDecoder`` class to be used instead of those standard objects.

.. nextslide:: Customizing Conversions

You can create powerful, customized encoders and decoders for specialized data
types not recognized by the standard implementations:

.. code-block:: pycon

    >>> import json
    >>> class ComplexEncoder(json.JSONEncoder):
    ...     def default(self, obj):
    ...         if isinstance(obj, complex):
    ...             return [obj.real, obj.imag]
    ...         # Let the base class default method raise the TypeError
    ...         return json.JSONEncoder.default(self, obj)
    ...
    >>> dumps(2 + 1j, cls=ComplexEncoder)
    '[2.0, 1.0]'
    >>> ComplexEncoder().encode(2 + 1j)
    '[2.0, 1.0]'
    >>> list(ComplexEncoder().iterencode(2 + 1j))
    ['[', '2.0', ', ', '1.0', ']']


REST
----

SOAP was invented in part to provide completely machine-readable
interoperability through WSDL.

.. rst-class:: build
.. container::

    Does that really work in real life?

    Hardly ever.

    Another reason was to provide extensibility via custom types defined in WSDL.

    Does that really work in real life?

    Hardly ever.

    So, if neither of these goals is really achieved by using SOAP, why pay all
    the overhead required to use the protocol?

.. nextslide::

Instead, a new form for Web Services was defined, ReST.

.. rst-class:: build
.. container::

    It stands for **Representational State Transfer**.

    .. rst-class:: build

    * Originally described by Roy T. Fielding (worth reading)
    * Use HTTP for what it can do
    * Read more in `this book <http://www.crummy.com/writing/RESTful-Web-Services/>`_

    .. rst-class:: build
    .. container::

        Seriously.

        Buy it and read (http://www.crummy.com/writing/RESTful-Web-Services/)

REST as HTTP
------------

In XML-RCP/SOAP the same HTTP method is used for all calls, and the *endpoint*
is a different function. For a commenting system on a blog, this might look
something like this:

.. rst-class:: build

* POST /getComment HTTP/1.1
* POST /getComments HTTP/1.1
* POST /addComment HTTP/1.1
* POST /editComment HTTP/1.1
* POST /deleteComment HTTP/1.1

.. nextslide::

RESTful Web Services are designed to use HTTP Methods as they were intended to
be used. So the same set of functionality can be expressed like this:

.. rst-class:: build

* GET /comment/<id> HTTP/1.1
* GET /comment HTTP/1.1
* POST /comment HTTP/1.1
* PUT /comment/<id> HTTP/1.1
* DELETE /comment/<id> HTTP/1.1

.. nextslide:: ROA

We call this approach **Resource Oriented Architecture** because each function
is implemented as a different method addressing the same web-based
**resource**.

.. rst-class:: build
.. container::

    The URL represents the *resource* we are working with.

    The HTTP Method represents the ``action`` to be taken.

.. nextslide:: Communicating Outcomes

Similarly, the HTTP Code returned by a RESTful service can tell us the
``result`` of our action.

.. rst-class:: build
.. container::

    For example, in our putative commenting system, consider a POST request to
    create a new comment::

        POST /comment HTTP/1.1

    Possible responses might include:

    .. rst-class:: build

    * Success: ``HTTP/1.1 201 Created``
    * Failure (unauthorized): ``HTTP/1.1 401 Unauthorized``
    * Failure (NotImplemented): ``HTTP/1.1 405 Not Allowed``
    * Failure (ValueError): ``HTTP/1.1 406 Not Acceptable``

.. nextslide:: Communicating Outcomes

Or for a PUT request to edit an existing comment::

    PUT /comment/<id> HTTP/1.1

.. rst-class:: build

* Success: ``HTTP/1.1 200 OK``
* Failure: ``HTTP/1.1 409 Conflict``

.. rst-class:: build
.. container::

    And a DELETE request to remove a comment::

        DELETE /comment/<id> HTTP/1.1

    * Success: ``HTTP/1.1 204 No Content``

REST and Application State
--------------------------

A fundamental truth of HTTP is that the protocol is **stateless**.

.. rst-class:: build
.. container::

    I'll repeat that, for emphasis.

    .. rst-class:: centered

    **HTTP is fundamentally STATELESS**

    No individual request may be assumed to know anything about any other
    request.

    If we are considering a request as a call to take some action on an
    applications (our web service), then the following conclusion may be drawn:

    All the required information representing the possible actions to take
    *should be present in every response*.

    This brings us to the fundamental axiom of RESTful Systems:

    .. rst-class:: centered

    **Hypermedia As The Engine Of Application State**

Applications are State Engines
------------------------------

.. ifnotslides::

    A State Engine is like a machine. The individual parts of the machine may
    be said to be in a given *state* at any one time. For example, the engine
    of a car can be *running* or *stopped*. If the engine is running, the drive
    shaft may be *engaged* or *disengaged* via a transmission.

    The act of *starting the engine* moves the state of the engine from
    *stopped* to *running*. The act of *putting the car in gear* moves the
    drive shaft from the state of *disengaged* to *engaged*.

    In a state engine we call these acts **transitions**.  They exist to move
    the resources of the application between available states.

.. ifslides::

    A state engine is like a machine

    .. rst-class:: build

    * Individual parts are in one given *state* at any time
    * In a car an engine can be *running* or *stopped*
    * Starting the engine moves the state of the engine from stopped to running
    * The drive shaft may be *engaged* or *disengaged*
    * Putting the car in gear moves the drive shaft from disengaged to engaged
    * These acts which move parts from one state to another are called
      **transitions**

.. nextslide:: HTTP State

In HTTP, we have said that no individual request can be asserted to be aware of
the current state of the application to which it will be sent.

To be RESTful, then, a web service response must:

.. rst-class:: build

* provide information about the current state of a resource
* provide information about available transitions for that resource (URIs)
* gracefully respond to incoming requests that reference out-of-date state
  information.
* provide all this in *each* HTTP response


Wrap Up
=======

.. rst-class:: left
.. container::

    Tonight, you'll do a
    :ref:`walkthrough of using a RESTful web service <rest_exercise>` to extend
    the data you built about apartment listings yesterday.

    As you do so, make note of how the information the web service provides you
    indicates what else you can do with the data you get back.
