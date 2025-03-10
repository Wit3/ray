---
title: Laravel
weight: 3
---

Inside a Laravel application, you can use all methods from [the framework agnostic version](/docs/ray/v1/usage/framework-agnostic-php-project).

Additionally, you can use these Laravel specific methods.

### Showing queries

You can display all queries that are executed by calling `showQueries` (or `queries`).

```php
ray()->showQueries();

User::firstWhere('email', 'john@example.com'); // this query will be displayed in Ray.
```

![screenshot](/docs/ray/v1/images/query.jpg)

To stop showing queries, call `stopShowingQueries`.

```php
ray()->showQueries();

User::firstWhere('email', 'john@example.com'); // this query will be displayed.

ray()->stopShowingQueries();

User::firstWhere('email', 'jane@example.com'); // this query won't be displayed.
```

Alternatively, you can pass a callable to `showQueries`. Only the queries performed inside that callable will be displayed in Ray.

```php
User::all(); // this query won't be displayed.

ray()->showQueries(function() {
    User::all(); // this query will be displayed.
});

User::all(); // this query won't be displayed.
```

### Counting queries

If you're interested in how many queries a given piece of code executes, and what the runtime of those queries is, you can use `countQueries`. It expects you to pass a closure in which all the executed queries will be counted.

```php
ray()->countQueries(function() {
    User::all();
    User::all();
    User::all();
});
```

![screenshot](/docs/ray/v1/images/query-count.png)

### Manually showing a query

You can manually send a query to Ray by calling `ray()` on a query. 

```php
User::query()
    ->where('email', 'john@example.com')
    ->ray()
    ->first();
```

![screenshot](/docs/ray/v1/images/single-query.png)

You can call `ray()` multiple times to see how a query is being built up.

```php
User::query()
    ->where('first_name', 'John')
    ->ray()
    ->where('last_name', 'Doe')
    ->ray()
    ->first();
```

![screenshot](/docs/ray/v1/images/single-query-multiple-calls.png)

### Showing duplicate queries

You can display all duplicate queries by calling `showDuplicateQueries`.

```php
ray()->showDuplicateQueries();

User::firstWhere('email', 'john@example.com'); // this query won't be displayed in Ray
User::firstWhere('email', 'john@example.com'); // this query will be displayed in Ray.
```

To stop showing duplicate queries, call `stopShowingDuplicateQueries`.

Alternatively, you can pass a callable to `showDuplicateQueries`. Only the duplicate queries performed inside that callable will be displayed in Ray.

```php
User::all();
User::all(); // this query won't be displayed.

ray()->showDuplicateQueries(function() {
    User::where('id', 1)->get('id');
    User::where('id', 1)->get('id'); // this query will be displayed.
});

User::all();
User::all(); // this query won't be displayed.
```

### Showing slow queries

You can display all queries that took longer than a specified number of milliseconds to execute by calling `showSlowQueries`.

```php
ray()->showSlowQueries(100);

// this query will only be displayed in Ray if it takes longer than 100ms to execute.
User::firstWhere('email', 'john@example.com');
```

Alternatively, you can also pass a callable to `showSlowQueries`. Only the slow queries performed inside that callable will be displayed in Ray.

```php
User::all();
User::all(); // this query won't be displayed.

ray()->showSlowQueries(100, function() {
    User::where('id', 1)->get('id'); // this query will be displayed if it takes longer than 100ms.
});
```

You can also use the shorthand method, `slowQueries()`:

```php
ray()->slowQueries(); // equivalent to calling 'showSlowQueries'.
```

To stop showing slow queries, call `stopShowingSlowQueries`.

### Showing events

You can display all events that are executed by calling `showEvents` (or `events`).

```php
ray()->showEvents();

event(new TestEvent());

event(new TestEventWithParameter('my argument'));
```

![screenshot](/docs/ray/v1/images/event.jpg)

To stop showing events, call `stopShowingEvents`.

```php
ray()->showEvents();

event(new MyEvent()); // this event will be displayed

ray()->stopShowingEvents();

event(new MyOtherEvent()); // this event won't be displayed.
```

Alternatively, you can pass a callable to `showEvents`. Only the events fired inside that callable will be displayed in Ray.

```php
event(new MyEvent()); // this event won't be displayed.

ray()->showEvents(function() {
    event(new MyEvent()); // this event will be displayed.
});

event(new MyEvent()); // this event won't be displayed.
```

### Showing jobs

You can display all jobs that are executed by calling `showJobs` (or `jobs`).

```php
ray()->showJobs();

dispatch(new TestJob('my-test-job'));

```

![screenshot](/docs/ray/v1/images/job.png)

To stop showing jobs, call `stopShowingJobs`.

```php
ray()->showJobs();

dispatch(new TestJob()); // this job will be displayed

ray()->stopShowingJobs();

dispatch(new MyTestOtherJob()); // this job won't be displayed.
```

