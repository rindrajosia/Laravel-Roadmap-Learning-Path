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
