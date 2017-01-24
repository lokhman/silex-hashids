# silex-hashids
[Hashids](http://hashids.org/php/) Service provider for [**Silex 2.0+**](http://silex.sensiolabs.org/) micro-framework.

> This project is a part of [`silex-tools`](https://github.com/lokhman/silex-tools) library.

## <a name="installation"></a>Installation
You can install `silex-hashids` with [Composer](http://getcomposer.org):

    composer require lokhman/silex-hashids

## <a name="documentation"></a>Documentation
Enable `HashidsServiceProvider` from `Provider` namespace to support brilliant
[Hashids library](http://hashids.org/php/). You may setup either a single or multiple profiles to use in your
application.

    use Lokhman\Silex\Provider\HashidsServiceProvider;

    $app->register(new HashidsServiceProvider(), [
        // default options for all profiles
        'hashids.options' => [
            'salt' => '',
            'min_length' => 0,
            'alphabet' => 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ1234567890',
        ],
        // optionally set multiple profiles
        'hashids.profiles' => [
            'profile1' => [
                'min_length' => 8,
            ],
            'profile2' => [
                'salt' => 'this is my salt',
                'alphabet' => 'abcdefghijklmnopqrstuvwxyz',
            ],
        ],
    ]);

    // single profile encoding
    $hash = $app['hashids']->encode(1, 2, 3);
    $app['hashids']->encode(1, 2, 3) === $hash;
    $app->hashids([1, 2, 3]) === $hash;

    // single profile decoding
    $id = $app['hashids']->decode($hash);
    $app['hashids']->decode($hash) === $id;
    $app->hashids($hash) === $id;

    // multiple profiles encoding
    $hash = $app['hashids']['profile1']->encode(1, 2, 3);
    $app['hashids:profile1']->encode(1, 2, 3) === $hash;
    $app->hashids([1, 2, 3], 'profile1') === $hash;

    // multiple profiles decoding
    $id = $app['hashids']['profile1']->decode($hash);
    $app['hashids:profile1']->decode($hash) === $id;
    $app->hashids($hash, 'profile1') === $id;

**N.B.:** To use `hashids()` application method, include `HashidsTrait` to your `Application` class. This method returns
`FALSE` on error, and if given hash contains a single encoded `id`, integer is returned, otherwise array.

Additionally, the service provider adds support for auto-conversion of hashed parameters in routes if you specify them
with `__hashids_` prefix.

    // GET /users/jR (single profile)
    $app->get('/users/{__hashids_id}', function(Application $app, $id) {
        return $app->json(['id' => $id]);  // { "id": 1 }
    });

    // GET /users/olejRejN (multiple profiles)
    $app->get('/users/{__hashids_profile1_id}', function(Application $app, $id) {
        return $app->json(['id' => $id]);  // { "id": 1 }
    });

## <a name="license"></a>License
Library is available under the MIT license. The included LICENSE file describes this in detail.
