# Route Groups
Les groupes de routes en Laravel permettent d'appliquer des attributs communs (comme middleware, préfixes d'URL, espaces de noms, etc.) à plusieurs routes sans avoir à répéter ces paramètres pour chaque route.

Cela rend le code plus lisible, plus organisé et plus facile à maintenir.
## Middleware
Pour attribuer un middleware pour toutes les routes d’un groupe, vous pouvez utiliser la méthode `middleware` avant de définir le groupe. Les middlewares sont exécutés dans l’ordre dans lequel ils sont répertoriés dans le tableau.
```php
Route::middleware(['first', 'second'])->group(function () {
    Route::get('/', function () {
        // Uses first & second middleware...
    });
 
    Route::get('/user/profile', function () {
        // Uses first & second middleware...
    });
});
```
## Controllers
Si un groupe de routes utilise toutes les mêmes contrôleur, vous pouvez utiliser la méthode `controller` pour définir le contrôleur commun pour toutes les routes au sein du groupe. Ensuite, lors de la définition des routes, vous devez uniquement fournir la méthode de contrôleur qu’ils appellent
```php
use App\Http\Controllers\OrderController;
 
Route::controller(OrderController::class)->group(function () {
    Route::get('/orders/{id}', 'show');
    Route::post('/orders', 'store');
});
```
## Subdomain Routing
Les groupes de routes peuvent également être utilisés pour gérer le routage de sous-domaine. Les sous-domaines peuvent se voir attribuer des paramètres de route, tout comme des URI de route, ce qui vous permet de capturer une partie du sous-domaine pour l’utiliser dans votre route ou votre contrôleur. Le sous-domaine peut être spécifié en appelant la méthode `domain` avant de définir le groupe
```php
Route::domain('{account}.example.com')->group(function () {
    Route::get('/user/{id}', function (string $account, string $id) {
        // ...
    });
});
```
## Route Prefixes
La méthode `prefix` peut être utilisée pour préfixer chaque route du groupe avec une URI donnée
```php
Route::prefix('admin')->group(function () {
    Route::get('/users', function () {
        // Matches The "/admin/users" URL
    });
});
```
## Route Name Prefixes
La méthode `name` peut être utilisée pour préfixer chaque nom de route dans le groupe avec une chaîne donnée.
```php
Route::name('admin.')->group(function () {
    Route::get('/users', function () {
        // Route assigned name "admin.users"...
    })->name('users');
});
```
## Summary
A basic route group in Laravel is created using Route::group(), as shown below
```php
Route::group([
  // All parameters go here
], function () {
  // All routes go here
});
```
The method takes in two parameters, an array, and a callback. The array contains all shared features of the routes in the group. An example can be seen below.
```php
Route::group([
  'middleware' => 'auth',
  'namespace' => 'Admin',
  'prefix' => '/admin',
  'as' => 'admin.',
], function () {
  // All routes go here
});
```
Here, all the routes in this group are prefixed with the /admin path and also prefixed with the admin name. The callback contains all routes in the group. So, if a route is defined like this:
```php
Route::group([
  'prefix' => '/admin',
  'as' => 'admin.',
], function () {
    Route::get('/', [AdminController::class, 'index'])->name('index');
});
```
The route actually refers to the /admin/ path and can be accessed with the name “admin.index”. For as many routes as you can include in the group, each one inherits these rules.