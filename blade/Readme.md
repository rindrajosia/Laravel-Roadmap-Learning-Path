# Displaying Variables in Blade: Introduction
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
# Blade If-Else
## If Statements
Vous pouvez construire des instructions if à l’aide des directives `@if`, `@elseif`, `@else`et `@endif`. Ces directives fonctionnent de la même manière que leurs homologues PHP.
 ```php
@if (count($records) === 1)
    I have one record!
@elseif (count($records) > 1)
    I have multiple records!
@else
    I don't have any records!
@endif
```
Pour plus de commodité, Blade fournit également une directive: `@unless`
 ```php
@unless (Auth::check())
    You are not signed in.
@endunless
```
En plus des directives conditionnelles déjà discutées, les directives `@isset` et `@empty` peuvent être utilisées comme raccourcis pratiques comme leurs leurs homologues PHP:
 ```php
@isset($records)
    // $records is defined and is not null...
@endisset
 
@empty($records)
    // $records is "empty"...
@endempty
```
## Directives d’authentification
Les directives `@auth` et `@guest` peuvent être utilisées pour déterminer rapidement si l’utilisateur actuel est `Authentifié` ou est un `Invité`
 ```php
@auth
    // The user is authenticated...
@endauth
 
@guest
    // The user is not authenticated...
@endguest
```
## Environment Directives
Vous pouvez vérifier si l’application s’exécute dans l’environnement de production à l’aide de la directive :`@production`
 ```php
@production
    // Production specific content...
@endproduction
```
Vous pouvez également déterminer si l’application s’exécute dans un environnement spécifique à l’aide de la directive :`@env`
 ```php
@env('staging')
    // The application is running in "staging"...
@endenv
 
@env(['staging', 'production'])
    // The application is running in "staging" or "production"...
@endenv
```
## Section Directives
Vous pouvez déterminer si une section d’héritage de modèle a du contenu en utilisant la directive :`@hasSection`
 ```php
@hasSection('navigation')
    <div class="pull-right">
        @yield('navigation')
    </div>
 
    <div class="clearfix"></div>
@endif
```
Vous pouvez utiliser la directive `@sectionMissing` pour déterminer si une section n’a pas de contenu :
 ```php
@sectionMissing('navigation')
    <div class="pull-right">
        @include('default-navigation')
    </div>
@endif
```
## Session Directives
La directive `@session` peut être utilisée pour déterminer si la valeur du `session` existe. Si la valeur de session existe, le contenu du modèle dans les directives `@session` et `@endsession` sera évalué. 
Dans le contenu de la directive  `@session`, vous pouvez afficher la variable `$value` pour afficher la valeur de la session.
 ```php
@session('status')
    <div class="p-4 bg-green-100">
        {{ $value }}
    </div>
@endsession
```
## Switch Statements
Les instructions switch peuvent être construites à l’aide des directives `@switch`, `@case`, `@break`, `@default`et `@endswitch`:
 ```php
@switch($i)
    @case(1)
        First case...
        @break
 
    @case(2)
        Second case...
        @break
 
    @default
        Default case...
@endswitch
```
# Loops
Encore une fois, chacune de directives de structures de boucle de Blade fonctionne de la même manière que ses homologues PHP :
 ```php
@for ($i = 0; $i < 10; $i++)
    The current value is {{ $i }}
@endfor

@foreach ($users as $user)
    <p>This is user {{ $user->id }}</p>
@endforeach

@forelse ($users as $user)
    <li>{{ $user->name }}</li>
@empty
    <p>No users</p>
@endforelse

@while (true)
    <p>I'm looping forever.</p>
@endwhile
```
Lorsque vous utilisez des boucles, vous pouvez également sauter l’itération actuelle ou terminer la boucle en utilisant les directives `@continue` et `@break`
 ```php
@foreach ($users as $user)
    @if ($user->type == 1)
        @continue
    @endif
 
    <li>{{ $user->name }}</li>
 
    @if ($user->number == 5)
        @break
    @endif
@endforeach
```
Vous pouvez également inclure la condition de continuation ou de rupture dans la déclaration de directive :
 ```php
@foreach ($users as $user)
    @continue($user->type == 1)
 
    <li>{{ $user->name }}</li>
 
    @break($user->number == 5)
@endforeach
```
## The Loop Variable
Lors de l’itération dans une boucle `foreach`, la variable `$loop` sera disponible à l’intérieur de votre boucle. Cette variable permet d’accéder à des informations utiles, telles que `l’index` de la boucle actuelle et s’il s’agit de `la première` ou de `la dernière` itération de la boucle :
 ```php
@foreach ($users as $user)
    @if ($loop->first)
        This is the first iteration.
    @endif
 
    @if ($loop->last)
        This is the last iteration.
    @endif
 
    <p>This is user {{ $user->id }}</p>
@endforeach
```
Si vous êtes dans une boucle imbriquée, vous pouvez accéder à la variable de la boucle parent via la propriété `$loop->parent`
 ```php
@foreach ($users as $user)
    @foreach ($user->posts as $post)
        @if ($loop->parent->first)
            This is the first iteration of the parent loop.
        @endif
    @endforeach
@endforeach
```
La variable `$loop` contient également une variété d’autres propriétés utiles :
| Propriété         | Description |
|------------------|------------|
| `$loop->index`       | L’index de l’itération de la boucle actuelle (commence à 0). |
| `$loop->iteration`   | L’itération de la boucle actuelle (commence à 1). |
| `$loop->remaining`   | Les itérations restantes dans la boucle. |
| `$loop->count`       | Nombre total d’éléments du tableau en cours d’itération. |
| `$loop->first`       | S’il s’agit de la première itération de la boucle. |
| `$loop->last`        | S’il s’agit de la dernière itération de la boucle. |
| `$loop->even`        | S’il s’agit d’une itération paire. |
| `$loop->odd`         | S’il s’agit d’une itération impaire. |
| `$loop->depth`       | Niveau d’imbrication de la boucle actuelle. |
| `$loop->parent`      | Lorsqu’il est dans une boucle imbriquée, la variable de boucle du parent. |
# Mises en page à l’aide de l’héritage de modèle
## Définition d’une mise en page
Les mises en page peuvent également être créées via « l’héritage de modèle ». C’était la principale façon de construire des applications avant l’introduction de `Composants`.
Pour commencer, prenons un exemple simple. Tout d’abord, nous allons examiner une mise en page. Étant donné que la plupart des applications Web conservent la même disposition générale sur différentes pages, il est pratique de définir cette disposition comme une seule vue Blade :
 ```php
<!-- resources/views/layouts/app.blade.php -->
 
<html>
    <head>
        <title>App Name - @yield('title')</title>
    </head>
    <body>
        @section('sidebar')
            This is the master sidebar.
        @show
 
        <div class="container">
            @yield('content')
        </div>
    </body>
</html>
```
Comme vous pouvez le voir, ce fichier contient un balisage HTML typique. Cependant, prenez note des directives `@section` et `@yield`. La directive `@section`, comme son nom l’indique, définit `une section de contenu`, tandis que la directive `@yield` est utilisée pour afficher `le contenu d’une section` donnée.
Maintenant que nous avons défini une mise en page pour notre application, définissons une page enfant qui hérite de la mise en page.

