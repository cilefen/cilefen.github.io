---
layout: post
title:  "Drupal 7: Download real names with webform"
date:   2015-03-04 12:00:00
categories: drupal
---

Webform displays [real names](https://www.drupal.org/project/realname)
on reports but it doesn't export them for download. Instead, you get the
short user name. But the webform API provides two hooks for creating
synthetic output fields: `hook_webform_results_download_submission_information_info()`
and `hook_webform_results_download_submission_information_data()`. Here is
an example that creates a 'Full Name' column in the output:

{% highlight php %}
<?php

/**
 * Implements hook_webform_results_download_submission_information_data().
 */
function wapps_webform_results_download_submission_information_info() {
  return array(
    'wapps_full_name' => t('Full Name'),
  );
}

/**
 * Implements hook_webform_results_download_submission_information_data().
 */
function wapps_webform_results_download_submission_information_data($token, $submission, array $options, $serial_start, $row_count) {
  switch ($token) {
    case 'wapps_full_name':
      $account = user_load($submission->uid);
      return format_username($account);
  }
}
{% endhighlight %}
