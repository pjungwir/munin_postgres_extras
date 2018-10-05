# `munin_postgres_extras`

This is a [munin](http://munin-monitoring.org/) plugin for collecting some extra metrics about Postgres,
not provided by [the normal Postgres plugins](https://github.com/munin-monitoring/contrib/tree/master/plugins/postgresql).

These plugins are included:

## `postgresql_blocked_queries`

Shows the number of queries currently waiting on a lock.

To install, symlink to your plugins directory:

    sudo ln -sf /this/directory/postgresql_blocked_queries /etc/munin/plugins/postgresql_blocked_queries

then make sure it runs as postgres:

    cat <<EOF | sudo tee /etc/munin/plugin-conf.d/postgresql_extras
    [postgresql_blocked_queries]
    user postgres
    EOF

Or you can make it run as a different OS user,
but it should be able to run `psql` without needing an interactive password.

You can include any of these optional settings in the config file:

    env.DBPORT 5433         # default is 5432
    env.DBHOST mydb01       # default is unset (peer authentication to the local host)
    env.DBNAME myapp_prod   # default is unset (checks all databases)
    env.DBUSER myapp_user   # default is unset (peer authentication with the OS user above)

If you use `DBUSER`, it should refer to a postgres role.
You may need a [`.pgpass`](https://www.postgresql.org/docs/current/static/libpq-pgpass.html) file
so that munin can run `psql` with that user without prompting for a password.

You may also run multiple copies of this plugin by adding extra to the symlink name, e.g.:

    sudo ln -sf /this/directory/postgresql_blocked_queries /etc/munin/plugins/postgresql_blocked_queries_production
    sudo ln -sf /this/directory/postgresql_blocked_queries /etc/munin/plugins/postgresql_blocked_queries_staging

Then set up the configurations accordingly:

    [postgresql_blocked_queries*]
    user postgres

    [postgresql_blocked_queries_production]
    env.DBNAME myapp_prod

    [postgresql_blocked_queries_staging]
    env.DBNAME myapp_staging

In the graphs, the extra part of the plugin name will appear in the title.

You can test your setup like this:

    sudo munin-run postgresql_blocked_queries config
    sudo munin-run postgresql_blocked_queries
