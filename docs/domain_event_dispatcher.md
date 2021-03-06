Domain Event Dispatcher
=======================

The domain event dispatcher is a special dispatcher in your application that dispatch only
domain events.


Make a rule
-----------

To make a new rule (its a listener) you should implements the `DomainRuleInterface`.

### Standalone usage

#### Add a standard rule

```php
<?php
use Biig\Component\Domain\Rule\DomainRuleInterface;
use Biig\Component\Domain\Event\DomainEvent;

$dispatcher->addRule(new class implements DomainRuleInterface {
    public function execute(DomainEvent $event) {
        // add some specific behavior
    }
    
    public function on() {
        return 'on.event';
    }
});
```

#### Add a post persist delayed rule

This is useful for post flush processing.

```php
<?php
use Biig\Component\Domain\Event\DomainEvent;
use Biig\Component\Domain\Rule\PostPersistDomainRuleInterface;

$dispatcher->addRule(new class implements PostPersistDomainRuleInterface {
    public function execute(DomainEvent $event) {
        // add some specific behavior
    }
    
    public function after() {
        return 'on.event'; // You have to specify the model
    }
});
```

### Symfony Integration

If you use the Symfony Bundle with auto-configuration of your services.
*You don't have anything to do.*

If you don't want to use the given interface or want more control on the
configuration you still can configure your service by hand:

```yaml
My\Domain\Rule:
    tags:
        - { name: biig_domain.rule, method: 'execute', event: 'on.event', priority: 0 }
```

_Notice: the priority field is optional._


#### Configuration reference

```yaml
biig_domain:
    # It modifies the DoctrineBundle configuration to register a new
    # ClassMetadataInfo class so the instantiator now set the domain event
    # dispatcher to your models automatically
    override_doctrine_instantiator: true
    
    # By default it will override the doctrine instantiator only for
    # the "default" entity manager of your application. You can specify
    # many entity managers if you want.
    entity_managers: []
    
    # Post persist events are not activated by default, you need to enable the post persist listeners
    persist_listeners:
        # As doctrine supports many connections, you need to enable your connections one by one
        doctrine: ['default']
```
