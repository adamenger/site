---
title: "Scheduled Tasks"
order: 450
---

Convox can set up `cron`-like recurring tasks on any of your application processes. This can be useful for background work like data dumps, batch jobs, or even queueing other background jobs for a worker.

#### Configuring tasks

Scheduled tasks are configured in `docker-compose.yml` using labels in the `convox.cron` namespace. The label format is:

`convox.cron.<task name>=<cron expression> <command>`

- **task name** is a unique name for each task that you choose. Task names may not be reused within the same application process and must be alphanumeric.

- **cron expression** describes the schedule on which the task will be invoked. See "Cron expression format" below for more info.

- **command** is the command to be run in this process. Invocations of the command are equivalent to manually running `convox run <process> <command> --detach`, and in fact it's a good idea to test that your command works using `convox run`.

Example: to run the command `bin/myjob` every hour on the `web` process, you would configure the label like this:

```yaml
web:
  labels:
    - convox.cron.myjob=0 * * * ? bin/myjob
```

#### Cron expression format

Cron expressions use the following format:

```
.---------------- minute (0 - 59)
|  .------------- hour (0 - 23)
|  |  .---------- day-of-month (1 - 31)
|  |  |  .------- month (1 - 12) OR JAN,FEB,MAR,APR ...
|  |  |  |  .---- day-of-week (0 - 6) (Sunday=0 or 7) OR SUN,MON,TUE,WED,THU,FRI,SAT
|  |  |  |  |
*  *  *  *  *
```

<div class="block-callout block-show-callout type-info" markdown="1">
  Using both day-of-week and day-of-month in the same expression is not supported. One of these fields must be a `?`.
</div>

Some example expressions:

<table>
  <tr>
    <th>Expression</th>
    <th>Meaning</th>
  </tr>
  <tr>
    <td><code>* * * * ?</code></td>
    <td>Run every minute</td>
  </tr>
  <tr>
    <td><code>*/10 * * * ?</code></td>
    <td>Run every 10 minutes</td>
  </tr>
  <tr>
    <td><code>0 * * * ?</code></td>
    <td>Run every hour</td>
  </tr>
  <tr>
    <td><code>30 6 * * ?</code></td>
    <td>Run at 6:30am UTC every day</td>
  </tr>
  <tr>
    <td><code>30 18 ? * MON-FRI</code></td>
    <td>Run at 6:30pm UTC every weekday</td>
  </tr>
  <tr>
    <td><code>0 12 1 * ?</code></td>
    <td>Run at noon on the first day of every month</td>
  </tr>
</table>
