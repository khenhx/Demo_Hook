## inport từ điển - phpMyAdmin
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
    $form['type_multi'] = [
      '#type' => 'checkboxes',
      '#title' => $this->t('Select element'),
      '#options' => [
        'n' => $this->t('Danh từ (n)'),
        'v' => $this->t('Động từ (v)'),
        'adj' => $this->t('Tính từ (adj)'),
        'adv' => $this->t('Trạng từ (adv)'),
      ],
    ];

    $form['type'] = [
      '#type' => 'select',
      '#title' => $this->t('Select element'),
      '#options' => [
        'n' => $this->t('Danh từ (n)'),
        'v' => $this->t('Động từ (v)'),
        'adj' => $this->t('Tính từ (adj)'),
        'adv' => $this->t('Trạng từ (adv)'),
      ],
    ];

    $form['key_word'] = [
      '#type' => 'textfield',
      '#title' => $this->t('từ khóa'),
      '#default_value' => '',
      '#size' => 60,
      '#maxlength' => 128,
    ];

//    $form['csv_file'] = [
//      '#type' => 'managed_file',
//      '#name' => 'csv_file',
//      '#title' => t('File *'),
//      '#size' => 20,
//      '#description' => t('CSV format only'),
//      '#upload_validators' => [
//        'file_validate_extensions' => ['csv'],
//      ],
//      '#upload_location' => 'public://my_files/',
//    ];
    $form['submit'] = [
      '#type' => 'submit',
      '#value' => $this->t('Import từ điển'),
    ];

    return $form;
  }

  /**
   * {@inheritdoc}
   */
  public function submitForm(array &$form, FormStateInterface $form_state) {
    $connection = \Drupal::database();
    $type = $form_state->getValue('type');
    $typeMulti = $form_state->getValue('type_multi');
    $keyWord = $form_state->getValue('key_word');

    $result = $connection->select('a_tu_dien', 't')
      ->fields('t', ['vietnam', 'type'])
      ->condition('type', $type)
      ->condition('vietnam', "%" . $keyWord . "%", 'LIKE')
      ->execute()
      ->fetchAll();

    $resultMulti = $connection->select('a_tu_dien', 't')
      ->fields('t', ['vietnam', 'type'])
      ->condition('type', $typeMulti, 'IN')
      ->condition('vietnam', "%" . $keyWord . "%", 'LIKE')
      ->execute()
      ->fetchAll();
    

    // Chỉ lấy những record là danh từ
    $notAdj = $connection->select('a_tu_dien', 't')
      ->fields('t', ['vietnam', 'type'])
      ->condition('type', 'n', '<>')
      ->execute()
      ->fetchAll();

    // Chỉ lấy những record không phải danh từ, không phải động từ
    $notNAndV = $connection->select('a_tu_dien', 't')
      ->fields('t', ['vietnam', 'type'])
      ->condition('type', ['n', 'v'], 'NOT IN')
      ->execute()
      ->fetchAll();

    $recordBeThua110 = $connection->select('a_tu_dien', 't')
      ->fields('t', ['id', 'vietnam', 'type'])
      ->condition('id', 110, '<=')
      ->execute()
      ->fetchAll();

    $recordBeThua = $connection->select('a_tu_dien', 't')
      ->fields('t', ['id', 'vietnam', 'type'])
      ->condition('id', 110, '<=')
      ->condition('id', 105, '>=')
      ->execute()
      ->fetchAll();

    $recordBeThuaAA = $connection->select('a_tu_dien', 't')
      ->fields('t', ['id', 'vietnam', 'type'])
      ->condition('id', [105, 110], 'NOT BETWEEN')
      ->execute()
      ->fetchAll();

    $getNull = $connection->select('a_tu_dien')
      ->exists('type')
      ->execute()
      ->fetchAll();

    dump($getNull);
    die;

//    $csv_file = $form_state->getValue('csv_file');
//    $file = File::load($csv_file[0]);
//    $data = $this->csvtoarray($file->getFileUri(), ',');
//    $connection = \Drupal::database();
//    foreach ($data as $row) {
//      $connection->insert('a_tu_dien')->fields($row)->execute();
//    }
//    $file->delete();
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
