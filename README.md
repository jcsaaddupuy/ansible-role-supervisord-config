supervisord_config
=================

This roles generate configurations files for supervisord and store them in
/etc/supervisor/conf.d.

It does not mess with the default supervisord configuration file.

Requirements
------------

This role does not install supervisord, you may want to install it with your
package manager or pypi.

It does not assume anything about your supervisord installation location and the
location for config files can bechanged with the variable supervisord_config_location (default to /etc/supervisor/conf.d)


Role Variables
--------------

- supervisord_config_location : location to store generatedconfiguration files
- supervisord_programs: dict containing keys/values to write in template

See examples below.

Dependencies
------------

None

Example Playbook
----------------

This example will generate two config file named yourapp-gunicorn.conf and yourapp-celery.conf.

The program name is managed throught the special key 'program_name', which will
also be use in the config file header like so : [program:value\_of\_programe\_name]


    - hosts: all
      roles:
         - role: supervisord_config
           supervisord_programs:
             # this key is for readability in yml and is not used in the template
             gunicorn:
               # this key will be used for the config file name and program name
               program_name: "yourapp-gunicorn"
               # Anything bellow will ends in the config file
               user: "app_user"
               environment:  APP_SETTINGS=whatever
               command: "/home/app_user/venv/bin/gunicorn app_name:app -c /path/to/config"
               directory: /path/to/app/directory 
               autostart: "true"
               autorestart: "true"
               redirect_stderr: "true"
               stdout_logfile: /path/to/logfile.log
               stderr_logfile : /path/to/logfile.log

             # this key is for readability in yml and is not used in the template
             celery:
               # this key will be used for the config file name and program name
               program_name: "yourapp-celery"
               # Anything bellow will ends in the config file
               user: "app_user"
               command: "/home/app_user/venv/bin/celery -A app.celery worker"
               directory: /path/to/app/directory 
               autostart: "true"
               autorestart: "true"
               redirect_stderr: "true"
               stdout_logfile: /path/to/logfile.log
               stderr_logfile : /path/to/logfile.log

Will generate two files in /etc/supervisor/supervisord.conf :

    cat /etc/supervisor/conf.d/yourapp-gunicorn.conf 

      [program:yourapp-gunicorn]
      user=app_user
      environment= APP_SETTINGS=whatever
      command=/home/app_user/venv/bin/gunicorn app_name:app -c /path/to/config
      directory=/path/to/app/directory 
      autostart=true
      autorestart=true
      redirect_stderr=true
      stdout_logfile=/path/to/logfile.log
      stderr_logfile=/path/to/logfile.log


    cat /etc/supervisor/conf.d/yourapp-celery.conf 

      [program:yourapp-celery]
      user=app_user
      command=/home/app_user/venv/bin/celery -A app.celery worker
      directory=/path/to/app/directory 
      autostart=true
      autorestart=true
      redirect_stderr=true
      stdout_logfile=/path/to/logfile.log
      stderr_logfile =/path/to/logfile.log

License
-------

BSD

Author Information
------------------

[github/jcsaaddupuy](https://github.com/jcsaaddupuy/)

