# Routage de base
Les routes Laravel les plus basiques acceptent un URI et une fermeture, offrant une méthode très simple et expressive pour définir les routes et le comportement sans fichiers de configuration de routage compliqués :
```php
use Illuminate\Support\Facades\Route;
 
Route::get('/greeting', function () {
    return 'Hello World';
});
```
## Les fichiers de routage par défaut
Tous les routes sont definies dans le fichier `routes`.
Les routes définis sont accessibles en saisissant l’URL du route défini dans votre navigateur. Par exemple, vous pouvez accéder au route se trouvant dans `routes/web.php` dans votre navigateur par `http://example.com/user`
```php
use App\Http\Controllers\UserController;
 
Route::get('/user', [UserController::class, 'index']);
```
## API Routes
Si votre application propose également une API sans état, vous pouvez activer le routage de l’API à l’aide de la commande Artisan :`install:api`
```php
php artisan install:api
```
La commande installe `install:api` de `Laravel Sanctum`, fournit une protection d’authentification par jeton d’API robuste mais simple qui peut être utilisée pour authentifier des consommateurs d’API tiers, des SPA ou des applications mobiles. De plus, la commande crée le fichier :`routes/api.php`
```php
Route::get('/user', function (Request $request) {
    return $request->user();
})->middleware('auth:sanctum');
```
Ces routes sont sans état et sont affectées à la fonction `routes/api.php api Groupe middleware`. De plus, le préfixe d’URI est automatiquement appliqué à ces routes, vous n’avez donc pas besoin de l’appliquer manuellement à chaque route dans le fichier. Vous pouvez changer le préfixe en modifiant le fichier de votre application :`/api bootstrap/app.php`
```php
->withRouting(
    api: __DIR__.'/../routes/api.php',
    apiPrefix: 'api/admin',
    // ...
)
```
## Méthodes de routeur disponibles
Le routeur vous permet d’enregistrer des routes qui répondent à n’importe quel verbe HTTP :
```php
Route::get($uri, $callback);
Route::post($uri, $callback);
Route::put($uri, $callback);
Route::patch($uri, $callback);
Route::delete($uri, $callback);
Route::options($uri, $callback);
```
Parfois, vous devrez peut-être enregistrer une route qui répond à plusieurs verbes HTTP. Vous pouvez le faire en utilisant la méthode: `match`. Vous pouvez également enregistrer une route qui répond à tous les verbes HTTP en utilisant la méthode :`any`
```php
Route::match(['get', 'post'], '/', function () {
    // ...
});
 
Route::any('/', function () {
    // ...
});
```
## Injection de dépendances
 Vous pouvez utiliser l'injection de dépendances dans la signature de votre fonction de rappel (callback) pour une route. Cela signifie que vous pouvez spécifier des classes comme paramètres de votre fonction, et Laravel les fournira automatiquement grâce à son `conteneur de services.` Par example, si votre route a besoin d'accéder à la requête HTTP actuelle, vous pouvez typer (type-hint) la classe `Illuminate\Http\Request` dans votre fonction de rappel. Laravel détectera cette dépendance et l'injectera automatiquement.
 ```php
use Illuminate\Http\Request;
 
Route::get('/users', function (Request $request) {
    // ...
});
```
## CSRF Protection
N’oubliez pas que tous les formulaires HTML pointant vers ou les routes définis dans le fichier routes doivent inclure un champ de jeton CSRF. Dans le cas contraire, la demande sera rejetée.
```php
<form method="POST" action="/profile">
    @csrf
    ...
</form>
```
## MRedirect Routes
Si vous définissez une route qui redirige vers une autre URI, vous pouvez utiliser la méthode `Route::redirect`. Cette méthode fournit un raccourci pratique qui vous permet de ne pas avoir à définir une route complète ou un contrôleur pour effectuer une redirection simple
```php
Route::redirect('/here', '/there');
```
Par défaut, cette methode renvoie un `code d’état`. Vous pouvez personnaliser le code d’état à l’aide du troisième paramètre facultatif : `Route::redirect 302`
```php
Route::redirect('/here', '/there', 301);
```
Vous pouvez également utiliser la méthode `Route::permanentRedirect` pour renvoyer le code d'etat `301`
```php
Route::permanentRedirect('/here', '/there');
```
# View routes
Si votre itinéraire ne doit renvoyer qu’un `Vue`, vous pouvez utiliser la méthode `Route::viewredirectview`. Cette méthode fournit un raccourci simple afin que vous n’ayez pas à définir une route ou un contrôleur complet. La méthode `accepte un URI comme premier argument et un nom de vue comme deuxième argument`. En outre, vous pouvez fournir en troisième argument `un tableau de données` à transmettre à la vue (facultatif)
```php
Route::view('/welcome', 'welcome');
 
Route::view('/welcome', 'welcome', ['name' => 'Taylor']);
```
# Basic Controllers
Pour générer rapidement une nouvelle controller, vous pouvez exécuter la commande Artisan `make:controller`. Par défaut, tous les contrôleurs de votre application sont stockés dans le répertoire :`app/Http/Controllers`
```php
php artisan make:controller UserController
```
Jetons un coup d’œil à un exemple de contrôleur de base. Un contrôleur peut avoir n’importe quel nombre de méthodes publiques qui répondront aux requêtes HTTP entrantes.
```php
<?php
 
namespace App\Http\Controllers;
 
use App\Models\User;
use Illuminate\View\View;
 
class UserController extends Controller
{
    /**
     * Show the profile for a given user.
     */
    public function show(string $id): View
    {
        return view('user.profile', [
            'user' => User::findOrFail($id)
        ]);
    }
}
```
Une fois que vous avez écrit une classe et une méthode de contrôleur, vous pouvez définir une route vers la méthode de contrôleur comme suit :
```php
use App\Http\Controllers\UserController;
 
Route::get('/user/{id}', [UserController::class, 'show']);
```
Lorsqu’une requête entrante correspond à l’URI de route spécifié, la méthode de la classe est appelée et les paramètres de route sont transmis à la méthode.`show App\Http\Controllers\UserController`
# Required Parameters
Parfois, vous aurez besoin de capturer des segments de l’URI dans votre itinéraire. Par exemple, vous devrez peut-être capturer l’ID d’un utilisateur à partir de l’URL. Vous pouvez le faire en définissant les paramètres de l’itinéraire :
```php
Route::get('/user/{id}', function (string $id) {
    return 'User '.$id;
});
```
Vous pouvez définir autant de paramètres d’itinéraire que requis par votre itinéraire :
```php
Route::get('/posts/{post}/comments/{comment}', function (string $postId, string $commentId) {
    // ...
});
```
Les paramètres de routage sont toujours encadrés entre `accolades` et doivent être constitués de `caractères alphabétiques`. Les `underscores` sont aussi acceptables dans le nom de parametres. Les paramètres de route sont injectés dans les callbacks / contrôleurs de route en fonction de leur `ordre` - les noms des arguments de callback / controller de route n’ont pas d’importance.
## Parameters and Dependency Injection
Si votre route a des dépendances que vous souhaitez que le conteneur de service Laravel injecte automatiquement dans le callback de votre route, vous devez `lister vos paramètres de route après vos dépendances` :
```php
use Illuminate\Http\Request;
 
Route::get('/user/{id}', function (Request $request, string $id) {
    return 'User '.$id;
});
```
# Optional Parameters
Parfois, vous devrez peut-être spécifier un paramètre de route qui n’est pas toujours présent dans l’URI. Vous pouvez le faire en plaçant `un point d'iterrogation ?` après le nom du paramètre. Assurez-vous de donner à la variable correspondante de l’itinéraire `une valeur par défaut`
```php
Route::get('/user/{name?}', function (?string $name = null) {
    return $name;
});
 
Route::get('/user/{name?}', function (?string $name = 'John') {
    return $name;
});
```
# Regular Expression Constraints
Vous pouvez limiter le format de vos paramètres d’itinéraire à l’aide de la méthode `where` sur une instance de `Route`. La méthode accepte le nom du paramètre et une expression régulière définissant la manière dont le paramètre doit être contraint
```php
Route::get('/user/{name}', function (string $name) {
    // ...
})->where('name', '[A-Za-z]+');
 
Route::get('/user/{id}', function (string $id) {
    // ...
})->where('id', '[0-9]+');
 
Route::get('/user/{id}/{name}', function (string $id, string $name) {
    // ...
})->where(['id' => '[0-9]+', 'name' => '[a-z]+']);
```
Pour plus de commodité, certains modèles d’expressions régulières couramment utilisés ont des méthodes d’assistance qui vous permettent d’ajouter rapidement des contraintes de modèle à vos itinéraires :
```php
Route::get('/user/{id}/{name}', function (string $id, string $name) {
    // ...
})->whereNumber('id')->whereAlpha('name');
 
Route::get('/user/{name}', function (string $name) {
    // ...
})->whereAlphaNumeric('name');
 
Route::get('/user/{id}', function (string $id) {
    // ...
})->whereUuid('id');
 
Route::get('/user/{id}', function (string $id) {
    // ...
})->whereUlid('id');
 
Route::get('/category/{category}', function (string $category) {
    // ...
})->whereIn('category', ['movie', 'song', 'painting']);
 
Route::get('/category/{category}', function (string $category) {
    // ...
})->whereIn('category', CategoryEnum::cases());
```
Si la requête entrante ne correspond pas aux contraintes du modèle de route, `une réponse HTTP 404 sera renvoyée`.
## Global Constraints
Si vous souhaitez qu’un paramètre de route soit toujours contraint par une expression régulière donnée, vous pouvez utiliser la méthode `pattern` en definissant ces modèles dans la méthode `boot` de la classe `App\Providers\AppServiceProvider`
```php
use Illuminate\Support\Facades\Route;
 
/**
 * Bootstrap any application services.
 */
public function boot(): void
{
    Route::pattern('id', '[0-9]+');
}
```
Une fois le motif défini, il est automatiquement appliqué à tous les itinéraires en utilisant le nom de paramètre suivant :
```php
Route::get('/user/{id}', function (string $id) {
    // Only executed if {id} is numeric...
});
```
## Encoded Forward Slashes
Dans Laravel, lorsqu'une route contient des paramètres dynamiques, comme {id} ou {slug}, tous les caractères sont autorisés sauf certains caractères spéciaux, comme / (barre oblique).
Cela signifie que, par défaut, Laravel ne permet pas d'avoir certains caractères dans les valeurs des paramètres de route.
Si vous voulez qu'un paramètre contienne des caractères spécifiques (exemple : uniquement des nombres, ou inclure un - ou un _), vous devez utiliser une contrainte avec une expression régulière en utilisant la méthode `where()`.
```php
Route::get('/search/{search}', function (string $search) {
    return $search;
})->where('search', '.*');
```
# Named Routes
Les routes nommées permettent de générer facilement des URL ou des redirections pour des routes spécifiques. Vous pouvez spécifier un nom pour une route en enchaînant la méthode `name` sur la définition de la route
```php
Route::get('/user/profile', function () {
    // ...
})->name('profile');
```
Vous pouvez également spécifier des noms de route pour les actions du contrôleur
```php
Route::get(
    '/user/profile',
    [UserProfileController::class, 'show']
)->name('profile');
```
## Generating URLs to Named Routes
Une fois que vous avez attribué un nom à une route donnée, vous pouvez utiliser le nom de la route lors de la génération d’URL ou de redirections via les fonctions helper de Laravel `route redirect`
```php
// Generating URLs...
$url = route('profile');
 
// Generating Redirects...
return redirect()->route('profile');
 
return to_route('profile');
```
Si l’itinéraire nommé définit des paramètres, vous pouvez passer les paramètres en tant que `deuxième argument` à la fonction `route`. Les paramètres donnés seront automatiquement insérés dans l’URL générée à leur position correcte 
```php
Route::get('/user/{id}/profile', function (string $id) {
    // ...
})->name('profile');
 
$url = route('profile', ['id' => 1]);
```
Si vous passez des paramètres supplémentaires dans le tableau, ces paires clé/valeur seront automatiquement ajoutées à la chaîne de requête de l’URL générée :
```php
Route::get('/user/{id}/profile', function (string $id) {
    // ...
})->name('profile');
 
$url = route('profile', ['id' => 1, 'photos' => 'yes']);
 
// /user/1/profile?photos=yes
```
## Inspecting the Current Route
Si vous souhaitez déterminer si la demande actuelle a été acheminée vers une route nommée donnée, vous pouvez utiliser la méthode `named` sur une instance de route. Par exemple, vous pouvez vérifier le nom actuel de la route à partir d’un middleware de route
```php
use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;
 
/**
 * Handle an incoming request.
 *
 * @param  \Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response)  $next
 */
public function handle(Request $request, Closure $next): Response
{
    if ($request->route()->named('profile')) {
        // ...
    }
 
    return $next($request);
}
```
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