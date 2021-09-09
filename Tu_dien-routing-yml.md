## file routing.yml
```php
tu_dien.import:
  path: '/import-tu-dien'
  defaults:
    _form: '\Drupal\tu_dien\Form\ImportTuDien'
    _title: 'Import Từ Điển'
  requirements:
    _permission: 'access content'

tu_dien.list:
  path: '/tu_dien/list'
  defaults:
    _title: 'Load từ điển'
    _controller: '\Drupal\tu_dien\Controller\TuDienController::entryList'
  requirements:
    _permission: 'access content'

tu_dien.add:
  path: '/tu_dien/add'
  defaults:
    _title: 'Tạo mới từ điển'
    _form: '\Drupal\tu_dien\Form\TuDienAddForm'
  requirements:
    _permission: 'access content'

tu_dien.update:
  path: '/tu_dien/update'
  defaults:
    _title: 'Update từ điển'
    _form: '\Drupal\tu_dien\Form\TuDienUpdateForm'
  requirements:
    _permission: 'access content'
    
tu_dien.delete:
  path: '/tu-dien/delete'
  defaults:
    _title: 'Delete từ điển'
    _form: '\Drupal\tu_dien\Form\TuDienDeleteForm'
  requirements:
    _permission: 'access content'

tu_dien.delete_type:
  path: '/tu-dien/delete-type'
  defaults:
    _title: 'Delete Type từ điển'
    _form: '\Drupal\tu_dien\Form\TuDienDeleteTypeForm'
  requirements:
    _permission: 'access content' 
```
    
