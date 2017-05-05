---
layout: post
title:  "Doctrine ORM: Faking Null Relationships"
date:   2016-12-29 12:00:00
categories: symfony
description: Empty references must be null. What to do if they are not?
---

My dev team must create a read-only web API for an existing database
in order to transition our clients away from direct database access so we can
refactor the database at a later time. The plan is to
go with [Doctrine ORM](http://docs.doctrine-project.org/projects/doctrine-orm/en/latest/) in order to represent the data relationships easily.
It's been great fun but we
have encountered a problem: the current database contains foreign keys equalling
zero that should be null in order to work with ORM. In a perfect world, we would
convert the zeroes to null right away, but we can't because:

  1. It would break legacy code.
  2. It would break partner code that we can't easily update.

Once the API is online, we will force clients to use it over time, which will eliminate
problem #2. Then to solve #1 we can refactor the legacy code and the database at
the same time. In the meantime,
there is a solution involving [ORM lifecycle events](http://docs.doctrine-project.org/projects/doctrine-orm/en/latest/reference/events.html) that makes it possible to get
the API running without causing problems #1 and #2, which I will explain below.

Let me break it down with an example. Table __appointment__ contains:

{% highlight plaintext %}
id  date        doctor_id
1   2017-01-04  4
2   2017-02-01  0
{% endhighlight %}

Table __doctor__ contains:

{% highlight plaintext %}
id  name
4   Dr. Alpha
5   Dr. Beta
{% endhighlight %}

The appointment with id 1 is with Dr. Alpha, but the appointment with id 2 has
not yet been assigned a doctor. As mentioned above, in this database, this
"no assigned doctor" situation is represented
by setting doctor_id to 0. Notice that there is no 0 row in the doctor table.

*Doctrine ORM can't handle this.* You will get:

    EntityNotFoundException: Entity of type 'AppBundle\Entity\Doctor'
    for IDs id(0) was not found

ORM expects
that doctor_id would be null if a doctor has not been assigned to a given
appointment. We can't fix the data (yet), but we can fake it with a lifecycle
event.

{% highlight php linenos %}
<?php

namespace AppBundle\Entity;

use Doctrine\ORM\Mapping as ORM;

/**
 * @ORM\HasLifecycleCallbacks()
 */
class Appointment
{
  /**
   * @var Doctor
   *
   * @ORM\ManyToOne(targetEntity="AppBundle\Entity\Doctor")
   * @ORM\JoinColumn(nullable=true)
   */
  private $doctor;

  // ...

  /**
   * @ORM\PostLoad
   *
   * @todo Remove once 0 values in the table are converted to NULL.
   */
  public function loadNullDoctor()
  {
      if ($this->doctor->getId() == 0) {
          $this->doctor = null;
      }
  }
}
{% endhighlight %}

By implementing a handler for the PostLoad event, we can pretend there is a null
where zeroes are found.
