## Repository la kho chua cac public function cua cac hook tron phpMyAdmin.
```php
<?php

namespace Drupal\tu_dien;

use Drupal\Core\Database\Connection;
use Drupal\Core\Messenger\MessengerInterface;
use Drupal\Core\Messenger\MessengerTrait;
use Drupal\Core\StringTranslation\StringTranslationTrait;
use Drupal\Core\StringTranslation\TranslationInterface;

/**
 * Class TuDienRepository
 * @package Drupal\tu_dien
 */
class TuDienRepository {

  use MessengerTrait;
  use StringTranslationTrait;

  /**
   * The database connection.
   *
   * @var \Drupal\Core\Database\Connection
   */
  protected $connection;

  /**
   * Construct a repository object.
   *
   * @param \Drupal\Core\Database\Connection $connection
   *   The database connection.
   * @param \Drupal\Core\StringTranslation\TranslationInterface $translation
   *   The translation service.
   * @param \Drupal\Core\Messenger\MessengerInterface $messenger
   *   The messenger service.
   */
  public function __construct(Connection $connection, TranslationInterface $translation, MessengerInterface $messenger) {
    $this->connection = $connection;
    $this->setStringTranslation($translation);
    $this->setMessenger($messenger);
  }

  /**
   * Read from the database using a filter array.
   *
   * @param array $entry
   *   An array containing all the fields used to search the entries in the table.
   * @return object
   *   An object containing the loaded entries if found.
   */
  public function load(array $entry = []) {
    // Read all the fields from the tu_dien table.
    $select = $this->connection
      ->select('a_tu_dien')
      // Add all the fields into our select query.
      ->fields('a_tu_dien');

    // Add each field and value as a condition to this query.
    foreach ($entry as $field => $value) {
      $select->condition($field, $value);
    }
    // Return the result in object format.
    return $select->execute()->fetchAll();
  }

  /**
   * Save an entry in the database.
   *
   * Exception handling is shown in this example. It could be simplified
   * without the try/catch blocks, but since an insert will throw an exception
   * and terminate your application if the exception is not handled, it is best
   * to employ try/catch.
   *
   * @param array $entry
   *   An array containing all the fields of the database record.
   *
   * @return int
   *   The number of updated rows.
   *
   * @throws \Exception
   *   When the database insert fails.
   */
  public function insert(array $entry) {
    try {
      $return_value = $this->connection->insert('a_tu_dien')
        ->fields($entry)
        ->execute();
    }
    catch (\Exception $e) {
      $this->messenger()->addMessage($this->t('Insert failed. Message = %message', [
        '%message' => $e->getMessage(),
      ]), 'error');
    }
    return $return_value ?? NULL;
  }

  /**
   * Update an entry in the database.
   *
   * @param array $entry
   *   An array containing all the fields of the item to be updated.
   *
   * @return int
   *   The number of updated rows.
   */
  public function update(array $entry) {
    try {
      // Connection->update()...->execute() returns the number of rows updated.
      $count = $this->connection->update('a_tu_dien')
        ->fields($entry)
        ->condition('id', $entry['id'])
        ->execute();
    }
    catch (\Exception $e) {
      $this->messenger()->addMessage($this->t('Update failed. Message = %message, query= %query', [
          '%message' => $e->getMessage(),
          '%query' => $e->query_string,
        ]
      ), 'error');
    }
    return $count ?? 0;
  }
}
```
