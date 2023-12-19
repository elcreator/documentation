---
hide:
    - navigation
---

!!! info "NOTE"

    All classes are prefixed with `Phalcon`



## Di\AbstractInjectionAware ![Abstract](../assets/images/abstract-green.svg) 

[Source on GitHub](https://github.com/phalcon/cphalcon/blob/5.0.x/phalcon/Di/AbstractInjectionAware.zep)


-   __Namespace__

    - `Phalcon\Di`

-   __Uses__
    

-   __Extends__
    

-   __Implements__
    
    - `InjectionAwareInterface`

This abstract class offers common access to the DI in a class


### Properties
```php
/**
 * Dependency Injector
 *
 * @var DiInterface
 */
protected container;

```

### Methods

```php
public function getDI(): DiInterface;
```
Returns the internal dependency injector


```php
public function setDI( DiInterface $container ): void;
```
Sets the dependency injector




## Di\Di 

[Source on GitHub](https://github.com/phalcon/cphalcon/blob/5.0.x/phalcon/Di/Di.zep)


-   __Namespace__

    - `Phalcon\Di`

-   __Uses__
    
    - `Phalcon\Config\Adapter\Php`
    - `Phalcon\Config\Adapter\Yaml`
    - `Phalcon\Config\ConfigInterface`
    - `Phalcon\Di\DiInterface`
    - `Phalcon\Di\Exception`
    - `Phalcon\Di\Exception\ServiceResolutionException`
    - `Phalcon\Di\InitializationAwareInterface`
    - `Phalcon\Di\InjectionAwareInterface`
    - `Phalcon\Di\Service`
    - `Phalcon\Di\ServiceInterface`
    - `Phalcon\Di\ServiceProviderInterface`
    - `Phalcon\Events\ManagerInterface`

-   __Extends__
    

-   __Implements__
    
    - `DiInterface`

Phalcon\Di\Di is a component that implements Dependency Injection/Service
Location of services and it's itself a container for them.

Since Phalcon is highly decoupled, Phalcon\Di\Di is essential to integrate the
different components of the framework. The developer can also use this
component to inject dependencies and manage global instances of the different
classes used in the application.

Basically, this component implements the `Inversion of Control` pattern.
Applying this, the objects do not receive their dependencies using setters or
constructors, but requesting a service dependency injector. This reduces the
overall complexity, since there is only one way to get the required
dependencies within a component.

Additionally, this pattern increases testability in the code, thus making it
less prone to errors.

```php
use Phalcon\Di\Di;
use Phalcon\Http\Request;

$di = new Di();

// Using a string definition
$di->set("request", Request::class, true);

// Using an anonymous function
$di->setShared(
    "request",
    function () {
        return new Request();
    }
);

$request = $di->getRequest();
```


### Properties
```php
/**
 * List of registered services
 *
 * @var ServiceInterface[]
 */
protected services;

/**
 * List of shared instances
 *
 * @var array
 */
protected sharedInstances;

/**
 * Events Manager
 *
 * @var ManagerInterface|null
 */
protected eventsManager;

/**
 * Latest DI build
 *
 * @var DiInterface|null
 */
protected static defaultDi;

```

### Methods

```php
public function __call( string $method, array $arguments = [] ): mixed | null;
```
Magic method to get or set services using setters/getters


```php
public function __construct();
```
Phalcon\Di\Di constructor


```php
public function attempt( string $name, mixed $definition, bool $shared = bool ): ServiceInterface | bool;
```
Attempts to register a service in the services container
Only is successful if a service hasn't been registered previously
with the same name


```php
public function get( string $name, mixed $parameters = null ): mixed;
```
Resolves the service based on its configuration


```php
public static function getDefault(): DiInterface | null;
```
Return the latest DI created


```php
public function getInternalEventsManager(): ManagerInterface | null;
```
Returns the internal event manager


```php
public function getRaw( string $name ): mixed;
```
Returns a service definition without resolving


```php
public function getService( string $name ): ServiceInterface;
```
Returns a Phalcon\Di\Service instance


```php
public function getServices(): ServiceInterface[];
```
Return the services registered in the DI


```php
public function getShared( string $name, mixed $parameters = null ): mixed;
```
Resolves a service, the resolved service is stored in the DI, subsequent
requests for this service will return the same instance


```php
public function has( string $name ): bool;
```
Check whether the DI contains a service by a name


```php
public function loadFromPhp( string $filePath ): void;
```
Loads services from a php config file.

```php
$di->loadFromPhp("path/services.php");
```

And the services can be specified in the file as:

```php
return [
     'myComponent' => [
         'className' => '\Acme\Components\MyComponent',
         'shared' => true,
     ],
     'group' => [
         'className' => '\Acme\Group',
         'arguments' => [
             [
                 'type' => 'service',
                 'service' => 'myComponent',
             ],
         ],
     ],
     'user' => [
         'className' => '\Acme\User',
     ],
];
```

@link https://docs.phalcon.io/en/latest/reference/di.html


```php
public function loadFromYaml( string $filePath, array $callbacks = null ): void;
```
Loads services from a yaml file.

```php
$di->loadFromYaml(
    "path/services.yaml",
    [
        "!approot" => function ($value) {
            return dirname(__DIR__) . $value;
        }
    ]
);
```

And the services can be specified in the file as:

```php
myComponent:
    className: \Acme\Components\MyComponent
    shared: true

group:
    className: \Acme\Group
    arguments:
        - type: service
          name: myComponent

user:
   className: \Acme\User
```

@link https://docs.phalcon.io/en/latest/reference/di.html


```php
public function offsetExists( mixed $name ): bool;
```
Check if a service is registered using the array syntax


```php
public function offsetGet( mixed $name ): mixed;
```
Allows to obtain a shared service using the array syntax

```php
var_dump($di["request"]);
```


```php
public function offsetSet( mixed $name, mixed $definition ): void;
```
Allows to register a shared service using the array syntax

```php
$di["request"] = new \Phalcon\Http\Request();
```


```php
public function offsetUnset( mixed $name ): void;
```
Removes a service from the services container using the array syntax


```php
public function register( ServiceProviderInterface $provider ): void;
```
Registers a service provider.

```php
use Phalcon\Di\DiInterface;
use Phalcon\Di\ServiceProviderInterface;

class SomeServiceProvider implements ServiceProviderInterface
{
    public function register(DiInterface $di)
    {
        $di->setShared(
            'service',
            function () {
                // ...
            }
        );
    }
}
```


```php
public function remove( string $name ): void;
```
Removes a service in the services container
It also removes any shared instance created for the service


```php
public static function reset(): void;
```
Resets the internal default DI


```php
public function set( string $name, mixed $definition, bool $shared = bool ): ServiceInterface;
```
Registers a service in the services container


```php
public static function setDefault( DiInterface $container ): void;
```
Set a default dependency injection container to be obtained into static
methods


```php
public function setInternalEventsManager( ManagerInterface $eventsManager );
```
Sets the internal event manager


```php
public function setService( string $name, ServiceInterface $rawDefinition ): ServiceInterface;
```
Sets a service using a raw Phalcon\Di\Service definition


```php
public function setShared( string $name, mixed $definition ): ServiceInterface;
```
Registers an "always shared" service in the services container


```php
protected function loadFromConfig( ConfigInterface $config ): void;
```
Loads services from a Config object.




## Di\DiInterface ![Interface](../assets/images/interface-blue.svg) 

[Source on GitHub](https://github.com/phalcon/cphalcon/blob/5.0.x/phalcon/Di/DiInterface.zep)


-   __Namespace__

    - `Phalcon\Di`

-   __Uses__
    
    - `ArrayAccess`

-   __Extends__
    
    `ArrayAccess`

-   __Implements__
    

Interface for Phalcon\Di\Di


### Methods

```php
public function attempt( string $name, mixed $definition, bool $shared = bool ): ServiceInterface | bool;
```
Attempts to register a service in the services container
Only is successful if a service hasn't been registered previously
with the same name


```php
public function get( string $name, mixed $parameters = null ): mixed;
```
Resolves the service based on its configuration


```php
public static function getDefault(): DiInterface | null;
```
Return the last DI created


```php
public function getRaw( string $name ): mixed;
```
Returns a service definition without resolving


```php
public function getService( string $name ): ServiceInterface;
```
Returns the corresponding Phalcon\Di\Service instance for a service


```php
public function getServices(): ServiceInterface[];
```
Return the services registered in the DI


```php
public function getShared( string $name, mixed $parameters = null ): mixed;
```
Returns a shared service based on their configuration


```php
public function has( string $name ): bool;
```
Check whether the DI contains a service by a name


```php
public function remove( string $name ): void;
```
Removes a service in the services container


```php
public static function reset(): void;
```
Resets the internal default DI


```php
public function set( string $name, mixed $definition, bool $shared = bool ): ServiceInterface;
```
Registers a service in the services container


```php
public static function setDefault( DiInterface $container ): void;
```
Set a default dependency injection container to be obtained into static
methods


```php
public function setService( string $name, ServiceInterface $rawDefinition ): ServiceInterface;
```
Sets a service using a raw Phalcon\Di\Service definition


```php
public function setShared( string $name, mixed $definition ): ServiceInterface;
```
Registers an "always shared" service in the services container




## Di\Exception 

[Source on GitHub](https://github.com/phalcon/cphalcon/blob/5.0.x/phalcon/Di/Exception.zep)


-   __Namespace__

    - `Phalcon\Di`

-   __Uses__
    

-   __Extends__
    
    `\Exception`

-   __Implements__
    

Exceptions thrown in Phalcon\Di will use this class



## Di\Exception\ServiceResolutionException 

[Source on GitHub](https://github.com/phalcon/cphalcon/blob/5.0.x/phalcon/Di/Exception/ServiceResolutionException.zep)


-   __Namespace__

    - `Phalcon\Di\Exception`

-   __Uses__
    

-   __Extends__
    
    `\Phalcon\Di\Exception`

-   __Implements__
    

Phalcon\Di\Exception\ServiceResolutionException



## Di\FactoryDefault 

[Source on GitHub](https://github.com/phalcon/cphalcon/blob/5.0.x/phalcon/Di/FactoryDefault.zep)


-   __Namespace__

    - `Phalcon\Di`

-   __Uses__
    
    - `Phalcon\Filter\FilterFactory`

-   __Extends__
    
    `\Phalcon\Di\Di`

-   __Implements__
    

This is a variant of the standard Phalcon\Di\Di. By default it automatically
registers all the services provided by the framework. Thanks to this, the
developer does not need to register each service individually providing a
full stack framework


### Methods

```php
public function __construct();
```
Phalcon\Di\FactoryDefault constructor




## Di\FactoryDefault\Cli 

[Source on GitHub](https://github.com/phalcon/cphalcon/blob/5.0.x/phalcon/Di/FactoryDefault/Cli.zep)


-   __Namespace__

    - `Phalcon\Di\FactoryDefault`

-   __Uses__
    
    - `Phalcon\Di\FactoryDefault`
    - `Phalcon\Di\Service`
    - `Phalcon\Filter\FilterFactory`

-   __Extends__
    
    `FactoryDefault`

-   __Implements__
    

Phalcon\Di\FactoryDefault\Cli

This is a variant of the standard Phalcon\Di. By default it automatically
registers all the services provided by the framework.
Thanks to this, the developer does not need to register each service individually.
This class is specially suitable for CLI applications


### Methods

```php
public function __construct();
```
Phalcon\Di\FactoryDefault\Cli constructor




## Di\InitializationAwareInterface ![Interface](../assets/images/interface-blue.svg) 

[Source on GitHub](https://github.com/phalcon/cphalcon/blob/5.0.x/phalcon/Di/InitializationAwareInterface.zep)


-   __Namespace__

    - `Phalcon\Di`

-   __Uses__
    

-   __Extends__
    

-   __Implements__
    

Interface for components that have `initialize()`


### Methods

```php
public function initialize(): void;
```





## Di\Injectable ![Abstract](../assets/images/abstract-green.svg) 

[Source on GitHub](https://github.com/phalcon/cphalcon/blob/5.0.x/phalcon/Di/Injectable.zep)


-   __Namespace__

    - `Phalcon\Di`

-   __Uses__
    
    - `Phalcon\Di\Di`
    - `Phalcon\Session\BagInterface`
    - `stdClass`

-   __Extends__
    
    `stdClass`

-   __Implements__
    
    - `InjectionAwareInterface`

This class allows to access services in the services container by just only
accessing a public property with the same name of a registered service

@property \Phalcon\Mvc\Dispatcher|\Phalcon\Mvc\DispatcherInterface $dispatcher
@property \Phalcon\Mvc\Router|\Phalcon\Mvc\RouterInterface $router
@property \Phalcon\Mvc\Url|\Phalcon\Mvc\Url\UrlInterface $url
@property \Phalcon\Http\Request|\Phalcon\Http\RequestInterface $request
@property \Phalcon\Http\Response|\Phalcon\Http\ResponseInterface $response
@property \Phalcon\Http\Response\Cookies|\Phalcon\Http\Response\CookiesInterface $cookies
@property \Phalcon\Filter\Filter $filter
@property \Phalcon\Flash\Direct $flash
@property \Phalcon\Flash\Session $flashSession
@property \Phalcon\Session\ManagerInterface $session
@property \Phalcon\Events\Manager|\Phalcon\Events\ManagerInterface $eventsManager
@property \Phalcon\Db\Adapter\AdapterInterface $db
@property \Phalcon\Encryption\Security $security
@property \Phalcon\Encryption\Crypt|\Phalcon\Encryption\Crypt\CryptInterface $crypt
@property \Phalcon\Html\TagFactory $tag
@property \Phalcon\Html\Escaper|\Phalcon\Html\Escaper\EscaperInterface $escaper
@property \Phalcon\Annotations\Adapter\Memory|\Phalcon\Annotations\Adapter $annotations
@property \Phalcon\Mvc\Model\Manager|\Phalcon\Mvc\Model\ManagerInterface $modelsManager
@property \Phalcon\Mvc\Model\MetaData\Memory|\Phalcon\Mvc\Model\MetadataInterface $modelsMetadata
@property \Phalcon\Mvc\Model\Transaction\Manager|\Phalcon\Mvc\Model\Transaction\ManagerInterface $transactionManager
@property \Phalcon\Assets\Manager $assets
@property \Phalcon\Di\Di|\Phalcon\Di\Di\DiInterface $di
@property \Phalcon\Session\Bag|\Phalcon\Session\BagInterface $persistent
@property \Phalcon\Mvc\View|\Phalcon\Mvc\ViewInterface $view


### Properties
```php
/**
 * Dependency Injector
 *
 * @var DiInterface|null
 */
protected container;

```

### Methods

```php
public function __get( string $propertyName ): mixed | null;
```
Magic method __get


```php
public function __isset( string $name ): bool;
```
Magic method __isset


```php
public function getDI(): DiInterface;
```
Returns the internal dependency injector


```php
public function setDI( DiInterface $container ): void;
```
Sets the dependency injector




## Di\InjectionAwareInterface ![Interface](../assets/images/interface-blue.svg) 

[Source on GitHub](https://github.com/phalcon/cphalcon/blob/5.0.x/phalcon/Di/InjectionAwareInterface.zep)


-   __Namespace__

    - `Phalcon\Di`

-   __Uses__
    

-   __Extends__
    

-   __Implements__
    

This interface must be implemented in those classes that uses internally the
Phalcon\Di\Di that creates them


### Methods

```php
public function getDI(): DiInterface;
```
Returns the internal dependency injector


```php
public function setDI( DiInterface $container ): void;
```
Sets the dependency injector




## Di\Service 

[Source on GitHub](https://github.com/phalcon/cphalcon/blob/5.0.x/phalcon/Di/Service.zep)


-   __Namespace__

    - `Phalcon\Di`

-   __Uses__
    
    - `Closure`
    - `Phalcon\Di\Exception\ServiceResolutionException`
    - `Phalcon\Di\Service\Builder`

-   __Extends__
    

-   __Implements__
    
    - `ServiceInterface`

Represents individually a service in the services container

```php
$service = new \Phalcon\Di\Service(
    "request",
    \Phalcon\Http\Request::class
);

$request = service->resolve();
```


### Properties
```php
/**
 * @var mixed
 */
protected definition;

/**
 * @var bool
 */
protected resolved = false;

/**
 * @var bool
 */
protected shared = false;

/**
 * @var mixed|null
 */
protected sharedInstance;

```

### Methods

```php
final public function __construct( mixed $definition, bool $shared = bool );
```
Phalcon\Di\Service


```php
public function getDefinition(): mixed;
```
Returns the service definition


```php
public function getParameter( int $position );
```
Returns a parameter in a specific position


```php
public function isResolved(): bool;
```
Returns true if the service was resolved


```php
public function isShared(): bool;
```
Check whether the service is shared or not


```php
public function resolve( mixed $parameters = null, DiInterface $container = null ): mixed;
```
Resolves the service


```php
public function setDefinition( mixed $definition ): void;
```
Set the service definition


```php
public function setParameter( int $position, array $parameter ): ServiceInterface;
```
Changes a parameter in the definition without resolve the service


```php
public function setShared( bool $shared ): void;
```
Sets if the service is shared or not


```php
public function setSharedInstance( mixed $sharedInstance ): void;
```
Sets/Resets the shared instance related to the service




## Di\Service\Builder 

[Source on GitHub](https://github.com/phalcon/cphalcon/blob/5.0.x/phalcon/Di/Service/Builder.zep)


-   __Namespace__

    - `Phalcon\Di\Service`

-   __Uses__
    
    - `Phalcon\Di\DiInterface`
    - `Phalcon\Di\Exception`

-   __Extends__
    

-   __Implements__
    

Phalcon\Di\Service\Builder

This class builds instances based on complex definitions


### Methods

```php
public function build( DiInterface $container, array $definition, mixed $parameters = null );
```
Builds a service using a complex service definition




## Di\ServiceInterface ![Interface](../assets/images/interface-blue.svg) 

[Source on GitHub](https://github.com/phalcon/cphalcon/blob/5.0.x/phalcon/Di/ServiceInterface.zep)


-   __Namespace__

    - `Phalcon\Di`

-   __Uses__
    

-   __Extends__
    

-   __Implements__
    

Represents a service in the services container


### Methods

```php
public function getDefinition(): mixed;
```
Returns the service definition


```php
public function getParameter( int $position );
```
Returns a parameter in a specific position


```php
public function isResolved(): bool;
```
Returns true if the service was resolved


```php
public function isShared(): bool;
```
Check whether the service is shared or not


```php
public function resolve( mixed $parameters = null, DiInterface $container = null ): mixed;
```
Resolves the service


```php
public function setDefinition( mixed $definition );
```
Set the service definition


```php
public function setParameter( int $position, array $parameter ): ServiceInterface;
```
Changes a parameter in the definition without resolve the service


```php
public function setShared( bool $shared );
```
Sets if the service is shared or not




## Di\ServiceProviderInterface ![Interface](../assets/images/interface-blue.svg) 

[Source on GitHub](https://github.com/phalcon/cphalcon/blob/5.0.x/phalcon/Di/ServiceProviderInterface.zep)


-   __Namespace__

    - `Phalcon\Di`

-   __Uses__
    

-   __Extends__
    

-   __Implements__
    

Should be implemented by service providers, or such components, which
register a service in the service container.

```php
namespace Acme;

use Phalcon\Di\DiInterface;
use Phalcon\Di\ServiceProviderInterface;

class SomeServiceProvider implements ServiceProviderInterface
{
    public function register(DiInterface $di)
    {
        $di->setShared(
            'service',
            function () {
                // ...
            }
        );
    }
}
```


### Methods

```php
public function register( DiInterface $di ): void;
```
Registers a service provider.

