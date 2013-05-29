manticore-django
================

Utility functionality for a Manticore Django project

Deployment script
-----------------
Manticore-django comes with a `fabric` deployment script. The script is based on Mezzanine's deployment script with additional features.
These features allow the script to deploy an *application* and *database* server using different `settings.py`.

### Fabric Configuration

This Fabric deploy script in Manticore-Django is a drop-in replacement for Mezzanine's fabric deployment.
In addition to Mezzanine's script, you are able to configure application, cron, and database hosts separately.

In your `settings.py` file:

        FABRIC = 
             "SSH_USER": "", # SSH username
             "SSH_PASS":  "", # SSH password (consider key-based authentication)
             "SSH_KEY_PATH":  "", # Local path to SSH key file, for key-based auth
             "DEPLOY_SSH_KEY_PATH": "", # Local path to an SSH private key that should be installed on the server for accessing remote repositories (optional)
             "DEPLOY_MY_PUBLIC_KEY": "~/.ssh/id_rsa.pub", # Local path to your SSH public key so you can access the server (optional)
             "APPLICATION_HOSTS": [], # List of hosts to deploy to, public IP addresses
             "DATABASE_HOSTS": [], # List of hosts to deploy to, public IP addresses for SSH access
             "CRON_HOSTS": [], # Optional list of hosts to run the cron job, public IP addresses, defaults to APPLICATION_HOSTS if not specified
             "PRIVATE_APPLICATION_HOSTS": [], # List of private IP addresses which the application server uses to communicate with the database, defaults to 127.0.0.1
             "PRIVATE_DATABASE_HOSTS": [], # List of private IP addresses that the database receives connections from the application server, defaults to 127.0.0.1
             "VIRTUALENV_HOME":  "", # Absolute remote path for virtualenvs
             "PROJECT_NAME": "", # Unique identifier for project
             "REQUIREMENTS_PATH": "", # Path to pip requirements, relative to project
             "GUNICORN_PORT": 8000, # Port gunicorn will listen on
             "LOCALE": "en_US.UTF-8", # Should end with ".UTF-8"
             "LIVE_HOSTNAME": "www.example.com", # Host for public site.
             "SITENAME": "Default", # Registered sitename in Django.
             "REPO_URL": "", # Git or Mercurial remote repo URL for the project
             "DB_PASS": "", # Live database password
             "ADMIN_PASS": "", # Live admin user password
             "LINUX_DISTRO": "squeeze", # Linux distribution such as Debian 6.0 (squeeze), 7.0 (wheezy), Ubuntu 10.04 (lucid), Ubuntu 12.04 (precise)
         }

### deploy/live_settings.py

Add/replace the following lines to Mezzanine's `deploy/live_settings.py`:

        DATABASES = {
            "default": {
                # Ends with "postgresql_psycopg2", "mysql", "sqlite3" or "oracle".
                "ENGINE": "django.db.backends.postgresql_psycopg2",
                # DB name or path to database file if using sqlite3.
                "NAME": "%(proj_name)s",
                # Not used with sqlite3.
                "USER": "%(proj_name)s",
                # Not used with sqlite3.
                "PASSWORD": "%(db_pass)s",
                # Set to empty string for localhost. Not used with sqlite3.
                "HOST": "%(primary_database_host)s",
                # Set to empty string for default. Not used with sqlite3.
                "PORT": "",
            }
        }

        ALLOWED_HOSTS = [%(allowed_hosts)s]

        # ...



### Tested Environments

I tested the script with the following configurations:

* Debian 6 and Postgresql 9.2
* Debian 6 and Postgresql 8.4 (deprecated)
