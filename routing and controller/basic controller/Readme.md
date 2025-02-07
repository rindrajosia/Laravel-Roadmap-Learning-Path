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