## PHPTemplate to Twig

### Converting `theme()` functions to Twig templates

![](../img/icons/carrot.png)

- [Convert core theme functions to Twig templates](https://www.drupal.org/node/1757550)

All of the `theme()` functions are deprecated in Drupal 8. They are all converted into twig template files.

> **Spoiler alert:** Not all of the `theme()` functions have been converted, *yet*. There are still couple of them around. Most of them are functions used to generate markup for the admin interface.

### Removing the template process layer

![](../img/icons/carrot.png)

- [Remove the process layer (hook_process and hook_process_HOOK)](https://www.drupal.org/node/1843650)
- [The template process layer has been removed](https://www.drupal.org/node/2038981)

The process layer was created to flatten complicated data structures - such as objects or arrays - into strings. In practice, render arrays themselves allow for late rendering via theme function or template file (and can be manipulated in other preprocess functions), and objects can implement `__toString` methods for printing. There is no longer a need for this whole extra layer of processing before rendering.

Since the process layer got removed, the only layer between the data and the template is the *preprocess* layer.

### Theme suggestion hooks

This hook allows any module or theme to provide alternative theme function or template name suggestions and reorder or remove suggestions provided by `hook_theme_suggestions_HOOK()` or by earlier invocations of this hook.

These theme suggestions will show up in the Twig debug output (when enabled). This allows to create a more complex logic to decide which template file should be used to render the data.

> Don't get confused with the double `hook`. The first hook stands for the name of the theme or module, the second stands for the template.

**Drupal 7:**

    <?php
    /**
    * Implements hook_preprocess_HOOK() for node templates.
    */
    function MYTHEME_preprocess_node(&$variables) {
      $variables['theme_hook_suggestions'][] = 'node__' . 'first';
      $variables['theme_hook_suggestions'][] = 'node__' . 'second';
    }

**Drupal 8:**

    <?php
    /**
    * Implements hook_theme_suggestions_HOOK_alter() for node templates.
    */
    function MYTHEME_theme_suggestions_node_alter(array &$suggestions, array $variables) {
      $suggestions[] = 'node__' . 'first';
      $suggestions[] = 'node__' . 'second';
    }

![](../img/icons/pensil.png)

To add a template file suggestion for the `page` hook, you would implement `hook_theme_suggestions_page_alter()`.

***
