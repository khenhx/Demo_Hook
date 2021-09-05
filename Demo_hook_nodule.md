```php
<?php

use Drupal\Core\Entity\EntityInterface;
use Drupal\Core\Form\FormStateInterface;


/**
 * Implements hook_form_alter().
 */
function custom_ex_form_alter(&$form, FormStateInterface $form_state, $form_id) {
//  $hooktextformService = \Drupal::service('custom_ex.hook_text_form_services');
//  $hooktextformService->formAlter($form, $form_id);
}

/**
 * Implements hook_ENTITY_TYPE_insert().
 */
function custom_ex_node_insert(EntityInterface $entity) {
  $hooktextformService = \Drupal::service('custom_ex.hook_text_form_services');
  $hooktextformService->hookFormEntityInsert($entity);
}

function custom_ex_node_delete(EntityInterface $entity) {
  $hooktextformService = \Drupal::service('custom_ex.hook_text_form_services');
  $hooktextformService->deleteTaxonomy($entity);
}
```
