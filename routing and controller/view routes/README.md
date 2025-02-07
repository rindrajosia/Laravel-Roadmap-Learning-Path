# View routes
Si votre itinéraire ne doit renvoyer qu’un `Vue`, vous pouvez utiliser la méthode `Route::viewredirectview`. Cette méthode fournit un raccourci simple afin que vous n’ayez pas à définir une route ou un contrôleur complet. La méthode `accepte un URI comme premier argument et un nom de vue comme deuxième argument`. En outre, vous pouvez fournir en troisième argument `un tableau de données` à transmettre à la vue (facultatif)
```php
Route::view('/welcome', 'welcome');
 
Route::view('/welcome', 'welcome', ['name' => 'Taylor']);
```