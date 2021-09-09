## Form Delete Id
```php
<?php

namespace Drupal\tu_dien\Form;

use Drupal\Core\Form\FormBase;
use Drupal\Core\Form\FormStateInterface;
use Drupal\tu_dien\TuDienRepository;
use Symfony\Component\DependencyInjection\ContainerInterface;

/**
 * Sample UI to update a record.
 *
 * @ingroup tu_dien
 */
class TuDienDeleteForm extends FormBase
{

  /**
   * Our database repository service.
   *
   * @var \Drupal\tu_dien\TuDienRepository
   */
  protected $repository;

  /**
   * {@inheritdoc}
   */
  public function getFormId()
  {
    return 'tu_dien_delete_form';
  }

  /**
   * {@inheritdoc}
   */
  public static function create(ContainerInterface $container) {
    $form = new static($container->get('tu_dien.repository'));
    $form->setStringTranslation($container->get('string_translation'));
    $form->setMessenger($container->get('messenger'));
    return $form;
  }

  /**
   * Construct the new form object.
   */
  public function __construct(TuDienRepository $repository) {
    $this->repository = $repository;
  }

  /**
   * Sample UI to update a record.
   */
  public function buildForm(array $form, FormStateInterface $form_state) {
    // Wrap the form in a div.
    $form = [
      '#prefix' => '<div id="deleteform">',
      '#suffix' => '</div>',
    ];
    // Query for items to display.
    $entries = $this->repository->load();
    // Tell the user if there is nothing to display.
    if (empty($entries)) {
      $form['no_values'] = [
        '#value' => $this->t('No entries exist in the table tu_dien table.'),
      ];
      return $form;
    }

    $keyed_entries = [];
    $options = [];
    foreach ($entries as $entry) {
      $options[$entry->id] = $this->t('@id: @vietnam @english (@tuloai)', [
        '@id' => $entry->id,
        '@vietnam' => $entry->vietnam,
        '@english' => $entry->english,
        '@tuloai' => $entry->type,
      ]);
      $keyed_entries[$entry->id] = $entry;
    }

    // Grab the pid.
    $id = $form_state->getValue('id');
    // Use the pid to set the default entry for updating.
    $default_entry = !empty($id) ? $keyed_entries[$id] : $entries[0];

    // Save the entries into the $form_state. We do this so the AJAX callback
    // doesn't need to repeat the query.
    $form_state->setValue('entries', $keyed_entries);

    $form['id'] = [
      '#type' => 'select',
      '#options' => $options,
      '#title' => $this->t('Choose entry to update'),
      '#default_value' => $default_entry->id,
    ];

    $form['submit'] = [
      '#type' => 'submit',
      '#value' => $this->t('Delete'),
    ];
    return $form;
  }

  /**
   * {@inheritdoc}
   */
  public function validateForm(array &$form, FormStateInterface $form_state) {}

  /**
   * {@inheritdoc}
   */
  public function submitForm(array &$form, FormStateInterface $form_state) {
    $id = $form_state->getValue('id');
    $count = $this->repository->delete($id);
    $this->messenger()->addMessage($this->t('Delete entry @entry (@count row updated)', [
      '@count' => $count,
      '@entry' => print_r($id, TRUE),
    ]));
  }
}
```