Alternatively, you can pass a callable to `showJobs`. Only the jobs dispatch inside that callable will be displayed in Ray.

```php
event(new TestJob()); // this job won't be displayed.

ray()->showJobs(function() {
    dispatch(new TestJob()); // this job will be displayed.
});

event(new TestJob()); // this job won't be displayed.
```

### Showing cache events

You can display all cache events using `showCache`

```php
ray()->showCache();

Cache::put('my-key', ['a' => 1]);

Cache::get('my-key');

Cache::get('another-key');
```

![screenshot](/docs/ray/v1/images/cache.png)

To stop showing cache events, call `stopShowingCache`.

### Showing http client requests

You can display all http client requests and responses using `showHttpClientRequests`

```php
ray()->showHttpClientRequests();

Http::get('https://example.com/api/users');
```

![screenshot](/docs/ray/v1/images/http.png)

To stop showing http client events, call `stopShowingHttpClientRequests`.

Alternatively, you can pass a callable to `showHttpClientRequests`. Only the http requests made inside that callable will be displayed in Ray.

```php
Http::get('https://example.com'); // this request won't be displayed.

ray()->showHttpClientRequests(function() {
    Http::get('https://example.com'); // this request will be displayed.
});

Http::get('https://example.com'); // this request won't be displayed.
```

### Handling models

Using the `model` function, you can display the attributes and relations of a model.

```php
ray()->model($user);
```

![screenshot](/docs/ray/v1/images/model.jpg)

The `model` function can also accept multiple models and even collections.

```php
// all of these models will be displayed in Ray
ray()->model($user, $anotherUser, $yetAnotherUser);

// all models in the collection will be display
ray()->model(User::all());

// all models in all collections will be displayed
ray()->model(User::all(), OtherModel::all());
```

Alternatively, you can use `models()` which is an alias for `model()`.

### Displaying mailables

Mails that are sent to the log mailer are automatically shown in Ray, you can also display the rendered version of a specific mailable in Ray by passing a mailable to the `mailable` function.

```php
ray()->mailable(new TestMailable());
```

![screenshot](/docs/ray/v1/images/mailable.jpg)

### Showing which views are rendered

You can display all views that are rendered by calling `showViews`.

```php
ray()->showViews();

// typically you'll do this in a controller
view('welcome', ['name' => 'John Doe'])->render();
```

![screenshot](/docs/ray/v1/images/views.png)

To stop showing views, call `stopShowingViews`.

### Displaying markdown

View the rendered version of a markdown string in Ray by calling the `markdown` function.

```php
ray()->markdown('# Hello World');
```

### Displaying collections

Ray will automatically register a `ray` collection macro to easily send collections to ray.

```php
collect(['a', 'b', 'c'])
    ->ray('original collection') // displays the original collection
    ->map(fn(string $letter) => strtoupper($letter))
    ->ray('uppercased collection'); // displays the modified collection
```

![screenshot](/docs/ray/v1/images/collection.jpg)

### Usage with a `Stringable`

Ray will automatically register a `ray` macro to `Stringable` to easily send `Stringable`s to Ray.

```php
Str::of('Lorem')
   ->append(' Ipsum')
   ->ray()
   ->append(' Dolor Sit Amen');
```

![screenshot](/docs/ray/v1/images/stringable.jpg)


### Displaying environment variables

You can use the `env()` method to display all environment variables as loaded from your `.env` file.  You may optionally pass an array of variable names to exclusively display.

```php
ray()->env();

ray()->env(['APP_NAME', 'DB_DATABASE', 'DB_HOSTNAME', 'DB_PORT']);
```

### Using Ray in Blade views

You can use the `@ray` directive to easily send variables to Ray from inside a Blade view. You can pass as many things as you'd like.

```blade
{{-- inside a view --}}

@ray($variable, $anotherVariables)
```

### Using Ray with test responses

When testing responses, you can send a `TestResponse` to Ray using the `ray()` method. 

`ray()` is chainable, so you can chain on any of Laravel's assertion methods.

```php
// somewhere in your app
Route::get('api/my-endpoint', function () {
    return response()->json(['a' => 1]);
});

// somewhere in a test
/** test */
public function my_endpoint_works_correctly() 
{
    $this
        ->get('api/my-endpoint')
        ->ray()
        ->assertSuccessful();
}
```

![screenshot](/docs/ray/v1/images/response.png)

### Displaying requests

To display all requests made in your Laravel app in Ray, you can call `ray()->showRequests()`. A typical place to put this would be in a service provider.

![screenshot](/docs/ray/v1/images/request.png)

To enable this behaviour by default, you can set the `send_requests_to_ray` option in [the config file](https://spatie.be/docs/ray/v1/configuration/laravel) to `true`.

