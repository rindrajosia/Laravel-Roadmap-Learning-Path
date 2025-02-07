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