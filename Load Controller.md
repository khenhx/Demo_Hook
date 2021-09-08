## Load tu dien tu phpMayAdmin ra.
```php
<?php

namespace Drupal\tu_dien\Controller;

use Drupal\Core\Controller\ControllerBase;
use Drupal\tu_dien\TuDienRepository;
use Symfony\Component\DependencyInjection\ContainerInterface;

/**
 * Controller for DBTNG Example.
 *
 * @ingroup tu_dien
 */
class TuDienController extends ControllerBase {

  /**
   * The repository for our specialized queries.
   *
   * @var \Drupal\tu_dien\TuDienRepository
   */
  protected $repository;

  /**
   * {@inheritdoc}
   */
  public static function create(ContainerInterface $container) {
    $controller = new static($container->get('tu_dien.repository'));
    $controller->setStringTranslation($container->get('string_translation'));
    return $controller;
  }

  /**
   * Construct a new controller.
   *
   * @param \Drupal\tu_dien\TuDienRepository $repository
   *   The repository service.
   */
  public function __construct(TuDienRepository $repository) {
    $this->repository = $repository;
  }

  /**
   * Render a list of entries in the database.
   */
  public function entryList() {
    $content = [];

    $content['message'] = [
      '#markup' => $this->t('Từ điển việt Anh.'),
    ];

    $rows = [];
    $headers = [
      $this->t('Id'),
      $this->t('Nghĩa tiếng Việt'),
      $this->t('english'),
      $this->t('tuloai'),
    ];

    $entries = $this->repository->load();

    foreach ($entries as $entry) {
      // Sanitize each entry.
      $rows[] = array_map('Drupal\Component\Utility\Html::escape', (array) $entry);
    }
    $content['table'] = [
      '#type' => 'table',
      '#header' => $headers,
      '#rows' => $rows,
      '#empty' => $this->t('No entries available.'),
    ];
    // Don't cache this page.
    $content['#cache']['max-age'] = 0;

    return $content;
  }

}
```
