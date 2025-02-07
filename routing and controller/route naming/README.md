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