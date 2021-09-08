## tu_dien.install
```php
<?php

/**
 * @file
 * Install, update and uninstall functions for the tu_dien module.
 */

/**
 * Implements hook_schema().
 *
 * Defines the database tables used by this module.
 *
 * @see hook_schema()
 *
 * @ingroup tu_dien
 */
function tu_dien_schema() {
  $schema['a_tu_dien'] = [
    'description' => 'Lưu từ điển.',
    'fields' => [
      'id' => [
        'type' => 'serial',
        'not null' => TRUE,
        'description' => 'Primary Key: Unique person ID.',
      ],
      'vietnam' => [
        'type' => 'varchar',
        'length' => 255,
        'not null' => TRUE,
        'default' => '',
        'description' => 'Nghĩa tiếng Việt',
      ],
      'english' => [
        'type' => 'varchar',
        'length' => 255,
        'not null' => TRUE,
        'default' => '',
        'description' => 'Nghĩa tiếng Anh.',
      ],
      'type' => [
        'type' => 'varchar',
        'length' => 255,
        'not null' => TRUE,
        'default' => '',
        'description' => 'Từ loại',
      ],
    ],
    'primary key' => ['id'],
    'indexes' => [
      'vietnam' => ['vietnam'],
      'english' => ['english'],
      'type' => ['type'],
    ],
  ];

  return $schema;
}
```
