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