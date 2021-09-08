## file services.yml
```php
# This declares the plugin manager to the service container. For background
# information on the service container, see https://www.drupal.org/node/2133171.
# Changes here require that the cache be cleared in order to have Drupal notice
# them.
services:
  tu_dien.repository:
    class: Drupal\tu_dien\TuDienRepository
    arguments: ['@database', '@string_translation', '@messenger']
```
