# Mapathe Core PHP - Documentation

## Vue d'ensemble
**Mapathe Core** est une bibliothèque PHP moderne qui fournit les composants fondamentaux pour développer des applications web avec injection de dépendances, routage et gestion de sessions.

## Installation
```bash
composer require mapathe/core
```

## Architecture

### Container d'Injection de Dépendances
Le `Container` gère automatiquement les dépendances avec support des singletons.

```php
$container = Container::getInstance();

// Lier une classe
$container->bind(Interface::class, Implementation::class);

// Singleton
$container->singleton(Service::class);

// Résoudre
$service = $container->resolve(Service::class);
```

### Application Principale
La classe `App` centralise l'accès aux services.

```php
// Initialiser l'application
App::initialize();

// Récupérer un service
$controller = App::get('CitoyenController');
$service = App::get(\App\Service\CitoyenService::class);
```

### Routage
Le `Router` gère les routes avec support des paramètres et middlewares.

```php
// Dans routes/route.web.php
return [
    [
        'method' => 'GET',
        'path' => '/users/{id}',
        'controller' => 'CitoyenController',
        'action' => 'show',
        'middlewares' => ['auth']
    ],
    [
        'method' => 'POST',
        'path' => '/login',
        'action' => function() {
            echo "Connexion";
        }
    ]
];

// Résoudre la route courante
Router::resolve();
```

### Configuration des Services
Les services sont configurés via `config/services.yml` :

```yaml
services:
  repositories:
    CitoyenRepository:
      class: \App\Repository\CitoyenRepository
      singleton: true
      
  services:
    CitoyenService:
      class: \App\Service\CitoyenService
      singleton: true
      dependencies: ['CitoyenRepository']
      
  controllers:
    CitoyenController:
      class: \App\Controller\CitoyenController
      dependencies: ['CitoyenService']
```

## Classes Abstraites

### AbstractController
Base pour tous les contrôleurs avec méthodes CRUD et réponses JSON.

```php
class CitoyenController extends AbstractController
{
    public function index() {
        $data = ['users' => []];
        $this->renderJson($data, "success", 200, "Liste récupérée");
    }
}
```

### AbstractEntity
Base pour les entités avec sérialisation.

```php
class User extends AbstractEntity
{
    public static function toObject(array $data): static {
        // Conversion array vers objet
    }
    
    public function toArray(): array {
        // Conversion objet vers array
    }
}
```

### AbstractRepository
Base pour les repositories avec connexion PDO.

```php
class UserRepository extends AbstractRepository
{
    public function findAll() {
        $stmt = $this->pdo->query("SELECT * FROM users");
        return $stmt->fetchAll();
    }
}
```

## Gestion des Sessions
```php
$session = Session::getInstance();

$session->set('user', $userData);
$user = Session::get('user');
Session::requireAuth(); // Middleware auth
```

## Base de Données
Configuration automatique via variables d'environnement :

```env
DB_DRIVER=pgsql
DB_HOST=localhost
DB_PORT=5433
DB_NAME=pgdbDaf
DB_USER=pguserDaf
DB_PASSWORD=pgpassword
```

## Middlewares Intégrés

- **auth** : Vérifie l'authentification utilisateur
- **guest** : Redirige les utilisateurs connectés

## Exemple Complet

```php
// Structure du projet
/src
  /core           # Classes du framework
  /config
    services.yml  # Configuration des services
/routes
  route.web.php   # Définition des routes

// Bootstrap
require 'vendor/autoload.php';
App::initialize();
Router::resolve();
```

## Fonctionnalités Clés

✅ **Injection de dépendances** automatique  
✅ **Routage** avec paramètres dynamiques  
✅ **Middlewares** d'authentification  
✅ **Sessions** sécurisées  
✅ **ORM** simple avec PDO  
✅ **Configuration** YAML  
✅ **Réponses JSON** standardisées  

Cette bibliothèque offre une base solide pour développer rapidement des applications PHP modernes avec une architecture propre et maintenable.