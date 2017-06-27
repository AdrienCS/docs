3.5 Guide de Migration
###################

CakePHP 3.5 is an API compatible upgrade from 3.4. This page outlines the
changes and improvements made in 3.5.

To upgrade to 3.5.x run the following composer command:

.. code-block:: bash

    php composer.phar require --update-with-dependencies "cakephp/cakephp:3.5.*"

Deprecations
============

The following is a list of deprecated methods, properties and behaviors. These
features will continue to function until 4.0.0 after which they will be removed.

* ``Cake\Http\Client\CookieCollection`` is deprecated. Use
  ``Cake\Http\Cookie\CookieCollection`` instead.
* ``Cake\View\Helper\RssHelper`` is deprecated. Due to infrequent use the
  RssHelper is deprecated.
* ``Cake\Controller\Component\CsrfComponent`` is deprecated. Use
  :ref:`csrf-middleware` instead.
* ``Cake\Datasource\TableSchemaInterface`` is deprecated. Use
  ``Cake\Database\TableSchemaAwareInterface`` instead.
* ``Cake\Console\ShellDispatcher`` is deprecated. Applications should update to
  use ``Cake\Console\CommandRunner`` instead.

Deprecated Combined Get/Set Methods
-----------------------------------

In the past CakePHP has leveraged 'modal' methods that provide both
a get and set mode. These methods complicate IDE autocompletion and our ability
to add stricter return types in the future. For these reasons, combined get/set
methods are being split into separate get and set methods.

The following is a list of methods that are deprecated and replaced with
``getX()`` and ``setX()`` methods:

``Cake\Cache\Cache``
    * ``config()``
    * ``registry()``
``Cake\Console\Shell``
    * ``io()``
``Cake\Console\ConsoleIo``
    * ``outputAs()``
``Cake\Console\ConsoleOutput``
    * ``outputAs()``
``Cake\Database\Connection``
    * ``logger()``
``Cake\Datasource\TypedResultTrait``
    * ``returnType()``
``Cake\Database\Log\LoggingStatement``
    * ``logger()``
``Cake\Datasource\ModelAwareTrait``
    * ``modelType()``
``Cake\Database\Query``
    * ``valueBinder()`` is now ``getValueBinder()``
``Cake\Datasource\QueryTrait``
    * ``eagerLoaded()`` (now ``isEagerLoaded()``)
``Cake\Event\EventDispatcherTrait``
    * ``eventManager()``
``Cake\Error\Debugger``
    * ``outputAs()`` (now ``getOutputFormat()`` / ``setOutputFormat()``)
``Cake\Http\ServerRequest``
    * ``env()`` (now ``getEnv()`` / ``withEnv()``)
``Cake\I18n\I18n``
    * ``locale()``
    * ``translator()``
``Cake\ORM\LocatorAwareTrait``
    * ``tableLocator()``
``Cake\ORM\EntityTrait``
    * ``invalid()`` (now ``getInvalid()``, ``setInvalid()``,
      ``setInvalidField()``, and ``getInvalidField()``)
``Cake\ORM\Table``
    * ``validator()``
``Cake\Routing\RouteCollection``
    * ``extensions()``
``Cake\TestSuite\TestFixture``
    * ``schema()``
``Cake\Utility\Security``
    * ``salt()``
``Cake\View\View``
    * ``template()``
    * ``layout()``
    * ``theme()``
    * ``templatePath()``
    * ``layoutPath()``
    * ``autoLayout()`` (now ``isAutoLayoutEnabled()`` / ``enableAutoLayout()``)

Behavior Changes
================

While these changes are API compatible, they represent minor variances in
behavior that may affect your application:

* ``BehaviorRegistry``, ``HelperRegistry`` et ``ComponentRegistry`` leveront une exception will now
  raise exceptions when ``unload()`` is called with an unknown object name. This
  change should help find errors easier by making possible typos more visible.
* ``HasMany`` associations now gracefully handle empty values set for the
  association property, similar to ``BelongsToMany`` associations - that is they
  treat ``false``, ``null``, and empty strings the same way as empty arrays. For
  ``HasMany`` associations this now results in all associated records to be
  deleted/unlinked in case the ``replace`` save strategy is being used.
  Consequently this allows to use forms to delete/unlink all associated records
  by passing an empty string. Previously this would have required custom
  marshalling logic, without that it would have only been possible to remove all
  but one record, as form data cannot be used to describe empty arrays.
* ``Http\Client`` no longer uses the ``cookie()`` method results when building
  requests. Instead the ``Cookie`` header and internal CookieCollection are
  used. This should only effect applications that have a custom HTTP adapter in
  their clients.
