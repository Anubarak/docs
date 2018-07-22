# How to Build a Module

[[toc]]

## Preparation

Before you begin working on a module, you need to decide on a couple things:

- **Namespace** – The root namespace that your module’s classes will live in. (See the [PSR-4](http://www.php-fig.org/psr/psr-4/) autoloading specification for details.) Note that this should *not* begin with `craft\`; use something that identifies you (the developer), or the project.
- **Module ID** – Something that uniquely identifies your plugin within the your project. (Module IDs must begin with a letter and contain only lowercase letters, numbers, and dashes. They should be `kebab-cased`.)

::: warning
When choosing a module ID, try to avoid names that will conflict with Craft’s core [controllers](https://github.com/craftcms/cms/tree/develop/src/controllers) (e.g. `app` would conflict with `AppController.php`), as well as any installed plugin handles. Otherwise bad things will happen.
:::

## Set up the basic file structure

To create a module, create a new directory for it somewhere within your Craft project, such as `modules/<ModuleID>/`. For example, if your module ID is `foo`, you might set it up like this:   

```
my-craft-project.dev/
├── modules/
│   └── foo/
│       └── Module.php
├── templates/
└── ...
```

::: tip
Use [pluginfactory.io](https://pluginfactory.io/) to create your module’s scaffolding with just a few clicks.
:::

## Set up class autoloading

Next up, you need to tell Composer how to find your module’s classes by setting the [`autoload`](https://getcomposer.org/doc/04-schema.md#autoload) field in your project’s `composer.json` file. For example, if your module’s namespace is `foo`, and it’s located at `modules/foo/`, this is what you should add:

```json
{
  // ...
  "autoload": {
    "psr-4": {
      "foo\\": "modules/foo/"
    }
  }
}
```

With that in place, go to your project’s directory in your terminal, and run the following command:

```bash
composer dump-autoload -a
```

That will tell Composer to update its class autoloader script based on your new `autoload` mapping.

## Update the application config

You can add your module to your project’s [application configuration](../config/README.md#application-config) by listing it in the [modules](api:yii\base\Module::modules) and [bootstrap](api:yii\base\Application::bootstrap) arrays. For example, if your module ID is `foo` and its Module class name is `foo\Module`, this is what you should add to `config/app.php`:

```php
return [
    // ...
    'modules' => [
        'foo' => foo\Module::class,
    ],
    'bootstrap' => [
        'foo',
    ],
];
```

::: tip
If your module doesn’t need to get loaded on every request, you can remove its ID from the `bootstrap` array.
:::

## The Module class

The `Module.php` file is your module’s entry point for the system. Its `init()` method is the best place to register event listeners, and any other steps it needs to take to initialize itself.

Use this template as a starting point for your `Module.php` file:

```php
<?php
namespace foo;

class Module extends \yii\base\Module
{
    public function init()
    {
        parent::init();

        // Custom initialization code goes here...
    }
}
```

Replace `foo` with your module’s actual namespace.

## Rendering Module Templates and Translations

Unlike plugins modules work with Yii2 directly thus they don't have the ability to provide their own template path nor their translation files by default. If the module should be able to render its own templates like plugins they need to include their template routes to the `craft\web\View` component using the `View::EVENT_REGISTER_CP_TEMPLATE_ROOTS` event (see [RegisterTemplateRootsEvent](https://docs.craftcms.com/api/v3/craft-web-view.html#event-register-cp-template-roots)

```php
<?php
namespace foo;

use craft\events\RegisterTemplateRootsEvent;
use craft\web\View;

class Module extends \yii\base\Module
{
    public function __construct($id, $parent = null, array $config = [])
    {
        // set an alias to the modules base path
        Craft::setAlias('@foo', $this->getBasePath());

        // inklude the "templates" directory 
        Event::on(
            View::class,
            View::EVENT_REGISTER_CP_TEMPLATE_ROOTS,
            function(RegisterTemplateRootsEvent $e) {
                // define the directory of the templates folder
                if (is_dir($baseDir = $this->getBasePath() . DIRECTORY_SEPARATOR . 'templates')) {
                    // Craft is now able to search for templates 
                    $e->roots[$this->id] = $baseDir;
                }
            }
        );

        // To include a custom translation category 
        $i18n = Craft::$app->getI18n();
        /** @noinspection UnSafeIsSetOverArrayInspection */
        if (!isset($i18n->translations[$id]) && !isset($i18n->translations[$id . '*'])) {
            $i18n->translations[$id] = [
                'class'            => PhpMessageSource::class,
                'sourceLanguage'   => 'en-US',
                'basePath'         => '@foo/translations',
                'forceTranslation' => true,
                'allowOverrides'   => true,
            ];
        }

        static::setInstance($this);
        
        parent::__construct($id, $parent, $config);
    }
}

```

You are now able to translate messages via `{{ 'Hello World'|t('foo') }}` or rendering templates `Craft::$app->getView()->renderTemplate('foo/path/to/template', []);`

## Further Reading

To learn more about modules, see the [Yii documentation](http://www.yiiframework.com/doc-2.0/guide-structure-modules.html).
