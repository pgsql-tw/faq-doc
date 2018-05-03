# Autovacuum Tuning Basics

本篇編譯自：[“Autovacuum Tuning Basics”，Tomas Vondra](https://blog.2ndquadrant.com/autovacuum-tuning-basics/)

A few weeks ago I covered the [basics of tuning checkpoints](https://blog.2ndquadrant.com/basics-of-tuning-checkpoints/), and in that post I also mentioned that the second common source of performance issues is autovacuum \(based on what we see on the mailing list and at our customers under [support](https://2ndquadrant.com/en/support/support-postgresql/)\). So let me follow-up on that with this post about the basics of `autovacuum` tuning. I’ll very briefly explain the necessary theory \(dead tuples, bloat and how `autovacuum`deals with it\), but the main focus of this blog post is tuning – what configuration options are there, rules of thumb, etc.

### Dead tuples

First, let’s briefly explain what are “dead tuples” and “bloat.” \(If you want a more detailed explanation, perhaps read Joe Nelson’s post which discusses this in a bit more detail.\)

When you do a `DELETE` in PostgreSQL, the row \(aka tuple\) is not immediately removed from the data file. Instead it is only marked as deleted by setting `xmax` field in a header. Similarly for `UPDATE`s, which may be seen as `DELETE` + `INSERT` in PostgreSQL.

This is one of the basic ideas behind PostgreSQL MVCC, as it allows greater concurrency with only minimal locking between the different processes. The downside of this MVCC implementation is of course that it leaves behind the deleted tuples, even after all the transactions that might see those versions finish.

If not cleaned up, those “dead tuples” \(effectively invisible to any transaction\) would stay in the data files forever, wasting disk space, and for tables with many `DELETE`s and `UPDATE`s the dead tuples might easily account for vast majority of disk space. Of course, those dead tuples would also be referenced from indexes, further increasing the amount of wasted disk space. This is what we call “bloat” in PostgreSQL. And naturally, the more data queries have to process \(even if 99% of it is immediately thrown away as “dead”\), the slower the queries.

### `VACUUM` and `autovacuum`

The most straightforward way to reclaim space occupied by dead tuples \(and make it available for new rows\) is by manually running `VACUUM` command. This maintenance command will scan the table and remove dead tuples both from the table and indexes – it will not generally return the disk space back to the operating system, but it will make it usable for new rows.

**Note:** `VACUUM FULL` would reclaim the space and return it to the OS, but is has a number of disadvantages. Firstly it acquires exclusive lock on the table, blocking all operations \(including `SELECT`s\). Secondly, it essentially creates a copy of the table, doubling the disk space needed, so it’s not very practical when already running out of disk space.

The trouble with `VACUUM` is that it’s entirely manual action – it only happens when you decide run it, not when it’s needed. You may put it into `cron` and run it every 5 minutes on all tables, but the chances are most of the runs will not actually clean anything, and the only effect will be higher CPU and I/O usage on the system. Or you may run it only once a day at night, in which case you’ll probably accumulate more dead tuples that you’d like.

Which leads us to the primary purpose of `autovacuum`; do the cleanup as needed to keep the amount of wasted space under control. The database does know how many dead tuples were produced over time \(each transaction reports the number of tuples it deleted and updated\), and so can trigger cleanup when the table accumulates a certain number of dead tuples \(by default this is 20% of the table, as we’ll see\). So it will be executed more often during busy periods, and less often when the database is mostly idle.

### `autoanalyze`

Cleanup of dead tuples is not the only task of `autovacuum`. It’s also responsible for updating data distribution statistics, used by the optimizer when planning queries. You may collect those manually by running `ANALYZE`, but it suffers similar issues as `VACUUM` – you’re likely to run it either too often or not often enough.

And the solution is also similar – the database can watch how many rows changed in the table, and run `ANALYZE` automatically.

**Note:** The negative effects are a bit worse for `ANALYZE`, because while the cost of `VACUUM` is proportional to the amount of dead tuples \(so fairly low when there are few/none\), `ANALYZE` has to rebuild the statistics from scratch on every execution. On the other hand, if you’re not running it often enough, the cost of poor plan choices may be just as severe.

I’ll mostly ignore this `autovacuum` task in the rest of this post for the sake of brevity – the configuration is fairly similar to the cleanup anyway, and follows roughly the same reasoning.

### Monitoring

Before doing any sort of tuning, you need to be able to collect relevant data – otherwise how could you say you need to do any tuning at all, or evaluate the impact of configuration changes?

In other words, you should have some basic monitoring in place, collecting metrics from the database. For cleanup, you need to be looking at least at these values:

\* \`pg\_stat\_all\_tables.n\_dead\_tup\` – number of dead tuples in each table \(both user tables and system catalogs\)  
\* \`\(n\_dead\_tup / n\_live\_tup\)\` – ratio of dead/live tuples in each table  
\* \`\(pg\_class.relpages / pg\_class.reltuples\)\` – space “per row”

If you already have a monitoring system deployed \(and you should\), chances are you’re already collecting such metrics. The overall goal is to get stable behavior, with no sudden/significant changes to any of those metrics.

There’s also a handy [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) extension, allowing you to perform analysis on tables and indexes, including computing the amount of free space, dead tuples etc.

### Tuning Goals

Before looking at the actual configuration parameters, let’s briefly discuss what are the high-level tuning goals, i.e. what we want to achieve when changing the parameters:

* **cleanup dead tuples** – Keep the amount of disk space reasonably low, not to waste unreasonable amount of disk space, prevent index bloat and keep queries fast.
* **minimize cleanup impact** – Don’t perform cleanup too often, as it would waste resources \(CPU, I/O and RAM\) and might significantly hurt performance.

That is, you need to find the right balance – running it too often may be just as bad as not running it often enough. The balance heavily depends on the amount of data you manage, the type of workload you are dealing with \(number of `DELETE/UPDATE`s\).

Most default values in `postgresql.conf` are quite conservative, for two reasons. Firstly, the default values were decided a few years ago, based on the resources \(CPU, RAM, …\) common at that time. Secondly, we want the default configuration to work everywhere, including tiny machines like Raspberry Pi or small VPS servers. For many deployments \(particularly smaller ones and/or handling read-mostly workloads\) the default configuration parameters will however work just fine.

As the database size and/or amount of writes increase, problems start to appear. The typical issue is that the cleanup does not happen often enough, and then when it happens it significantly disrupts performance, because it has to deal with a lot of garbage. If those cases you should follow this simple rule:

_If it hurts, you’re not doing it often enough._

That is, tune the parameters so that the cleanup happens more often, and processes smaller amount of dead tuples every time.

**Note**: People sometimes follow a different rule – _If it hurts, don’t do it._ – and disable `autovacuum` entirely. Please don’t do that unless you really \(really really\) know what you’re doing, and have regular cleanup script in place. Otherwise you’re painting yourself in the corner, and instead of somewhat degraded performance you’ll have to deal with severely degraded performance or possibly even an outage.

So now that we know what we want to achieve by the tuning, let’s see the configuration parameters …

### Thresholds and Scale Factors

Naturally, the first thing you may tweak is when the cleanup gets triggered, which is affected by two parameters:

* **autovacuum\_vacuum\_threshold** = 50
* **autovacuum\_vacuum\_scale\_factor** = 0.2

and the cleanup is triggered whenever the number of dead tuples \(which you can see as `pg_stat_all_tables.n_dead_tup`\) exceeds

`threshold + pg_class.reltuples * scale_factor`

the table will be considered as in need of cleanup. The formula basically says that up to 20% of a table may be dead tuples before it gets cleaned up \(the threshold of 50 rows is there to prevent very frequent cleanups of tiny tables\).

The default scale factor works fine for small and medium-sized tables, but not so much for very large tables – on 10GB table this is roughly 2GB of dead tuples, while on 1TB table it’s ~200GB.

This is an example of accumulating a lot of dead tuples, and processing all of it at once, which is going to hurt. And per the rule mentioned before, the solution is to do it more often by significantly decreasing the scale factor, perhaps even like this:

`autovacuum_vacuum_scale_factor = 0.01`

which decreases the limit to only 1% of the table. An alternative solution is to abandon the scale factor entirely, and use solely the threshold

```text
autovacuum_vacuum_scale_factor = 0
autovacuum_vacuum_threshold = 10000
```

which should trigger the cleanup after generating 10000 dead tuples.

One trouble is that these changes in `postgresql.conf` affect all tables \(the whole cluster, in fact\), and it may undesirably affect cleanups of small tables, including for example system catalogs.

When the small tables are cleaned up more frequently, the easiest solution is to simply ignore the problem entirely. Cleanup of small tables is going to be fairly cheap, and the improvement on large tables is usually so significant that even if you ignore small inefficiency on small tables, the overall effect is still very positive.

But if you decided to change the configuration in a way that would significantly delay cleanup on small tables \(as for example with setting scale\_factor=0 and threshold=10000\), it’s better to apply those changes only to particular tables using `ALTER TABLE`:

```text
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0);
ALTER TABLE t SET (autovacuum_vacuum_threshold = 10000);
```

Try to keep the configuration as simple as possible, and override the parameters for as few tables as possible. It’s also a good idea to include this into your internal documentation, including the reasoning for particular values.

### Throttling {#THROTTLING}

A nice feature build into `autovacuum` is throttling. The cleanup is meant to be a maintenance task running in the background, with minimum impact on user queries etc. In other words, it should not consume too much resources \(CPU and disk I/O\), and this is exactly the purpose of the throttling built into `autovacuum`.

The cleanup process is fairly simple – it reads pages \(8kB chunks of data\) from data files, and checked if it needs cleanup. If there are no dead tuples, the page is simply thrown away without any changes. Otherwise it’s cleaned up \(dead tuples are removed\), is marked as “dirty” and eventually written out. The costing is based on defining cost for three basic operations:

```text
vacuum_cost_page_hit = 1
vacuum_cost_page_miss = 10
vacuum_cost_page_dirty = 20
```

That is, if the page is read from `shared_buffers`, it counts as 1. If it’s not found in `shared_buffers` and needs to be read from the OS, it counts as 10 \(it might still be served from RAM, but we don’t know\). And finally, if the page is dirtied by the cleanup, it counts as 20. That allows us to compute “cost of work” done by `autovacuum`.

The throttling is then done by limiting the amount of work that can be done in one go, which is by default set to 200, and every time the cleanup does this much work it’ll sleep for 20ms:

```text
autovacuum_vacuum_cost_delay = 20ms
autovacuum_vacuum_cost_limit = 200
```

So, how much work does that actually allow? With 20ms delay, the cleanup can do 50 rounds per second, and with 200 tokens per round that means 10000 token per second. That means:

* 80 MB/s reads from `shared_buffers` \(assuming it’s not dirtied\)
* 8 MB/s reads from OS \(possibly from disk\)
* 4 MB/s writes \(pages dirtied by the `autovacuum` process\)

Considering the capabilities of current hardware, and that the reads/writes are mostly sequential, those limits are too low.

What we generally do is increasing the `cost_limit` parameter, e.g. to 1000 \(or 2000\), which increases the throughput by 5x \(or 10x\). You may of course tweak the other parameters \(cost per page operation, sleep delay\), but we do that only very rarely – changing the cost limit works well enough.

### Number of Workers

One configuration option not yet mentioned is `autovacuum_max_workers`, so what’s that about? Well, the cleanup does not happen in a single `autovacuum` process, but the database is allowed to start up to `autovacuum_max_workers` processes that actually do cleanup of different databases/tables.

That’s useful, because you for example don’t want to stop cleaning up small tables until a cleanup of a single large table finishes \(which may take quite a bit of time, because of the throttling\).

The trouble is users assume the number of workers is proportional to the amount of cleanup that can happen. If you bump the number of `autovacuum` workers up to 6, it’ll surely do twice as much work compared to the default 3 workers, right?

Well, no. The cost limit, described a few paragraphs ago, is global, shared by all `autovacuum` workers. Each worker process only gets `1/autovacuum_max_workers` of the total cost limit, so increasing the number of workers will only make them go slower.

It’s a bit like highway – doubling the number of cars but making them go half the speed will only give you about the same number of people reaching the destination per hour.

So if the cleanup on your database can’t keep up with user activity, increasing the number of workers is not a solution, unless you also tweak the other parameters.

### Per-table Throttling

Actually, when I said that the cost limit is global and shared by all `autovacuum` workers, I’ve been lying \(a bit\). Similarly to scale factor and threshold, it’s possible to set the cost limit and delay per table:

```text
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Workers processing such tables are then not included in the global costing, and are throttled independently.

This gives you quite a bit of flexibility and power, but don’t forget – with great power comes great responsibility!

In practice, we almost never use this feature, for two basic reasons. Firstly, you generally do want to use a single global limit on the background cleanup. Secondly, having multiple workers that are sometimes throttled together and sometimes independently makes it much harder to monitor and analyze behavior of the system.

### Summary

So that’s how you tune `autovacuum`. If I had to sum it into a few basic rules, it’d be these five:

* Don’t disable `autovacuum`, unless your really know what you’re doing. Seriously.
* On busy databases \(doing a lot of `UPDATE`s and `DELETE`s\), particularly large ones, you should probably decrease the scale factor, so that cleanup happens more frequently.
* On reasonable hardware \(good storage, multiple cores\), you should probably increase the throttling parameters, so that the cleanup can keep up.
* Increasing `autovacuum_max_workers` alone will not really help in most cases. You’ll get more processes that go slower.
* You can set the parameters per table using `ALTER TABLE`, but think twice if you really need that. It makes the system more complex and more difficult to inspect.

I originally included a few sections explaining cases when `autovacuum` does not really work, and how to detect them \(and what is the best solution\), but the blog post is already too long so I’ll post that separately in a few days.

