# Shopify PHP SDK

This SDK was created to enable rapid efiicient development using Shopify's API.

## Installation

```json
{
    "require" : {
        "robby-bugatti/shopify-php-sdk": "1.0.0"
    }
}
```
then install

```shell
composer install
```
## Usage / Examples

Essentially, there are 2 ways to initialize this SDK.

### Authentication

To use the SDK for OAuth purposes, you need to provide your api_key, api_secret, permissions, and redirect_uri

```php
$options = array(
    'api_key' => 'some_random_api_key',
    'api_secret' => 'some_random_api_secret',
    'redirect_uri' => "http://your_app.com/redirect_uri",
    'permissions' => "<permissions your applicaiton requires, comma separated>",
    'store' => "myshopify.domain.com"
);

\Shopify\Shopify::init($options);

if(isset($_GET['code']))
{
    // Redirect to Shopify to start OAuth
    header("Location: ".\Shopify\Auth::authorizationUrl());
} else {
    // We can go ahead and get the access token
    echo \Shopify\Auth::accessToken();
    // This should return something that looks like this:
    // 53e20e750c89274d02b53927135fd664
}
```

Now that we have an access token, we can make authenticated requests to Shopify.
Once you have an Access Token, you only need to provide the token and the store it belongs to

```php
$options = array(
    'access_token' => '53e20e750c89274d02b53927135fd664',
    'store' => 'myshopify.domain.com'
);
\Shopify\Shopify::init($options);
```

You now have access to all the methods the SDK provides!

### Reading

The SDK uses static methods to fetch data from Shopify
```php
// Basic layout
\Shopify\<requested_object>::{method}($params);

// Fetch shop info
\Shopify\Shop::get(); // This doesn't require params, because theres only one store object

// Get all products
\Shopify\Product::all(); // Returns array of Product objects

\Shopify\Order::get(<order_id>); // Return a single order
```

### Creating

To create objects in the Shopify domain, simply set all the attributes you want to put on the object, and call save();

```php
$opts = array(
    'title' => 'Random title',
    'handle' => "Some Product",
    'product_type' => "Application",
);

$product = new \Shopify\Product($opts);
$product->create();
echo $product->id;
// 2178508200
```

### Updating

To update an object through the SDK, just call update() on an instantiated object

```php
// Fetch and update inline
$product = \Shopify\Product::get(2178508200);
$product->title = "A new product title";
$product->update();


// If you know the ID, you can create a *new* object and just call update
$opts = array(
    'id' => 2178508200,
    'title' => "A new product title"
);

$product = new \Shopify\Product($opts);
$product->update();
```

### Deleting

To delete an object, simply call the objects static delete() method, passing the ID
```php
\Shopify\Product::delete(2178508200);
// returns true

```

## Error Handling

The SDK is designed to throw Exceptions when an error is encountered. Wrap calls to Shopify in try / catch statements, and use your desired exception handler.

```php
try {
    $product = new \Shopify\Product(array());
} catch (Exception $e) {
    echo $e->getMessage();
}
// "Title cannot be blank"
```

There are multiple exception types that can be encountered
