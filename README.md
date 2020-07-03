
# Symfony + DDD

## Introduction

First of all this is my "way' of introducing DDD in my symfony side project. Pretty sure there is other ways to do it. This is basicaly and step by step of what I did. I was strongly inspired by https://github.com/Nikoms/clean-architecture-example. 
This is just an introduction. You will still have to adapt your code to DDD. I will just try to show you how to adapt symfony !

Symfony used : 5.0.8

PHP used : 7.4.6

## Folders and namespaces
```
\bin
\config
\public
\src
  \Domain
  \Infrastructure 
  # Here symfony controllers, services, entities, etc ...
  \Presentation
\Templates
```
For Infrastructure (Symfony) I will use the namespace ``Infra`` and keep using ``App`` for the core code. 
## Symfony modifications

 - Move ``src*/`` to ``src/Infrastructure``
 - Adapt composer.json : 
```json
  "autoload": {
    "psr-4": {
      "App\\": "src/",
      "Infra\\": "src/Infrastructure"
    }
  },
```
 - In ``src/Infrastructure`` change all namespaces uses and declaration to :
    - ``namespace Infra\* ;``
    - ``use Infra\* ;``
 - Change the Kernel namespace call in ``public/index.php`` and ``bin/console.php`` : ``use Infra\Kernel;``
 - Adapt the function ``getProjectDir()`` in ``src/Infrastructure/Kernel.php`` (may be a better way to do this) : 
 
 ```php
     public function getProjectDir(): string
    {
        return \dirname(__DIR__).'/..';
    }
 ```
 - Modify ``conifg/services.yaml`` : 
 ```yaml
    Infra\:
      resource: '../src/Infrastructure/*'
      exclude: '../src/Infrastructure/{DependencyInjection,Entity,Migrations,Tests,Kernel.php}'

    Infra\Controller\:
        resource: '../src/Infrastructure/Controller'
        tags: ['controller.service_arguments']
 ```
 - If use it adapt your routing file ``conifg/routes.yaml`` :
 ```yaml
     app_xxx:
        path: /
        controller: Infra\Controller\XXXController::index
  ```
 - Modify ``config/routes/annotations.yaml`` :
 ```yaml
    controllers:
        resource: ../../src/Infrastructure/Controller/
        type: annotation

    kernel:
        resource: ../../src/Infrastructure/Kernel.php
        type: annotation
 ```
  - If use it adapt your user prodiver config in``conifg/packages/security.yaml`` :
 ```yaml
    providers:
        users:
            entity:
                class: 'Infra\Entity\User'
                property: 'username'
  ```
  - If use it adapt your doctrine config in``conifg/packages/doctrine.yaml`` :
 ```yaml
    mappings:
        Infra:
            is_bundle: false
            type: annotation
            dir: '%kernel.project_dir%/src/Infrastructure/Entity'
            prefix: 'Infra\Entity'
            alias: Infra
  ```
## What's next  ?

## Sources / Help

https://github.com/Nikoms/clean-architecture-example 

https://www.youtube.com/watch?v=tdE5wE5MvsI ( french )

https://www.youtube.com/watch?v=2H1rdx3al_8 ( french ) 