## Extending a Layout
Lors de la définition d'une vue enfant, utilisez la directive `@extends` Blade pour spécifier quel layout la vue enfant doit `"hériter"`. Les vues qui étendent un layout Blade peuvent injecter du contenu dans les sections du layout en utilisant des directives `@section`. N'oubliez pas, comme vu dans l'exemple ci-dessus, que le contenu de ces sections sera affiché dans le layout en utilisant : `@yield`
 ```php
<!-- resources/views/child.blade.php -->
 
@extends('layouts.app')
 
@section('title', 'Page Title')
 
@section('sidebar')
    @@parent
 
    <p>This is appended to the master sidebar.</p>
@endsection
 
@section('content')
    <p>This is my body content.</p>
@endsection
```
Dans cet exemple, la section `sidebar` utilise la directive `@@parent` pour ajouter (plutôt que d’écraser) du contenu à la barre latérale de la mise en page. La directive `@@parent` sera remplacée par le contenu de la mise en page lorsque la vue sera rendue.
#### Remarque
Contrairement à l’exemple précédent, la section `sidebar` se termine par `@endsection` au lieu de `@show`. La directive  `@endsection` ne définira qu’une section tandis que `@show` définira et produira (yield == produira) immédiatement la section.

La directive `@yield` accepte également une valeur par défaut comme deuxième paramètre. Cette valeur sera affichée si la section a afficher n’est pas définie
 ```php
@yield('content', 'Default content')
```

## Including Subviews
La directive `@include` de Blade vous permet d’inclure une vue Blade à partir d’une autre vue. Toutes les variables disponibles pour la vue parent seront mises à la disposition de la vue incluse.
 ```php
<div>
    @include('shared.errors')
 
    <form>
        <!-- Form Contents -->
    </form>
</div>
```
Même si la vue incluse hérite de toutes les données disponibles dans la vue parente, vous pouvez également transmettre un tableau de données supplémentaires qui sera mises à la disposition de la vue incluse :
 ```php
@include('view.name', ['status' => 'complete'])
```
Si vous essayez d’accéder à une `vue qui n’existe pas` avec la deirective `@include`, Laravel lancera une erreur. Si vous souhaitez inclure une `vue qui peut être présente ou non`, vous devez utiliser la directive : `@includeIf`
 ```php
@includeIf('view.name', ['status' => 'complete'])
```
Si vous souhaitez inclure un vue si une expression booléenne donnée est évaluée à `true` ou `false`, vous pouvez utiliser les directives `@includeWhen` et `@includeUnless`:
 ```php
@includeWhen($boolean, 'view.name', ['status' => 'complete'])
 
@includeUnless($boolean, 'view.name', ['status' => 'complete'])
```
Pour inclure la première vue qui existe à partir d’un tableau donné de vues, vous pouvez utiliser la directive: `@includeFirst`
 ```php
@includeFirst(['custom.admin', 'admin'], ['status' => 'complete'])
```