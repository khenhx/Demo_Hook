## Demo_hook_php <=> Demo_hook_module (Liên Kết với nhau).
## Demo_hook_php 
```php
<?php

namespace Drupal\custom_ex\Services;

use Drupal\Core\Entity\EntityInterface;
use Drupal\taxonomy\Entity\Term;

class HookTextFormService {

 ## S1: Demo hook form sử dụng [submit]
  public function formAlter(&$form, $form_id) {
    if ($form_id === "node_product_form") {
      $form['create_save'] = [
        '#type' => 'checkbox',
        '#title' => t('Tạo Taxonomy'),
        '#weight' => 150,
      ];
      $form['actions']['submit']['#submit'][] = [self::class, 'hookFormSubmit'];
    }
  }

  public function hookFormSubmit($form, $form_state) {
    if ($form_state->getValue('create_save') === 1) {
      $term = Term::create([
        'parent' => [],
        'name' => $form_state->getValue('title')[0]['value'],
        'vid' => "san_pham",
      ])->save();
    }
  }

## S2: Demo hook sử dụng node insert
  public function hookFormEntityInsert(EntityInterface $entity) {
    if ($entity->bundle() === 'product' && $entity->get('field_tao_taxonomy')->value === 1) {
      $entity->label();
      $term = Term::create([
        'parent' => [],
        'name' => $entity->get('title')[0],
        'vid' => "san_pham",
      ])->save();

      if ($term === 1) {
        \Drupal::messenger()->addMessage(t('Term %term đã được tạo!', ['%term' => $entity->get('title')->value]));
      }
    }
  }

  /**
   * @throws \Drupal\Core\Entity\EntityStorageException
   * @throws \Drupal\Component\Plugin\Exception\InvalidPluginDefinitionException
   * @throws \Drupal\Component\Plugin\Exception\PluginNotFoundException
   */
   
   ## S3: Demo hook dùng để xóa, sử dụng node delete
  public function deleteTaxonomy(EntityInterface $entity) {
    if ($entity->bundle() === 'product') {
      $name = $entity->label();
      /** @var \Drupal\taxonomy\Entity\Term $term */
      $terms = \Drupal::entityTypeManager()->getStorage('taxonomy_term')->loadByProperties(['name' => $name]);
      foreach ($terms as $term) {
        $term->delete();
      }
    }
  }
}
```
