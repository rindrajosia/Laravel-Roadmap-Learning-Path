# Introduction
 Les fichiers de modèle de Blade utilisent l’extension de fichier `.blade.php` et sont généralement stockés dans le répertoire `resources/views`.
 Les Blade vues peuvent être renvoyées à partir de routes ou de contrôleurs à l’aide du global helper `view`. 
 Les données peuvent être transmises à la vue Blade à l’aide du deuxième argument de l’helper `view`.
 ```php
Route::get('/', function () {
    return view('greeting', ['name' => 'Finn']);
});
```
## Affichage des données
Vous pouvez afficher les données qui sont transmises à vos vues en enveloppant la variable `entre accolades`. Par exemple, on a l’itinéraire suivant :
```php
Route::get('/', function () {
    return view('welcome', ['name' => 'Samantha']);
});
```
Vous pouvez afficher le contenu de la variable `name` comme suit :
```php
Hello, {{ $name }}.
```
Vous n’êtes pas limité à l’affichage du contenu des variables passées à la vue.  Vous pouvez également afficher le résultats de n’importe quelle fonction PHP. En fait, vous pouvez mettre n’importe quel code PHP que vous souhaitez à l’intérieur d’une instruction d'affichage de Blade.
```php
The current UNIX timestamp is {{ time() }}.
```
## Encodage d’entité HTML
Par défaut, Blade (et la fonction de Laravel) encodera deux fois les entités HTML. Si vous souhaitez désactiver le double encodage, appelez la méthode `Blade::withoutDoubleEncoding` dans la méthode `boot` de `AppServiceProvider`:
```php
<?php
 
namespace App\Providers;
 
use Illuminate\Support\Facades\Blade;
use Illuminate\Support\ServiceProvider;
 
class AppServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     */
    public function boot(): void
    {
        Blade::withoutDoubleEncoding();
    }
}
```
### Affichage des données non échappées
Par défaut, les instructions Blade sont automatiquement envoyées via la fonction `htmlspecialchars` de PHP pour empêcher les attaques XSS. Si vous ne souhaitez pas que vos données soient échappées, vous pouvez utiliser la syntaxe suivante: `{!! !!}`
```php
Hello, {!! $name !!}.
```
## Frameworks Blade et JavaScript
Étant donné que de nombreux frameworks JavaScript utilisent également des accolades pour indiquer qu’une expression donnée doit être affichée dans le navigateur, vous pouvez utiliser le symbole `@` pour informer le moteur de rendu Blade qu’une expression doit rester intacte. Par exemple:
```php
<h1>Laravel</h1>
 
Hello, @{{ name }}.
```
Dans cet exemple, le symbole `@` sera supprimé par Blade ; cependant, l’expression `{{ name }}` ne sera pas touchée par le moteur Blade, ce qui lui permettra d’être rendue par votre framework JavaScript.
Le symbole `@` peut également être utilisé pour échapper aux directives Blade :
```php
{{-- Blade template --}}
@@if()
 
<!-- HTML output -->
@if()
```
### Rendu JSON
Pour initialiser une variable JavaScript en passant un tableau a partir du vue, on peut le passer au format `JSON`.
```php
<script>
    var app = <?php echo json_encode($array); ?>;
</script>
```
Cependant, au lieu d’appeler manuellement , vous pouvez utiliser la methode `Illuminate\Support\Js::from` de `json_encode` et `JSON.parse`. La méthode renverra une instruction JavaScript qui convertira l’objet ou le tableau donné en un objet JavaScript valide.
```php
<script>
    var app = {{ Illuminate\Support\Js::from($array) }};
</script>
```
Les dernières versions du squelette de l’application Laravel incluent la façade `Js`, qui permet d’accéder facilement à cette fonctionnalité dans vos modèles Blade.
```php
<script>
    var app = {{ Js::from($array) }};
</script>
```
### La directive `@verbatim`
Si vous affichez des variables JavaScript dans une grande partie de votre modèle, vous pouvez encapsuler le HTML dans la directive `@verbatim` afin de ne pas avoir à préfixer chaque instruction d'affichage de Blade avec le symbole `@`
```php
@verbatim
    <div class="container">
        Hello, {{ name }}.
    </div>
@endverbatim
```