<style>
    code {
        color: red;
    }
</style>

# Welcome to the Left Forum Development Team!
This is Left Forum's development repository. This is where the members of and contributors to our organization actively maintain our site.

## Table of Contents
1. Setting Up Your Local Environment
2. Installing Drupal
3. Our Workflow

## Setting Up Your Local Environment
In order to get Drupal 8 up-and-running on your local machine, you will have to set up your local environment by installing some basic server technologies. The main ones required by Drupal are known collectively as the **AMP** stack. AMP stands for **Apache** (a web server), **MySQL** (a database management system), and **PHP** (a standard backend language). Together, these technologies manage most of the backend for typical websites. Variations of the AMP stack are supported by most modern and widely-distributed operating systems, giving you the freedom to work in whichever environment you are most comfortable in. However, each environment comes with a unique set of obstacles that you may encounter during the installation or development stages of the process. You should consider these when deciding which is the best solution for you.

### Linux
Drupal works best on a **Linux** operating system, and as such, working in Linux is the recommended option. Linux can be installed **_beside_** Windows or Mac via a dual boot solution. It can also be installed **_within_** Windows or Mac through a virtual machine (such as VirtualBox). Note that running an operating system within another (such as through a virtual machine) can be intensive for some computers and will always be less efficient than running Linux as a stand-alone or dual-booted operating system. Still, it may be a viable option for someone with a decently-performing computer not looking to mess with their boot configurations.

There are plenty of Linux distributions to choose from. **Ubuntu** is one of the more widely-used ones, but there are also many other distros available for free online.

Installing a **LAMP** (Linux AMP) stack is fairly straightforward for those familiar with Linux. For those unfamiliar, it involves typing commands into a _terminal_ to install the different components. It is highly recommended that you learn the basic commands to navigate through Linux's file system as well as some commonly-used tty programs. The time afforded by command-based tools is well worth the effort put into learning them. As a good start, learn how to install a LAMP stack by searching online for the necessary commands. Because Linux distro's use different packaging systems, these commands may vary based on your installation.

### Mac OS and Windows
AMP variants are available in other operating systems, including **WAMP** (Windows AMP), **MAMP** (Mac AMP), and **XAMPP** (the extra 'P' is for _Perl_). Some, including XAMPP, are available in both environments. Installers can be found to install all the necessary components at once. Note that not all of the underlying components are supported to the same extent as their Linux counterparts, and that the environments may work differently, lack certain features, or encounter exclusive errors.

## Installing Drupal
To practice with a Drupal sandbox, go to [Drupal.org][drupal] for installation instructions. The sandbox will allow you to test modules and themes and be able to experiment with Drupal core.

### Git
Once you are comfortable with the basics of Drupal, you can begin to work on our development project. You'll need to have **git** installed on your local machine. You should be able to install git from the _command-line_ (terminal) on Linux and Mac OS. For Windows, you can use **git bash** (a bash shell port with support for a small set of bash commands), any of the Linux distros available on the _Windows Store_ as part of the **Windows Subsystem for Linux** (WSL) project, or the Windows **GitHub Desktop App** (which offers a GUI interface for those not familiar or comfortable with the git shell-commands). To get our project, _clone_ it from the repository.

### Composer
Following Drupal's recommended development setup, we will be using **composer** to manage the packages used for our project, rather than installing/updating anything from the user interface. You will have to install composer _globally_ on your machine. You can see that after cloning our repository, _composer.json_ and _composer.lock_ files exist on the project root-directory. The .json file lists the required packages and their version constraints for running our site, while the .lock file keeps track of the actual versions of those packages being used. You won't have to modify these files directly as you will be using commands to add, update, and remove packages; and yes, you can do this in any environment. You may also notice that the .json file lists the default directories for Drupal packages, including separate directories for modules, themes, profiles, etc. Contributed packages (**contrib**) are also separated from **custom** packages. For our purposes, custom packages include our own original packages, unsupported versions of contrib packages, as well as modified contrib packages. Though the custom directories are defined within the .json file, these are merely included to show the distinction as we are not currently using composer to track these packages. Original and modified contrib packages cannot be tracked by composer as they are not included in drupal's nor a 3rd-party's package-directories. Unsupported packages can be tracked by composer by adding their repositories as package-directories, but this can get messy. So instead, we will let these packages be tracked by git as part of our project.

To add a package as a dependency and install it, you use the 'require' command:

`composer require <vendor>/<package>[:<version_constraint>]`

For Drupal packages, the vendor is 'drupal'. You are not usually required to include a version constraint, as denoted by the square-bracket syntax. The packages we have used have been listed in our .json file after running 'require'. In order to install them on your machine, you will need to run

`composer install`

from within the project root-directory.

### Drush
We will be using **drush** to manage drupal configurations from the command-line. This is more efficient than using the UI and we encourage you to learn some of the commands to apply to your own environment. We recommend installing drush _globally_ through composer. Use 'require' as before, and apply the 'global' modifier.

`composer global require drush/drush`

### Drupal Settup
Now you can start the drupal installation. Copy the _default.settings.php_ file in _sites/default/_ to a file named _settings.php_ in the same directory. Then you should be able to get on your browser and type in the address, which will probably be _localhost_ if your apache server is running on your local machine. This should redirect you to _&lt;host_name&gt;/core/install.php_ to begin the installation process. Follow the steps presented to finish the installation.

Now, in order to synchronize your configurations with those of the development server for this project, you will need to acquire the **uuid** assigned to the drupal installation on the server. To do this, ssh onto our server and run the following command under the project root-directory.

`drush config-get "system.site" uuid`

and then change your local installation's **uuid**:

`drush config-set "system.site" uuid <val>`

You will need to import the configuration changes the team has already made to our Drupal site. Our configurations are exported to the _sites/all/configuration_ directory. Run the following command to update your local site:

`drush config-import --source=sites/all/configuration`

If you get a message about needing to delete shortcut entities before importing, use this and try again:

`drush ev '\Drupal::entityManager()->getStorage("shortcut_set")->load("default")->delete();'`

## Our Workflow
Maintaining our site requires that everyone actively communicate with each other to discuss issues, solutions, and to coordinate tasks. Forgetting steps to our process along the way can easily lead to some nasty errors to resolve, which would affect not only you, but other team members as well. In the following sections, we detail the procedures we use to maintain consistent views across all of our environments.

### Pulling Updates
1. Pull code and configuration changes from GitHub:

    `git pull origin master`

2. If any changes have been made to the composer file, you will need to download, remove, or update the necessary packages based on the changes. Luckily, you can do this by running one command:

    `composer install`

3. If any changes were made to configuration settings, including enabling/disabling of modules or themes, you will need to import those changes to your local site:

    `drush config-import --source=sites/all/configuration`

### Pushing Updates
1. If you're adding a new package that can be tracked by composer, make sure to update the composer.json file by adding the package as a dependency:

    `composer require <vendor>/<package>[:<version_constraint>]`

Other useful composer commands you might want to look into: 'update' and 'remove'

2. If you've made any changes to your configuration, by enabling new modules for example, you must update the configuration directory to stage those changes via git.

    `drush config-export --destination=sites/all/configuration`

3. Now stage your changes, commit them, and push them onto our repository. If you're using the command-line for git, use the following commands:

    ```git add .
    git commit -m "some useful message"
    git push origin master```

[drupal]: https://www.drupal.org