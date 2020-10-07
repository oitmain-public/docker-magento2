Inspired and copied most of the code from https://hub.docker.com/r/alexcheng/magento2/ and https://github.com/yiisoft/yii2-docker

## Quick start

Get your repo.magento.com authentication keys handy. https://devdocs.magento.com/guides/v2.4/install-gde/prereq/connect-auth.html

Start the docker container

    docker-compose up -d
    
Run the installation
    
    # download magento using composer
    docker-compose exec web download-magento
    
    # install magento through cli. check .env for variables
    docker-compose exec web install-magento
    
Optional commands

    # (optional) install sample data
    docker-compose exec web install-sampledata
    
    # (optional) run this command to clean the cache and re-build magento 2 during development
    docker-compose exec web rebuild-magento

You can access the files in `src` folder, and access the application through the following URL

    http://127.0.0.1/
    
    http://127.0.0.1/admin
    
PhpMyAdmin

    http://127.0.0.1:8888/
    
How to reload apache

    docker-compose exec web service apache2 reload

**NOTES:** 
- Minimum required Docker engine version `17.04` for development (see [Performance tuning for volume mounts](https://docs.docker.com/docker-for-mac/osxfs-caching/))
- The default configuration uses a host-volume in your home directory `.docker-composer` for composer caches
- Magento2 will be mounted to `src` folder
- Xdebug can be enabled in `php.ini`, reload apache to apply changes. Debug port is 9005 

## Configuration

For admin username and password, please refer to the file `.env`. You can also change the file `.env` to update those configurations. Below are the default configurations.

### Database

The default `docker-compose.yml` uses MariaDB as the database.

## Windows WSL2 Permission Issue
Credit: https://www.reddit.com/r/bashonubuntuonwindows/comments/d4z0tz/can_not_edit_etcnginxnginxconf_from_windows/

Access to wsl$ is provided by the (proprietary) init process in WSL which runs as root, and therefore has full access to all WSL files. However init intentionally limits permissions via wsl$ according to whatever the default user is for the distro. One workaround is to change this default user to root.

- Exit from all WSL windows.
- Open a Command Prompt (not WSL).
- Wait for WSL to terminate. Run wsl --list --running in the cmd prompt until it's no longer listed, or force it to close with wsl --terminate.
- Change the default user with `ubuntu config --default-user root`
- Check to see if the change went through. Run wsl mount and look at the last line. If the change went through, it should look something like this: `C:\ on /mnt/c type drvfs (rw,noatime,uid=0,gid=0,case=off)`
  - Notice the uid=0,gid=0 part—this indicates the default user was changed. If it says uid=1000,gid=1000 (I've had this happen before, I don't know why) then return to step 3.

Optional steps
- Create a new shortcut to open WSL with your non-root username. Assuming that username is myuser, create a shortcut to start WSL which points to C:\Windows\System32\wsl.exe ~ -u myuser.
- If you want to return the default owner of files inside /mnt/c back to your non-root user, you'll need to update the WSL config file. Inside WSL, run: `echo -e '[automount]\noptions = "uid=1000,gid=1000"' | sudo tee -a /etc/wsl.conf` (this assumes your shell is bash).
