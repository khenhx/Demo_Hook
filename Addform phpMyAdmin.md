## tao moi tu dien trong phpMyAdmin roi load ra.
```php
<?php

namespace Drupal\tu_dien\Form;

use Drupal\Core\DependencyInjection\ContainerInjectionInterface;
use Drupal\Core\Form\FormInterface;
use Drupal\Core\Form\FormStateInterface;
use Drupal\Core\Messenger\MessengerTrait;
use Drupal\Core\Session\AccountProxyInterface;
use Drupal\Core\StringTranslation\StringTranslationTrait;
use Drupal\tu_dien\TuDienRepository;
use Symfony\Component\DependencyInjection\ContainerInterface;

/**
 * Form to add a database entry, with all the interesting fields.
 *
 * @ingroup tu_dien
 */
class TuDienAddForm implements FormInterface, ContainerInjectionInterface {

  use StringTranslationTrait;
  use MessengerTrait;

  /**
   * Our database repository service.
   *
   * @var \Drupal\tu_dien\TuDienRepository
   */
  protected $repository;

  /**
   * The current user.
   *
   * We'll need this service in order to check if the user is logged in.
   *
   * @var \Drupal\Core\Session\AccountProxyInterface
   */
  protected $currentUser;

  /**
   * {@inheritdoc}
   *
   * We'll use the ContainerInjectionInterface pattern here to inject the
   * current user and also get the string_translation service.
   */
  public static function create(ContainerInterface $container) {
    $form = new static(
      $container->get('tu_dien.repository'),
      $container->get('current_user')
    );
    // The StringTranslationTrait trait manages the string translation service
    // for us. We can inject the service here.
    $form->setStringTranslation($container->get('string_translation'));
    $form->setMessenger($container->get('messenger'));
    return $form;
  }

  /**
   * Construct the new form object.
   */
  public function __construct(TuDienRepository $repository, AccountProxyInterface $current_user) {
    $this->repository = $repository;
    $this->currentUser = $current_user;
  }

  /**
   * {@inheritdoc}
   */
  public function getFormId() {
    return 'tu_dien_add_form';
  }

  /**
   * {@inheritdoc}
   */
  public function buildForm(array $form, FormStateInterface $form_state) {
    $form = [];
    $form['name'] = [
      '#type' => 'textfield',
      '#title' => $this->t('Nghĩa tiếng Việt'),
      '#size' => 15,
    ];
    $form['surname'] = [
      '#type' => 'textfield',
      '#title' => $this->t('English'),
      '#size' => 15,
    ];

    $form['age'] = [
      '#type' => 'textfield',
      '#title' => $this->t('Từ loại'),
      '#size' => 5,
    ];

    $form['submit'] = [
      '#type' => 'submit',
      '#value' => $this->t('Tạo'),
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
    // Gather the current user so the new record has ownership.
    $account = $this->currentUser;
    // Save the submitted entry.
    $entry = [
      'vietnam' => $form_state->getValue('name'),
      'english' => $form_state->getValue('surname'),
      'type' => $form_state->getValue('age'),
      'id' => $account->id(),
    ];
    $return = $this->repository->insert($entry);
    if ($return) {
      $this->messenger()->addMessage($this->t('Created entry @entry', ['@entry' => print_r($entry, TRUE)]));
    }
  }

}
```
