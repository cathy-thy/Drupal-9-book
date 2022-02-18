# Sample Code

{% embed url="https://github.com/cathy-thy/d9-demo/tree/main/custom/my_custom_form" %}
View my full code
{% endembed %}

### 1. Folder Structure





### 2. Create Table

First thing you need to do is to create a table for storing your form data. You can do that by including the "**.install**" file on your custom module.&#x20;

I have a table named "**studentinfo**" with **four attributes**

* **id**
* **student\_name**
* **student\_gender**
* **student\_email**

<details>

<summary>my_custom_form.install</summary>

```php
<?php
function my_custom_form_schema() {
  $schema['studentinfo'] = [
    'fields' => [
      'id'=>[
        'type'=>'serial',
        'not null' => TRUE,
      ],
      'student_name'=>[
        'type' => 'varchar',
        'length' => 40,
        'not null' => TRUE,
      ],
      'student_gender'=>[
        'type' => 'varchar',
        'length' => 40,
        'not null' => TRUE,
      ],
      'student_email'=>[
        'type' => 'varchar',
        'length' => 40,
        'not null' => TRUE,
      ],
    ],
    'primary key' => ['id'],
  ];
  return $schema;
}
```

</details>

After enabling your module, visit your DB, you should see the table is created. There is no record yet, so you will see empty table.

![](../../.gitbook/assets/form1.png)

![](<../../.gitbook/assets/form2 (1).png>)

![](../../.gitbook/assets/form3.png)

### 2.1 Build Form

You can see there is "**StudentInfoForm.php**", here is where you create the form.&#x20;

<details>

<summary>StudentInfoForm.php</summary>

```php
<?php

namespace Drupal\my_custom_form\Form;

use Drupal\Core\Form\FormBase;
use Drupal\Core\Form\FormStateInterface;
use Drupal\Core\Database\Database;
use Drupal\Core\Url;
use Symfony\Component\HttpFoundation\RedirectResponse;

/**
 * Form Data
 */
class StudentInfoForm extends FormBase
{
    /**
     * {@inheritdoc}
     */
    public function getFormId()
    {
        return 'studentinfo_form';
    }

    /**
     * {@inheritdoc}
     */
    public function buildForm(array $form, FormStateInterface $form_state)
    {
        $conn = Database::getConnection();
        $record = [];
        if (isset($_GET['id'])) {
            $query = $conn->select('studentinfo', 'si')
                ->condition('id', $_GET['id'])
                ->fields('si');
            $record = $query->execute()->fetchAssoc();
        }

        //Description of the form to be displayed on the user interface
        $form['text_header'] = [
        '#prefix' => '<strong>',
        '#suffix' => '<br><br></strong>',
        '#markup' => $this->t('This is for recording student information.'),
        '#weight' => -100,
        ];

        //Different fields
        $form['student_name'] = [
        '#type' => 'textfield',
        '#title' => $this->t('Student Name'),
        '#required' => true,
        '#default_value' => (isset($record['student_name']) && $_GET['id']) ? $record['student_name'] : '',
        ];

        $form['student_gender'] = [
            '#type' => 'select',
            '#title' => $this->t('Gender'),
            '#options' => [
                'male' => t('Male'),
              'female' => t('Female'),
              'na' => t('Prefer not to say'),
              '#default_value' => (isset($record['student_gender']) && $_GET['id']) ? $record['student_gender'] : '',
            ],
        ];

        $form['student_email'] = [
        '#type' => 'email',
        '#title' => $this->t('Student Email'),
        '#required' => true,
        '#default_value' => (isset($record['student_email']) && $_GET['id']) ? $record['student_email'] : '',
        ];

        //Submit and Reset buttons
        $form['submit'] = [
        '#type' => 'submit',
        '#value' => 'save',
        ];

        $form['reset'] = [
        '#type' => 'button',
        '#value' => 'reset',
        '#attributes' => ['onclick' => 'this.form.reset();return false;',],
        ];
        return $form;
    }

    /**
    * {@inheritdoc}
    */
    public function validateForm(array &$form, FormStateInterface $form_state)
    {
        $student_name = $form_state->getValue('student_name');
        if (preg_match('/[#$%^&*()+=\-\[\]\';,.\/{}|":<>?~\\\\]/', $student_name)) {
            $form_state->setErrorByName('student_name', $this->t('Your name should not include special character(s).'));
        }
        parent::validateForm($form, $form_state);
    }

    /**
     * {@inheritdoc}
     */
    public function submitForm(array &$form, FormStateInterface $form_state)
    {

        $field = $form_state->getValues();

        $student_name = $field['student_name'];
        $student_gender = $field['student_gender'];
        $student_email = $field['student_email'];

        $field = [
            'student_name' => $student_name,
            'student_gender' => $student_gender,
            'student_email' => $student_email,
        ];
        $query = \Drupal::database();
        if (isset($_GET['id'])) {
            //for updating existing record
            $query->update('studentinfo')
                ->fields($field)
                ->condition('id', $_GET['id'])
                ->execute();
            \Drupal::messenger()->addMessage('Successfully saved data from custom form.');
        } else {
            //for inserting new value
            $query->insert('studentinfo')
                ->fields($field)
                ->execute();
            \Drupal::messenger()->addMessage('Successfully saved new data from custom form.');
        }
        drupal_flush_all_caches();
        $form_state->setRedirect('my_custom_form.studentinfo_table_controller_display');
    }
}
```

</details>

We can create form by extending the "**FormBase**"&#x20;

There are three main functions&#x20;

* buildForm
* validateForm  (this is in fact optional, missing this will not affect your form creation)
* submitForm

#### 2.1.1 buildForm

On buildForm function, I have connected to the database. You can also see&#x20;

* student\_name
* student\_gender
* student\_email

#### 2.1.2 validateForm

On the validateForm, I check whether there is special character on the user name.&#x20;

If you use validator and invalid data is entered, the user will not be able to submit the form.

![](../../.gitbook/assets/form6.png)

#### 2.2.2 submitForm

On the submitForm, I also have to connect to the database. If the user is updating the existing record, I will use the update; otherwise, I have to insert new value to the DB.

```
$query->update('studentinfo') 
//or
$query->insert('studentinfo')
```

### 2.2 Visit Form

On the ".routing" file, you can specify the location of the form. This time, the path is "_/my\_custom\_form/form/studentinfo/data_"

<details>

<summary>my_custom_form.routing.yml</summary>

```php
my_custom_form.studentinfo_form:
  path: '/my_custom_form/form/studentinfo/data'
  defaults:
    _form: '\Drupal\my_custom_form\Form\StudentInfoForm'
    _title: 'Student Info Form'
  requirements:
    _access: 'TRUE'
```

</details>

{% hint style="info" %}
If you are curious about how do we know it is setting the path for the form that we want, you can check "**StudentInfoForm.php**", there is a function "**getFormId()**", we have specified the id ("**studentinfo\_form**") there. \
This links to the first line of routing file, "_**my\_custom\_form.studentinfo\_form**_"
{% endhint %}

#### Visit the form

![](../../.gitbook/assets/form4.png)







