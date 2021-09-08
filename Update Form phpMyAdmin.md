## UpdateForm, sua roi Update vao phpMyAdmin
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
class TuDienUpdateForm extends FormBase {

  /**
   * Our database repository service.
   *
   * @var \Drupal\tu_dien\TuDienRepository
   */
  protected $repository;

  /**
   * {@inheritdoc}
   */
  public function getFormId() {
    return 'tu_dien_update_form';
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
      '#prefix' => '<div id="updateform">',
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
      '#ajax' => [
        'wrapper' => 'updateform',
        'callback' => [$this, 'updateCallback'],
      ],
    ];

    $form['vietnam'] = [
      '#type' => 'textfield',
      '#title' => $this->t('Updated first vietnam'),
      '#size' => 15,
      '#default_value' => $default_entry->vietnam,
    ];

    $form['english'] = [
      '#type' => 'textfield',
      '#title' => $this->t('Updated last english'),
      '#size' => 15,
      '#default_value' => $default_entry->english,
    ];
    $form['type'] = [
      '#type' => 'textfield',
      '#title' => $this->t('Updated tuloai'),
      '#size' => 4,
      '#default_value' => $default_entry->type,
    ];

    $form['submit'] = [
      '#type' => 'submit',
      '#value' => $this->t('Update'),
    ];
    return $form;
  }

  /**
   * AJAX callback handler for the pid select.
   *
   * When the pid changes, populates the defaults from the database in the form.
   */
  public function updateCallback(array $form, FormStateInterface $form_state) {
    // Gather the DB results from $form_state.
    $entries = $form_state->getValue('entries');
    // Use the specific entry for this $form_state.
    $entry = $entries[$form_state->getValue('id')];
    // Setting the #value of items is the only way I was able to figure out
    // to get replaced defaults on these items. #default_value will not do it
    // and shouldn't.
    foreach (['vietnam', 'english', 'type'] as $item) {
      $form[$item]['#value'] = $entry->$item;
    }
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
    $entry = [
      'id' => $form_state->getValue('id'),
      'vietnam' => $form_state->getValue('vietnam'),
      'english' => $form_state->getValue('english'),
      'type' => $form_state->getValue('type'),
    ];
    $count = $this->repository->update($entry);
    $this->messenger()->addMessage($this->t('Updated entry @entry (@count row updated)', [
      '@count' => $count,
      '@entry' => print_r($entry, TRUE),
    ]));
  }
U
}
```
