Dependency Injection
====================

### Simple service

```yml
services:
    acme.my_service:
        class: AcmeBundle\MyService
        arguments: 
            # Inject service
            - '@acme.another_service%' 
            
            # Inject parameter
            - '%acme.my_parameter%'
               
        calls:
            - [ 'setService', [ '@acme.yet_another_service' ] ]
            
        
        # Verbose syntax: see http://symfony.com/blog/new-in-symfony-2-7-dependency-injection-improvements
        calls:
          - method: setLogger
            arguments:
              - "@logger"
          - method: setClass
            arguments:
              - User    
            
        tags:
            - { name: 'acme.tag' }
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<container xmlns="http://symfony.com/schema/dic/services"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://symfony.com/schema/dic/services http://symfony.com/schema/dic/services/services-1.0.xsd">

    <services>
    
        <service id="acme.my_service" class="AcmeBundle\MyService">
            
            <!-- Inject service -->
            <argument type="service" id="acme.another_service" />
            
            <!-- Inject parameter -->
            <argument>%acme.my_parameter%</argument>
            
            <call method="setService">
                <argument type="service" id="acme.yet_another_service" />
            </call>
            <tag name="acme.tag" />
        </service>
        
    </services>
</container>
```

### Aliases

```yml
services:
    acme.my_service:
        class: 'AcmeBundle\MyService'
    acme.my_service_2:
        alias: 'acme.my_service'

    # Shortcut
    acme.my_service_3: '@acme.my_service'
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<container xmlns="http://symfony.com/schema/dic/services"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://symfony.com/schema/dic/services http://symfony.com/schema/dic/services/services-1.0.xsd">

    <services>
    
        <service id="acme.my_service" class="AcmeBundle\MyService" />
        
        <service id="acme.my_service2" alias="acme.my_service" />
        
    </services>

</container>
```

### Auto aliases

```yml
services:
    app.mysql_lock:
        class: AppBundle\Lock\MysqlLock
        public: false
    app.postgresql_lock:
        class: AppBundle\Lock\PostgresqlLock
        public: false
    app.sqlite_lock:
        class: AppBundle\Lock\SqliteLock
        public: false

    app.lock:
        tags:
            - { name: auto_alias, format: "app.%database_type%_lock" }
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<container xmlns="http://symfony.com/schema/dic/services"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://symfony.com/schema/dic/services http://symfony.com/schema/dic/services/services-1.0.xsd">

    <services>
    
        <service id="app.mysql_lock"  class="AppBundle\Lock\MysqlLock"  public="false"/>
        <service id="app.pgsql_lock"  class="AppBundle\Lock\PgsqlLock"  public="false"/>
        <service id="app.sqlite_lock" class="AppBundle\Lock\SqliteLock" public="false"/>
        
        <service id="app.lock">
        
            <tag name="auto_alias" format="app.%database_type%_lock" />
        
        </service>
        
    </services>

</container>
```


### Factories

```yml
services:

    # Factory
    app.newsletter_manager_factory:
        class: 'AppBundle\Email\NewsletterManagerFactory'
        
    app.newsletter_manager1:
        class:   'AppBundle\Email\NewsletterManager'
        # call a static method
        factory: ['AppBundle\Email\NewsletterManager', 'create']

    app.newsletter_manager2:
        class:   'AppBundle\Email\NewsletterManager'
        # call an usual method
        factory: 'app.newsletter_manager_factory:createNewsletterManager'

```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<container xmlns="http://symfony.com/schema/dic/services"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://symfony.com/schema/dic/services http://symfony.com/schema/dic/services/services-1.0.xsd">

    <services>
        
        <!-- Factory -->
        <service id="app.newsletter_manager_factory"
            class="AppBundle\Email\NewsletterManagerFactory"
        />
        
        <service id="app.newsletter_manager1" class="AppBundle\Email\NewsletterManager">
            <!-- call a static method -->
            <factory class="AppBundle\Email\NewsletterManager" method="create" />
        </service>


        <service id="app.newsletter_manager2" class="AppBundle\Email\NewsletterManager">
            <!-- call an usual method -->
            <factory service="app.newsletter_manager_factory"
                method="createNewsletterManager"
            />
        </service>
    </services>
</container>
```


### Event Listeners
```yml
services:
    app.exception_listener:
        class: AppBundle\EventListener\ExceptionListener
        tags:
            - { name: kernel.event_listener, event: kernel.exception }
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<container xmlns="http://symfony.com/schema/dic/services"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://symfony.com/schema/dic/services http://symfony.com/schema/dic/services/services-1.0.xsd">

    <services>
        <service id="app.exception_listener"
            class="AppBundle\EventListener\ExceptionListener">

            <tag name="kernel.event_listener" event="kernel.exception" />
        </service>
    </services>
</container>
```

### Event Subscribers
```yml
services:
    app.exception_subscriber:
        class: AppBundle\EventSubscriber\ExceptionSubscriber
        tags:
            - { name: kernel.event_subscriber }
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<container xmlns="http://symfony.com/schema/dic/services"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://symfony.com/schema/dic/services http://symfony.com/schema/dic/services/services-1.0.xsd">

    <services>
        <service id="app.exception_subscriber"
            class="AppBundle\EventSubscriber\ExceptionSubscriber">

            <tag name="kernel.event_subscriber"/>
        </service>
    </services>
</container>
```    
