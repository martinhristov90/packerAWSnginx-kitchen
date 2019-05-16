# How to create a AMI image with Packer providing Nginx webserver and tested with KitchenCI.

# Purpose:

This repository's sole purpose is to demonstrate how to built a AWS with Packer providing Nginx webserver tested with KitchenCI.

# Technologies in use :

- Packer
- Vagrant
- VirtualBox
- Nginx
- KitchenCI

# How to install the pre-requisites:

- [How to install Vagrant](https://www.vagrantup.com/docs/installation/)
- [How to install VirtualBox](https://www.virtualbox.org/manual/ch02.html)

# How to use:

- Execute `git clone https://github.com/martinhristov90/packerAWS.git`.
- Put your AWS credentials in `~/.aws/credentials`, this file should look like this :
    ```
    [default]
    aws_access_key_id = YOUR_KEY
    aws_secret_access_key = YOUR_SECRET_KEY
    ```
- Change into directory packerAWS with `cd packerAWS`.

# Setup KitchenCI:

- For using [KitchenCI](https://kitchen.ci/), ruby environment needs to be set up first.
- Run `brew install ruby`
- After previous command finish, run `gem install rbenv`, this would give you ability to choose particular version of Ruby. This is a prerequisite.
- Next, [Bundler](https://bundler.io) needs to be installed, run `gem install bundler`, this would provide the dependencies that KitchenCI needs. It is going to install the Gems defined in the `Gemfile`
- Run the following two commands, to setup Ruby environment for the local directory.
    ```bash
    echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
    echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
    ```
- Reload your BASH interpreter or apply the changes to the profile :
    ```shell
    source ~/.bash_profile 
    ```
- Verify rbenv is installed properly with :
    ```shell
    type rbenv   # → "rbenv is a function"
    ```
- To install the particular version that we need, run the following command in the project directory:
    ```shell
    rbenv install 2.5.3
    ```
- Set local version to be used with command :
    ```shell
    rbenv local 2.5.3
    ```
- Previous step is going to create a file named .ruby-version, with the following content `2.5.3`
- Install the needed Gems for KitchenCI using Bundle with command :
    ```shell
    bundle install
    ```
- You should now have KitcheCI setup, now you need to specify the region and the availability zone that the AMI you want to test is located, replace the values below in templateAWS.json file with the ones valid for your AMI : 
    ```shell
      region: us-east-1 --> CHANGE IT TO FIT YOUR NEEDS
      availability_zone: us-east-1d --> CHANGE IT TO FIT YOUR NEEDS
    ```
- Create a [SSH key pair in AWS](https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#key-pairs), set `.pem` file in the templateAWS.json with follwoing command: 
    ```shell
    ln -s PATH_TO_YOUR_PEM_FILE key.pem
    ```
- Run Kitchen in the context of Bundle with the following command : 
    ```shell
    bundle exec kitchen list
    ```
- Build the testing environment with:
    ```shell
    bundle exec kitchen converge
    ```
- Test it : 
    ```shell
    bundle exec kitchen verify
    ```
- The output should look like this :
    ```shell
    Profile: tests from {:path=>"/Users/martinhristov/Tasks/PackerLearn/packerAWSnginx-kitchen/test/integration/default"} (tests from  {:path=>".Users.martinhristov.Tasks.PackerLearn.packerAWSnginx-kitchen.test.integration.default"})
    Version: (not specified)
    Target:  ssh://ubuntu@ec2-54-211-91-208.compute-1.amazonaws.com:22
    
      System Package nginx
         ✔  should be installed
    
    Test Summary: 1 successful, 0 failures, 0 skipped
           Finished verifying <default-packer-AWS> (0m5.80s).
    -----> Kitchen is finished. (0m9.76s)
    ```
- To destroy the testing environment run :
    ```shell
    bundle exec kitchen destroy
    ```