* Multi-word subcommand names previouly required camelBacked names to be used
  when invoking shells. Now subcommands can be invoked with underscored_names.
  For example: ``cake tool initMyDb`` can now be called with ``cake tool
  init_my_db``. If your shells previously bound two subcommands with different
  inflections, only the last bound command will function.
* ``SecurityComponent`` will blackhole post requests that have no request data
  now. This change helps protect actions that create records using database
  defaults alone.
* ``Cake\ORM\Table::addBehavior()`` and ``removeBehavior()`` now return
  ``$this`` to assist in defining table objects in a fluent fashion.
* Cache engines no longer throw an exception when they fail or are misconfigured,
  but instead fall back to the noop ``NullEngine``. Fallbacks can also be
  :ref:`configured <cache-configuration-fallback>` on a per-engine basis.

New Features
============

Cache
-----

* Cache engines can now be configured with a ``fallback`` key that defines a
  cache configuration to fall back to if the engine is misconfigured (or
  unavailable). See :ref:`cache-configuration-fallback` for more information on
  configuring fallbacks.

Core
----

* ``Cake\Core\ObjectRegistry`` now implements the ``Countable`` and
  ``IteratorAggregate`` interfaces.

Console
-------

* ``Cake\Console\ConsoleOptionParser::setHelpAlias()`` was added. This method
  allows you to set the command name used when generating help output. Defaults
  to ``cake``.
* ``Cake\Console\CommandRunnner`` was added replacing
  ``Cake\Console\ShellDispatcher``.
* ``Cake\Console\CommandCollection`` was added to provide an interface for
  applications to define the command line tools they offer.

Datasource
----------

* ``Cake\Datasource\SchemaInterface`` was added.
* New abstract types were added for ``smallinteger`` and ``tinyinteger``.
  Existing ``SMALLINT`` and ``TINYINT`` columns will now be reflected as these
  new abstract types. ``TINYINT(1)`` columns will continue to be treated as
  boolean columns in MySQL.
* ``Cake\Datasource\PaginatorInterface`` was added. The ``PaginatorComponent``
  now uses this interface to interact with paginators. This allows other
  ORM-like implementations to be paginated by the component.
* ``Cake\Datasource\Paginator`` was added to paginate ORM/Database Query
  instances.

Event
-----

* ``Cake\Event\EventManager::on()`` and ``off()`` methods are now chainable
  making it simpler to set multiple events at once.

Http
----

* New Cookie & CookieCollection classes have been added. These classes allow you
  to work with cookies in an object-orientated way, and are available on
  ``Cake\Http\ServerRequest``, ``Cake\Http\Repsonse``, and
  ``Cake\Http\Client\Response``. See the :ref:`request-cookies` and
  :ref:`response-cookies` for more information.
* New middleware has been added to make applying security headers easier. See
  :ref:`security-header-middleware` for more information.
* New middleware has been added to transparently encrypt cookie data. See
  :ref:`encrypted-cookie-middleware` for more information.
* New middleware has been added to make protecting against CSRF easier. See
  :ref:`csrf-middleware` for more information.
* ``Cake\Http\Client::addCookie()`` was added to make it easy to add cookies to
  a client instance.

ORM
---

* ``Cake\ORM\Query::contain()`` now allows you to call it without the wrapping
  array when containing a single association. ``contain('Comments', function ()
  { ... });`` will now work. This makes ``contain()`` consistent with other
  eagerloading related methods like ``leftJoinWith()`` and ``matching()``.

Routing
-------

* ``Cake\Routing\Router::reverseToArray()`` was added. This method allow you to
  convert a request object into an array that can be used to generate URL
  strings.
* ``Cake\Routing\RouteBuilder::resources()`` had the ``path`` option
  added. This option lets you make the resource path and controller name not
  match.
* ``Cake\Routing\RouteBuilder`` now has methods to create routes for
  specific HTTP methods. e.g ``get()`` and ``post()``.
* ``Cake\Routing\RouteBuilder::loadPlugin()`` was added.
* ``Cake\Routing\Route`` now has fluent methods for defining options.

TestSuite
---------

* ``IntegrationTestCase::head()`` was added.
* ``IntegrationTestCase::options()`` was added.
* ``IntegrationTestCase::disableErrorHandlerMiddleware()`` was added to make
  debugging errors easier in integration tests.

Validation
----------
* ``Cake\Validation\Validator::regex()`` was added for a more convenient way
  to validate data against a regex pattern.
* ``Cake\Validation\Validator::addDefaultProvider()`` was added. This method
  lets you inject validation providers into all the validators created in your
  application.
* ``Cake\Validation\ValidatorAwareInterface`` was added to define the methods
  implemented by ``Cake\Validation\ValidatorAwareTrait``.

View
----

* ``Cake\View\Helper\PaginatorHelper::limitControl()`` was added. This method
  lets you create a form with a select box for updating the limit value on
  a paginated result set.
