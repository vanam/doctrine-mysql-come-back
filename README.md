
# KdybyDoctrineMySQLComeBack

Auto reconnect on Doctrine MySql has gone away exceptions on doctrine/dbal >=2.3,<2.6-dev with Kdyby/Connection extension.

# Installation

```console
$ composer require vanam/kdyby-doctrine-mysql-come-back dev-master
```

# Configuration

In order to use KdybyDoctrineMySQLComeBack you have to set `wrapperClass` and `driverClass` connection params.
You can choose how many times Doctrine should be able to reconnect, setting `x_reconnect_attempts` driver option. Its value should be an int.

An example of configuration at connection instantiation time:

```php
use Doctrine\DBAL\Configuration;
use Doctrine\DBAL\DriverManager;

$config = new Configuration();

//..

$connectionParams = array(
    'dbname' => 'mydb',
    'user' => 'user',
    'password' => 'secret',
    'host' => 'localhost',
    // [doctrine-mysql-come-back] settings
    'wrapperClass' => 'Facile\DoctrineMySQLComeBack\Doctrine\DBAL\Connection',
    'driverClass' => 'Facile\DoctrineMySQLComeBack\Doctrine\DBAL\Driver\PDOMySql\Driver',
    'driverOptions' => array(
        'x_reconnect_attempts' => 3
    )
);

$conn = DriverManager::getConnection($connectionParams, $config);

//..
```

An example of neon configuration on Nette projects:

```neon
# config.neon

doctrine:
    driverClass: Facile\DoctrineMySQLComeBack\Doctrine\DBAL\Driver\PDOMySql\Driver
    wrapperClass: Facile\DoctrineMySQLComeBack\Doctrine\DBAL\Connection
    host: %database.host%
    user: %database.user%
    password: %database.password%
    dbname: %database.dbname%
    driverOptions:
        x_reconnect_attempts: 3
    metadata:
        App: %appDir% # namespace with models in Nette app
```
An example of yaml configuration on Symfony 2 projects:

```yaml
# Doctrine example Configuration
doctrine:
    dbal:
        default_connection: %connection_name%
        connections:
            %connection_name%:
                host:     %database_host%
                port:     %database_port%
                dbname:   %database_name%
                user:     %database_user%
                password: %database_password%
                charset:  UTF8
                wrapper_class: 'Facile\DoctrineMySQLComeBack\Doctrine\DBAL\Connection'
                driver_class: 'Facile\DoctrineMySQLComeBack\Doctrine\DBAL\Driver\PDOMySql\Driver'
                options:
                    x_reconnect_attempts: 3
```

# Usage

To force a reconnection try after a long running task you can call
```php
$em->getConnection()->refresh();
```
before performing any other operation different from SELECT.

Instead, in case your next query will be a SELECT, reconnection will be automagically done.

# Thanks

Thanks to Dieter Peeters and his proposal on [DBAL-275](http://www.doctrine-project.org/jira/browse/DBAL-275). Check it out if you are using doctrine/dbal <2.3.
