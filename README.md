# Symfony2-Ansible deploy
## tl;dr
An Ansible playbook for deploying Symfony2 apps, with a lot of features and sensible defaults.

## The long story
The project is an [Ansible](http://www.ansible.com/) playbook to easily deploy [Symfony2](http://symfony.com/) apps, it takes its inspiration from some projects hat you can find on the net, our main credits go to [servergrove/ansible-symfony2](https://github.com/servergrove/ansible-symfony2).

We made that playbook to fit our needs, specifically the main features are:
- Self-contained project, all you need is the **ansible** folder, everything is there.
- Deploy from the git repo of the code, of course you can choose witch branch/tag/version to deploy.
- Support for multiple environment of the app, you can deploy a production version, a testing, a demo, etc... whatever you need !
- Support for multiple releases of the same environment of the the app, switch between them by simply changing a symlink. You can also choose how many older version of the app you want to keep.
- You can choose the name of the folder in which an app version will be deployed, otherwise the playbook will create a directory name from a timestamp.
- You can specify the Symfony's _console_ command location and the Symfony's _log_ directory, this makes us [Symfony3](http://symfony.com/blog/symfony-3-0-the-roadmap) compliant !
- Standard defaults are provided for all config options, you can run the playbook without overriding any of them, you'll get the work done !

These and other features can be configured via Ansible options, for a full list see below, in the configuration section.

## Installation
Just clone the repository and put the _ansible_ folder in your Symfony2 project, we suggest `app/Resources/ansible`, but any location will be fine, depending on your needs.

## Configuration
Configuration is easy, basically you need to put your environment setting in the following files:

### hosts
You've guessed it, it's the Ansible _host_ file, put your deployment servers there.

### deploy.yml
There you can find some of the most used settings, specifically:

```
app_remote_env: testing # Default environment if none is specified.
sf2_project_env: prod # Symfony's environment.
sf2_project_name: MyAwesomeProject # Project name.
sf2_project_repo: https://github.com/spectre/MyAwesomeProject.git # Project Git repo.
sf2_project_branch: develop # Default source branch to deploy, if none is specified.
ansible_ssh_user: root # Ansible remote user.
```

### ansible/roles/deploy/defaults/main.yml
Here are the other settings:

```
sf2_project_release: ~ # Project release name for the given environment, if non is specified a timestamp will be used.
sf2_project_php_path: php # Php executabile path.
sf2_project_keep_releases: 3 # How many old releases to keep online.
sf2_project_clean_versioning: true
sf2_project_console_opts: '' # Symfony console options.
sf2_project_composer_opts: '--no-dev --optimize-autoloader --no-interaction' # Composer options.
sf2_fire_migrations: false # Run Db update after deploy.
sf2_console_path: bin/console # Symfony console command path.
sf2_logs_path: var/logs # Symfony logs directory.
sf2_project_composer_path: "{{ sf2_project_root }}" # Composer path.
```

### ansible/roles/deploy/defaults/vars
This folder holds the configuration for your various environments, there can be as many as you need, just name them properly and refer to them when you run the playbook. Two sample environments are given, here's a shot of the _prod.yml_ confguration:

```
# Default DB Parameters
db_driver: pdo_mysql
db_host: project-prod-db-host
db_port: null
db_name: project-prod-db-name
db_user: project-prod-db-user
db_password: project-prod-db-password

# Other sensible paramenters
mailer_transport:  smtp
mailer_host:       127.0.0.1
mailer_user:       ~
mailer_password:   ~

locale:            en
secret:            OMyGodIMSecret!

debug_toolbar:          true
debug_redirects:        false
use_assetic_controller: true

# Project remote folder
sf2_project_root: /home/vhosts/project-prod # Project destination directory.
```

## Usage
Run the playbook as usual:

```
ansible-playbook -l webservers -i app/Resources/ansible/hosts app/Resources/ansible/deploy.yml -v -e "app_remote_env=dev"
```

The above example runs the playbook and specifies using the dev environment.

## Todo
- Better docs !
- Future enanchement may include the encapsulation of the playbook into a bundle or as a Gulp task,
- make your suggestion !

## Contributing
Feedback, PR and suggestions are welcome !
