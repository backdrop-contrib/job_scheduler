JOB SCHEDULER
===================

Simple API for scheduling tasks once at a predetermined time or periodically at a fixed interval.

CONTENTS OF THIS FILE
---------------------

 - Introduction
 - Tested
 - Known Issues
 - Special Thanks
 - Requirements
 - Installation
 - Coming From Drupal?
 - Usage
 - License
 - Credits
 - Maintainers

TESTED
-----

?

KNOWN ISSUES
---------------------

This module has undergone the usual porting to Backdrop, but I am only aware of its usefulness as an API in support of Feeds module.

Feeds module does not work in Backdrop yet because it depended on CTools in Drupal 7 and parts of CTools are in Backdrop core and I am not sure which parts and how to access them yet.

SPECIAL THANKS
--------------

https://www.drupal.org/u/febbraro for this nice module


REQUIREMENTS
------------

none

INSTALLATION
------------

Install this module using the official Backdrop CMS instructions at https://backdropcms.org/guide/modules

COMING FROM DRUPAL?
-------------------

Nothing substantially different.

PERMISSIONS
------------

@todo


USAGE
-----

Declare scheduler.

  function example_cron_job_scheduler_info() {
    $schedulers = array();
    $schedulers['example_unpublish'] = array(
      'worker callback' => 'example_unpublish_nodes',
    );
    return $schedulers;
  }

Add a job.

  $job = array(
    'type' => 'story',
    'id' => 12,
    'period' => 3600,
    'periodic' => TRUE,
  );
  JobScheduler::get('example_unpublish')->set($job);

Work off a job.

  function example_unpublish_nodes($job) {
    // Do stuff.
  }

Remove a job.

  $job = array(
    'type' => 'story',
    'id' => 12,
  );
  JobScheduler::get('example_unpublish')->remove($job);

Optionally jobs can declared together with a schedule in a hook_cron_job_scheduler_info().

  function example_cron_job_scheduler_info() {
    $schedulers = array();
    $schedulers['example_unpublish'] = array(
      'worker callback' => 'example_unpublish_nodes',
      'jobs' => array(
         array('type' => 'story', 'id' => 12, 'period' => 3600, 'periodic' => TRUE),
      )
    );
    return $schedulers;
  }

Jobs can have a 'crontab' instead of a period. Crontab syntax are Unix-like formatted crontab lines.
Example of job with crontab.

  // This will create a job that will be triggered from monday to friday, from january to july, every two hours
  function example_cron_job_scheduler_info() {
    $schedulers = array();
    $schedulers['example_unpublish'] = array(
      'worker callback' => 'example_unpublish_nodes',
      'jobs' => array(
         array('type' => 'story', 'id' => 12, 'crontab' => '0 */2 * january-july mon-fri', 'periodic' => TRUE),
      )
    );
    return $schedulers;
  }

Read more about crontab syntax, http://linux.die.net/man/5/crontab

Backdrop Queue integration
========================

Optionally, at the scheduled time Job Scheduler can queue a job for execution,
rather than executing the job directly. This is useful when many jobs need to
be executed or when the job's expected execution time is very long.

More information on Drupal Queue: http://api.drupal.org/api/group/queue/7

Instead of declaring a worker callback, declare a queue name.

  function example_cron_job_scheduler_info() {
    $schedulers = array();
    $schedulers['example_unpublish'] = array(
      'queue name' => 'example_unpublish_queue',
    );
    return $schedulers;
  }

This of course assumes that you have declared a queue. Notice how in this
pattern the queue callback contains the actual worker callback.

  function example_cron_queue_info() {
    $schedulers = array();
    $schedulers['example_unpublish_queue'] = array(
      'worker callback' => 'example_unpublish_nodes',
    );
    return $schedulers;
  }


Work off a job: when using a queue, Job Scheduler reserves a job for one hour
giving the queue time to work off a job before it reschedules it. This means
that the worker callback needs to reset the job's schedule flag in order to
allow renewed scheduling.

  function example_unpublish_nodes($job) {
    // Do stuff.
    // Set the job again so that its reserved flag is reset.
    JobScheduler::get('example_unpublish')->set($job);
  }

Example
=======

See Feeds module.


Hidden settings
===============

Hidden settings are variables that you can define by adding them to the $conf
array in your settings.php file.

Name:        'job_scheduler_class_' . $name
Default:     'JobScheduler'
Description: The class to use for managing a particular schedule.


Support
-------

https://github.com/backdrop/backdrop-issues/issues/487
https://github.com/backdrop/backdrop-issues/issues/1073

LICENSE
-------

This project is GPL v2 software. See the LICENSE.txt file in this directory for complete text.

CREDITS
-----------

- febbraro <https://www.drupal.org/u/febbraro>

Current Maintainers on Drupal:

- webflo <https://www.drupal.org/u/webflo>
- joachim <https://www.drupal.org/u/joachim>
- alex_b <https://www.drupal.org/u/alex_b>

MAINTAINERS
-----------

- Laryn Kragt Bakker <https://github.com/laryn>

Ported to Backdrop by:

 - biolithic <https://github.com/biolithic>
