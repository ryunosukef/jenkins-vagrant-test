vagrant, chef, serverspec, jenkins
=====================

[Vagrant + Chef Solo + serverspec + Jenkins でサーバー構築を CI](http://d.hatena.ne.jp/naoya/20130520/1369054828) を参照しながら、CIを実践してみよう


vagrant up
---------------
```
$ vagrant up local
```

ssh config
---------------
```
$ vagrant ssh-config local --host=jenkingrant > vagrant-ssh.conf
```


gem
---------------
chefのバージョンは、11.8ではなく、11.6固定とする

```
$ cat Gemfile
source 'https://rubygems.org'

gem "chef", "11.6.0"
gem "knife-solo", "~> 0.3.0"
gem 'serverspec'
gem 'rake'
gem 'ci_reporter'
```

```
$ bundle
Using rake (10.1.0) 
Using builder (3.2.2) 
Using erubis (2.7.0) 
Using highline (1.6.20) 
Using json (1.7.7) 
Using mixlib-log (1.6.0) 
Using mixlib-authentication (1.3.0) 
Using mixlib-cli (1.3.0) 
Using mixlib-config (2.0.0) 
Using mixlib-shellout (1.2.0) 
Using net-ssh (2.7.0) 
Using net-ssh-gateway (1.2.0) 
Using net-ssh-multi (1.1) 
Using ipaddress (0.8.0) 
Using systemu (2.5.2) 
Using yajl-ruby (1.1.0) 
Using ohai (6.20.0) 
Using mime-types (2.0) 
Using rest-client (1.6.7) 
Using chef (11.6.0) 
Using ci_reporter (1.9.0) 
Using diff-lcs (1.2.5) 
Using knife-solo (0.3.0) 
Using rspec-core (2.14.7) 
Using rspec-expectations (2.14.4) 
Using rspec-mocks (2.14.4) 
Using rspec (2.14.1) 
Using serverspec (0.11.4) 
Using bundler (1.3.5) 
Your bundle is complete!
```

knife solo prepare
-------------

```
$ bundle exec knife solo prepare jenkingrant -F vagrant-ssh.conf
Bootstrapping Chef...
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
101  6790  101  6790    0     0   1005      0  0:00:06  0:00:06 --:--:-- 10812
Downloading Chef 11.6.0 for el...
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 22.6M  100 22.6M    0     0   349k      0  0:01:06  0:01:06 --:--:--  532k
Installing Chef 11.6.0
warning: /tmp/tmp.fnmqhIkv/chef-11.6.0.x86_64.rpm: Header V4 DSA/SHA1 Signature, key ID 83ef826a: NOKEY
Preparing...                ########################################### [100%]
   1:chef                   ########################################### [100%]
Thank you for installing Chef!
```

knife solo cook
--------------

```
$ bundle exec knife solo cook jenkingrant -F vagrant-ssh.conf
Running Chef on jenkingrant...
Checking Chef version...
Uploading the kitchen...
Generating solo config...
Running Chef...
Starting Chef Client, version 11.6.0
Compiling Cookbooks...
Converging 2 resources
Recipe: nginx::default
  * package[nginx] action install
    - install version 1.0.15-5.el6 of package nginx

  * service[nginx] action enable
    - enable service service[nginx]

  * service[nginx] action start
    - start service service[nginx]

Chef Client finished, 3 resources updated
```

rake spec
---------------

```
$ bundle exec rake ci:setup:rspec spec
.....

Finished in 0.35925 seconds
5 examples, 0 failures
```

delete ssh config
---------------

```
$ rm -f vagrant-ssh.conf
```

vagrant destroy
---------------

```
$ vagrant destroy -f local
[local] Forcing shutdown of VM...
[local] Destroying VM and associated drives...
```

vagrant status
---------------

最後にVMが起動していないことを確認する

```
$ vagrant status
Current machine states:

local                     not created (virtualbox)
remote                    not created (virtualbox)

This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.
```