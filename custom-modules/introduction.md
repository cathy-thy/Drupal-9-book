---
description: >-
  There are few points you should understand before creating your first custom
  module.
---

# Introduction

### 1. How to enable custom module

It's simple to let Drupal know you have created custom module.

After you have added the following folders and "xxx.info.yml", you should be able to view your module on Drupal UI.&#x20;

{% hint style="info" %}
Be careful of the naming convention. Files inside should start with the name same as folder name (in this case, "demo\_module").
{% endhint %}

![Create "custom" folder under modules if you haven't completed that.](<../.gitbook/assets/image (5).png>)

```
// demo_module.info.yml content

name: 'Sample Demo Module'
description: 'demonstrate how to add a module'
package: Custom
type: module
core_version_requirement:  ^9
```

After adding the folder, navigate to the Drupal UI, you should be able to view your module on the extent page.

![](<../.gitbook/assets/image (1) (1).png>)











