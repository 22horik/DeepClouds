# XII. Admin processes

### Run admin/management tasks as one-off processes

The [process formation](VIII%20Concurrency%203d5f5d0b9428459b8927bf261b93db25.md) is the array of processes that are used to do the app’s regular business (such as handling web requests) as it runs. Separately, developers will often wish to do one-off administrative or maintenance tasks for the app, such as:

- Running database migrations (e.g. `manage.py migrate` in Django, `rake db:migrate` in Rails).
- Running a console (also known as a [REPL](http://en.wikipedia.org/wiki/Read-eval-print_loop) shell) to run arbitrary code or inspect the app’s models against the live database. Most languages provide a REPL by running the interpreter without any arguments (e.g. `python` or `perl`) or in some cases have a separate command (e.g. `irb` for Ruby, `rails console` for Rails).
- Running one-time scripts committed into the app’s repo (e.g. `php scripts/fix_bad_records.php`).

One-off admin processes should be run in an identical environment as the regular [long-running processes](VI%20Processes%20b60f1b06f8364a1388aa44f32f417c5e.md) of the app. They run against a [release](V%20Build,%20release,%20run%209636ae65a02c4cb6b4450bb8fdf61471.md), using the same [codebase](I%20Codebase%202b059202958543419888e3e678b44fc5.md) and [config](III%20Config%205b5606111aee443b801e040561320667.md) as any process run against that release. Admin code must ship with application code to avoid synchronization issues.

The same [dependency isolation](II%20Dependencies%2051ed07fe144d4619b2597e06ca3bf96e.md) techniques should be used on all process types. For example, if the Ruby web process uses the command `bundle exec thin start`, then a database migration should use `bundle exec rake db:migrate`. Likewise, a Python program using Virtualenv should use the vendored `bin/python` for running both the Tornado webserver and any `manage.py` admin processes.

Twelve-factor strongly favors languages which provide a REPL shell out of the box, and which make it easy to run one-off scripts. In a local deploy, developers invoke one-off admin processes by a direct shell command inside the app’s checkout directory. In a production deploy, developers can use ssh or other remote command execution mechanism provided by that deploy’s execution environment to run such a process.