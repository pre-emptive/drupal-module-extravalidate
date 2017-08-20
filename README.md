# drupal-module-extravalidate
Easy Field Validation: The ExtraValidate Module

The ExtraValidate module is a small Drupal 6 module that helps module developers peform field validation on complex forms.

Ordinarily, Drupal passes all validation work to a form validation function. Whilst this is fine for most jobs, on complex forms things can get, well, complex. The ExtraValidate module can offload a good deal of the basic validation work required on common forms. It can check integers and floating point numbers (making sure they're actually numbers, and optionally within a defined range). For more complex work, it can call callback functions.

The automatic calling of callback functions is where the real power of ExtraValidate comes in. Where a form has a number of fields all requiring the same sort of information, a single callback can be used to check them all. The callback functions themselves are simple; they are passed the information they need to make an informed decision about a given form element, without having to do any complicated data manipulations. Thus, with a well crafted callback, it is possible to validate many fields very quickly with a minimum programming overhead.

A demonstration module for the ExtraValidate module is included along side the module itself. However, a module skeleton is as follows:
```
<?php
function example_form() {
  $form = array();
  $form['quantity'] = array(
    '#title' => 'Item Quantity',
    '#type' => 'textfield',
    '#extra_validate' => array('type' => 'integer', 'min' => 0, 'max' => 100),
  );
  $form['colour'] = array(
    '#title' => 'Item Colour',
    '#type' => 'textfield',
    '#extra_validate' => array('callback function' => 'example_form_callback'),
  );
  ...
}

function example_form_validate($form, &$form_state) {
  // This is all that's required to validate every element that has #extra_validate
  // information in it:
  module_invoke('extravalidate', 'validate', $form, $form_state);
}

function example_form_callback($form, $tree, $value) {
  // You would normally have many more colours than this little list!
  // (if not - redesign your form to use checkboxes or radios!)
  $colours = array('red', 'green', 'blue', 'orange', 'pink', 'purple', 'cyan', 'puce');
  // This is a really basic check, and not really complete!
  if(!in_array($value, $colours) && !preg_match('/^\s*#[0-9a-fA-F]+/', $value)) {
    $string = implode('][', $tree);
    form_set_error($string, t('%name must be a colour or an RGB value preceeded by #', array('%name' => $form['#title'])));
  }
}
?>
```
In the above snippet, we can see an ordinary form defined in the example_form() function. This is really standard Drupal, but with the addition of the special element "#extra_validate". It requires an array value, which contains all the information required by the ExtraValidate module. In the first example, the array value instructs ExtraValidate to ensure the value is an integer, within the range of 0 to 100. In a form that collects many integers, this is an easy way to validate them all.

The second example uses a callback function. This function is passed the form element, the position in the form's tree and the value of that particular element. Checking the value is then easy, as is reporting it via form_set_error(). Had there been many fields in the form to collect colours, this would provide a quick and convenient way to validate them all.
