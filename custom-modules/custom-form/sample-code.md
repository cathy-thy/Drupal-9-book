# Sample Code

{% embed url="https://github.com/cathy-thy/d9-demo/tree/main/custom/my_custom_form" %}
View my full code
{% endembed %}

### 1. Folder Structure



### 2. Code Analysis

#### 2.1 Create Table

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

After enabling your module, visit your DB, you should see the table is created.

![](../../.gitbook/assets/form1.png)

![](<../../.gitbook/assets/form2 (1).png>)



