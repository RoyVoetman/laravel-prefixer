# Laravel Prefixer

## Introduction
Define a prefix in your controller which is automatically appended to your view- or route-names.

In a resource controller, it is a common pattern to have all the associated views in the same folder, and the same goes for the location of your routes. With this feature, you can define a prefix which is automatically appended to your view-name or route-name. 

[![Latest Version](https://img.shields.io/packagist/v/royvoetman/laravel-prefixer.svg?style=flat-square)](https://packagist.org/packages/royvoetman/laravel-prefixer)
[![MIT Licensed](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](LICENSE)
[![Total Downloads](https://img.shields.io/packagist/dt/royvoetman/laravel-prefixer.svg?style=flat-square)](https://packagist.org/packages/royvoetman/laravel-prefixer)

## Installation

```bash
composer require royvoetman/laravel-prefixer 
```

## View prefixes

When you want to use View prefixes your controller will have to implement the `RoyVoetman\Prefixer\Contracts\ViewPrefix` interface. This interface requires you to add a `viewPrefix` method that returns a `string`.

Second your Controller must include the `RoyVoetman\Prefixer\Http\Traits\CreatesViews` trait. This trait includes the `view(string $view)` method to the controller which handles the prefixing for us. The best practice is to include this trait in your `BaseController` . This method checks if the `CreatesViews` interface is implemented, if this is not the case this method will behave the same as the `view()` global helper function.

```php
namespace App\Http\Controllers;

use Illuminate\Contracts\Support\Renderable;
use RoyVoetman\Prefixer\Http\Traits\CreatesViews;

/**
 * Class BookController
 *
 * @package App\Http\Controllers
 */
class BookController extends Controller implements CreatesViews
{
    /**
     * @return \Illuminate\Contracts\Support\Renderable
     */
    public function create(): Renderable
    {
      	// Return view: `authorized.books.create`
        return $this->view('create');
    }
  
    /**
     * @return \Illuminate\Contracts\Support\Renderable
     */
    public function edit(Book $book): Renderable
    {
      	// You can have chain methods like `with()` just like 
      	// you normally would when using `return view()`
        return $this->view('edit')->with('book', $book);
    }
  
    /**
     * @return string
     */
    public function viewPrefix(): string
    {
        return 'authorized.books';
    }
}
```

## Route prefixes

Route prefixing works the same as View Prefixing except for the following: 

The Controller must implement the `RoyVoetman\Prefixer\Contracts\RoutePrefix` interface and must include the `RoyVoetman\Prefixer\Http\Traits\ForwardsRequests` trait. 

Instead of the `viewPrefix` method, you have to include a `routePrefix` method. And instead of the `view(string $view)` method you have to use the `redirect(string $route)` method. When the `RoutePrefix` method is not implemented this method will behave the same as calling `redirect()->route($route)`. 

> Route prefixes only work if you are using [named routes](https://laravel.com/docs/5.8/routing#named-routes). 

```php
namespace App\Http\Controllers;

use Illuminate\Http\RedirectResponse;
use RoyVoetman\Prefixer\Http\Traits\RoutePrefix;

/**
 * Class BookController
 *
 * @package App\Http\Controllers
 */
class BookController extends Controller implements RoutePrefix
{
    /**
     * @return \Illuminate\Http\RedirectResponse
     */
    public function store(): RedirectResponse
    {
	...        

        // Redirect to: `books.index`
        return $this->redirect('index');
    }
  
    /**
     * @return \Illuminate\Http\RedirectResponse
     */
    public function update(Book $book): RedirectResponse
    {
        ...
          
      	// You can have chain methods like `with()` just like 
      	// you normally would when using `return redirect()`
        return $this->redirect('index')->with('status', 'Book updated');
    }
  
    /**
     * @return string
     */
    public function routePrefix(): string
    {
        return 'books';
    }
}
```

## View and Route prefixes

There is a convenient shortcut when you want to implement the `ViewPrefix` and the `RoutePrefix` interface. You can include the `RoyVoetman\Prefixer\Contracts\ResponsePrefixes` interface which just extends both interfaces.

```php
/**
 * Interface ResponsePrefixes
 *
 * @package App\Interfaces
 */
interface ResponsePrefixes extends RoutePrefix, ViewPrefix
{
    //
}
```

## Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information what has changed recently.

## Contributing

Contributions are **welcome** and will be fully **credited**. We accept contributions via Pull Requests on [Github](https://github.com/RoyVoetman/laravel-prefixer).

### Pull Requests

- **[PSR-2 Coding Standard](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md)** - The easiest way to apply the conventions is to install [PHP Code Sniffer](http://pear.php.net/package/PHP_CodeSniffer).
- **Document any change in behaviour** - Make sure the `README.md` and any other relevant documentation are kept up-to-date.
- **Create feature branches** - Don't ask us to pull from your master branch.
- **One pull request per feature** - If you want to do more than one thing, send multiple pull requests.


## License

The MIT License (MIT). Please see [License File](LICENSE) for more information.
