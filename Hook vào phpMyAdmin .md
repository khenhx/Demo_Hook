## Hook tạo Bảng phpMyAdmin.
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


## Hook tạo File phpMyAdmin.
```php
<?php

namespace Drupal\tu_dien\Form;

use Drupal\Core\Form\FormBase;
use Drupal\Core\Form\FormStateInterface;
use Drupal\file\Entity\File;

class ImportTuDien extends FormBase {

  /**
   * {@inheritdoc}
   */
  public function getFormId() {
    return 'import_tu_dien_form';
  }

  /**
   * {@inheritdoc}
   */
  public function buildForm(array $form, FormStateInterface $form_state) {
    $form['csv_file'] = [
      '#type' => 'managed_file',
      '#name' => 'csv_file',
      '#title' => t('File *'),
      '#size' => 20,
      '#description' => t('CSV format only'),
      '#upload_validators' => [
        'file_validate_extensions' => ['csv'],
      ],
      '#upload_location' => 'public://my_files/',
    ];
    $form['submit'] = [
      '#type' => 'submit',
      '#value' => $this->t('Import từ điển'),
    ];

    return $form;
  }

  /**
   * {@inheritdoc}
   */
   ## Hook đọc file
  public function submitForm(array &$form, FormStateInterface $form_state) {
    $csv_file = $form_state->getValue('csv_file');
    $file = File::load($csv_file[0]);
    $data = $this->csvtoarray($file->getFileUri(), ',');
    $connection = \Drupal::database();
    foreach ($data as $row) {
      $connection->insert('a_tu_dien')->fields($row)->execute();
    }
    $file->delete();
  }

  public function csvtoarray($filename = '', $delimiter) {
    if (!file_exists($filename) || !is_readable($filename)) return FALSE;
    $header = NULL;
    $data = [];
    if (($handle = fopen($filename, 'r')) !== FALSE) {
      while (($row = fgetcsv($handle, 1000, $delimiter)) !== FALSE) {
        if (!$header) {
          $header = $row;
        } else {
          $data[] = array_combine($header, $row);
        }
      }
      fclose($handle);
    }
    return $data;
  }


}
```
