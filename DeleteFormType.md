## Delete form theo $type
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
class TuDienDeleteTypeForm extends FormBase
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
    return 'tu_dien_delete_type_form';
  }

  /**
   * {@inheritdoc}
   */
  public static function create(ContainerInterface $container)
  {
    $form = new static($container->get('tu_dien.repository'));
    $form->setStringTranslation($container->get('string_translation'));
    $form->setMessenger($container->get('messenger'));
    return $form;
  }

  /**
   * Construct the new form object.
   */
  public function __construct(TuDienRepository $repository)
  {
    $this->repository = $repository;
  }

  /**
   * Sample UI to update a record.
   */
  public function buildForm(array $form, FormStateInterface $form_state)
  {
    // Wrap the form in a div.
    $form = [
      '#prefix' => '<div id="deletetypeform">',
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
      $options[$entry->type] = $this->t('@tuloai', [
        '@tuloai' => $entry->type,
      ]);
      $keyed_entries[$entry->type] = $entry;
    }

    // Grab the pid.
    $type = $form_state->getValue('type');
    // Use the pid to set the default entry for updating.
    $default_entry = !empty($type) ? $keyed_entries[$type] : $entries[0];

    // Save the entries into the $form_state. We do this so the AJAX callback
    // doesn't need to repeat the query.
    $form_state->setValue('entries', $keyed_entries);
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

    $form['submit'] = [
      '#type' => 'submit',
      '#value' => $this->t('Delete Type'),
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
    $entry = [
      'type' => $form_state->getValue('type'),
    ];
    $count = $this->repository->deleteType($entry);
    $this->messenger()->addMessage($this->t('Delete type entry @entry (@count row updated)', [
      '@count' => $count,
      '@entry' => print_r($entry, TRUE),
    ]));
  }
}
```
