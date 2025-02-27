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