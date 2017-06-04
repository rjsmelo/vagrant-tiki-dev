## TikiWiki Basic Dev Box

**!! Warning !!**

**This Vagrant box was designed to be run as a development environment, not as a production box**

**!! Warning !!**

### Usage

* Clone this repo to your disk
* Do ```vagrant up```
* You can check the box IP after boot by issuing ```vagrant ssh -c ifconfig```
* The base folder of this repo will be shared as ```/vagrant``` inside the box
* The content of the folder ```webroot``` will be exposed as the webroot of Apache
* The DB will have a admin user created, username: ```admin```, password: ```MysqlBoxPassword```

#### Getting TikiWiki running from SVN

You need to drop the tiki distribution inside the ```webroot``` folder, for instance if you want to run the ```trunk``` branch using SVN, you can do:

```
vagrant ssh
cd /vagrant/webroot/
svn checkout https://svn.code.sf.net/p/tikiwiki/code/trunk trunk
cd trunk
bash setup.sh composer
```

After you can point your browser to ```http://<IP of Vagrant Machine>/trunk``` and use Tiki as normally

That way you can repeat for ```17.x```, ```16.x```, etc.

### Requirements

You need a working copy of  [VirtualBox](https://www.virtualbox.org/) and [Vagrant](https://www.vagrantup.com/)

### Contributions

If you find any problem, or want to contribute/extend this repo, just:

* Clone the repo on GitHub
* Make your changes on your copy
* Submit a Pull Request

I'll do my best to review and apply your contribution.

Thank you!
