---
layout: post
title:  "Drupal 7: Creating a simple token replacement text filter"
date:   2014-12-27 12:00:00
categories: drupal
---

This module demonstrates how to create a Drupal 7 text filter that
replaces tokens with some html.

Start by implementing `hook_filter_info()`.

{% highlight php %}
<?php

/**
 * Implements hook_filter_info().
 */
function dining_filter_filter_info() {
  $filters['dining_filter_issues'] = array(
    'title' => t('Dining Issues'),
    'description' => t('Provides inline food issues token replacement'),
    'process callback'  => '_dining_filter_issues_process',
    'tips callback' => '_dining_filter_issues_tips',
    'weight' => 50,
  );
  return $filters;
}
{% endhighlight %}

The process callback function performs the replacements.

{% highlight php %}
<?php

/**
 * Process callback for the dining_filter_issues filter.
 */
function _dining_filter_issues_process($text, $filter) {
  $issues = _dining_filter_issues();
  return str_replace(array_keys($issues), $issues, $text);
}
{% endhighlight %}

The tips callback provides help text for your content creators 
in the editing area. Tips look like this:

![Filter tips screenshot]({{ site.url }}/assets/filter-tips.png)

{% highlight php %}
<?php

/**
 * Filter tips callback for the dining_filter_issues filter.
 */
function _dining_filter_issues_tips($filter, $format, $long = FALSE) {
  return t('[v] = Vegetarian, [g] = Vegan, [n] = Nuts, [s] = shellfish, [p] = pork');
}
{% endhighlight %}

This utility function is used in `_dining_filter_issues_process()`. It
returns the array of replacments.

{% highlight php %}
<?php

/**
 * Returns the array of issues.
 */
function _dining_filter_issues() {
  return array(
    '[v]' => '<span class="dining-issue-icon dining-issue-vegetarian"></span>',
    '[g]' => '<span class="dining-issue-icon dining-issue-vegan"></span>',
    '[n]' => '<span class="dining-issue-icon dining-issue-nuts"></span>',
    '[s]' => '<span class="dining-issue-icon dining-issue-shellfish"></span>',
    '[p]' => '<span class="dining-issue-icon dining-issue-pork"></span>',
  );
}
{% endhighlight %}
