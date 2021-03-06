# Libertree Usage

This documentation assumes the directories and settings given in
INSTALLATION.md were followed.  If not, adapt the instructions below
accordingly.

## Startup

### System services

If necessary, start up the system services.

Start up the PostgreSQL server.  Under Gentoo:

    # /etc/init.d/postgresql-9.1 start

Start up the web server which will proxy requests.  This is either Nginx,
Apache or something equivalent.  Under Gentoo:

    # /etc/init.d/nginx start
    # # OR
    # /etc/init.d/apache2 start

### Ruby services

Libertree needs three Ruby services running: the frontend, the backend and job
processing.  The websocket server is optional.  Some of these services might be
served by more than one process or daemon.

#### Backend

As the libertree user:

    % cd ~/git/libertree-backend-rb
    % rvm use 1.9.3@libertree-backend-rb
    % bundle exec ruby -Ilib bin/server.rb config.yaml

#### Job Processing

As the libertree user:

    % cd ~/git/libertree-backend-rb
    % rvm use 1.9.3@libertree-backend-rb
    % bundle exec ruby bin/job-processor.rb config.yaml

#### Frontend

As the libertree user:

    % cd ~/git/libertree-frontend-ramaze
    % rvm use 1.9.3@libertree-frontend-ramaze
    % bundle exec unicorn -p <port number>

Use any port number desired.  The web server proxy will proxy requests from the
standard HTTP port (80) to this port.

Optional (recommended) web socket server:

    % cd ~/git/libertree-frontend-ramaze
    % rvm use 1.9.3@libertree-frontend-ramaze
    % bundle exec ruby websocket-server.rb

## Maintenance

Multiple job processing and frontend processes can be started, but almost all
new installations will not need to scale in this way for some time.

To update the server with new code updates, first update the code of each
repository:

    % cd ~/git/libertree-db
    % git fetch origin && git merge --ff-only @{u}
    % cd ~/git/libertree-client-rb
    % git fetch origin && git merge --ff-only @{u}
    % cd ~/git/libertree-backend-rb
    % git fetch origin && git merge --ff-only @{u}
    % cd ~/git/libertree-frontend-ramaze
    % git fetch origin && git merge --ff-only @{u}

Then restart all the services.  They can all be stopped by typing Ctrl-C. Check for
release notes which may describe new settings or migrations.

If there are SCSS changes, compile the SCSS to CSS:

    % cd ~/git/libertree-frontend-ramaze
    % ./css-build.sh

This command is safe to run, even if it is not certain whether there were SCSS
changes.
