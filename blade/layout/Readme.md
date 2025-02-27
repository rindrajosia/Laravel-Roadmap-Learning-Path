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