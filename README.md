# `munin_postgres_extras`

This is a [munin](http://munin-monitoring.org/) plugin for collecting some extra metrics about Postgres,
not provided by [the normal Postgres plugins](https://github.com/munin-monitoring/contrib/tree/master/plugins/postgresql).

These plugins are included:

## `postgres_blocked_queries`

Shows the number of queries currently waiting on a lock.

To install, copy this to `/usr/local/share/munin/plugins` (or somewhere else if you prefer),
then symlink from there to your plugins directory:

    sudo ln -sf /usr/local/share/munin/plugins/postgres_blocked_queries /etc/munin/plugins/postgres_blocked_queries

then make sure it runs as postgres:

    cat <<EOF | sudo tee /etc/munin/plugin-conf.d/postgres_extras
    [postgres_blocked_queries]
    user postgres
    EOF

Or you can make it run as a different OS user,
but it should be able to run `psql` without needing an interactive password.

You can include any of these optional settings in the config file:

    env.PGPORT 5433         # default is 5432
    env.PGHOST mydb01       # default is unset (peer authentication to the local host)
    env.PGDATABASE myapp_prod   # default is unset (checks all databases)
    env.PGUSER myapp_user   # default is unset (peer authentication with the OS user above)

If you use `PGUSER`, it should refer to a postgres role.
You may need a [`.pgpass`](https://www.postgresql.org/docs/current/static/libpq-pgpass.html) file
so that munin can run `psql` with that user without prompting for a password.

You may also run multiple copies of this plugin by adding extra to the symlink name, e.g.:

    sudo ln -sf /this/directory/postgres_blocked_queries /etc/munin/plugins/postgres_blocked_queries_production
    sudo ln -sf /this/directory/postgres_blocked_queries /etc/munin/plugins/postgres_blocked_queries_staging

Then set up the configurations accordingly:

    [postgres_blocked_queries*]
    user postgres

    [postgres_blocked_queries_production]
    env.PGUSER myapp_prod

    [postgres_blocked_queries_staging]
    env.PGUSER myapp_staging

In the graphs, the extra part of the plugin name will appear in the title.

You can test your setup like this:

    sudo munin-run postgres_blocked_queries config
    sudo munin-run postgres_blocked_queries
