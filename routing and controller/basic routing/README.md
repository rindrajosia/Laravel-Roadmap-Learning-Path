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