# Cloud Computing and AWS

- [2 Tier Deployment on AWS](https://github.com/khanmaster/devops_bootcamp_2Tier_deployment_AWS/blob/main/2-Tier-app-deployment.md)
- [AWS S3](https://github.com/khanmaster/devops_bootcamp_2Tier_deployment_AWS/blob/main/S3.md)
- [CICD with Jenkins](https://github.com/khanmaster/devops_bootcamp_2Tier_deployment_AWS/blob/main/jenkins_deployment.md)
- [DevOps with CICD and Jenkins](https://medium.com/@ahshahkhan/devops-culture-and-cicd-3761cfc62450)
- [Markdown Guide](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet#links)

# Simple Deployment


## Pre-requisites

* set up with an IAM account


In the previous lesson we built an "environment" for the developers to work in by creating virtual machines with all the software they need to build and run their applications.

Now we want people to be able to use it. This is the basic idea of deployment. How can we get our app in to the hands of the people we want using it?

For web applications ( which we'll be building ) this means getting your code on to a server that is publically visible. But there are other types of deployment too.

In our case we aren't ready for a fully Continuously Deployed system yet so we're going to push it to an environment that we'll call "UAT" `User acceptance Testing`.

This will be a dummy server that is accesible from the web but it won't be public. This server will be used for testing by our imaginary manual testing teams.

You may remember from the development environment lesson that one of the principles ideas was that all the places that the code needs to run should be as close to identical as possible. This was to avoid the dreaded "it works on my machine" error.

So our goal for this lesson is to recreate our development environment on a machine that can be seen from the web. For this we'll be using AWS.

## AWS

AWS stands for Amazon Web Services. It is a huge set of hardware and services that we can use to build an infrastructure for our app. It's pretty enormous and can do hundreds of things.

We will be using it to create virtual machines that match our dev environment. But these virtual machines will be live to the world. It is definitely not the only service that we could use and you will see many others as well.

Let's get started.

You will have all been set up with an account that allows you to access the Sparta area of AWS. You can log in here:


While the steps for creating a virtual machine on AWS will be different than those for Vagrant the concepts are the same. Try to match them up with what we did in the last lesson.

### Launch an instance

Click on the services tab at the top of the homepage. As you can see AWS has a lot of services that it can offer. The one we want is called EC2 so choose that.

EC2 is Amazon's service for renting hardware and configuring it to your needs. It is very flexible as it allows you to rent hardware for any period of time. We can create and destroy instances as we need them with no penalties. This is why it is incredibly popular.

On the EC2 page click the "Launch Instance" button.

#### Which operating system

The first step is to choose the operating system. On the development environment we are running Ubuntu 14.04 64Bit. This option is close to the bottom of the list of environments.

#### Hardware

The next step is to choose the power of the machine. How many CPUs do we need and how much RAM. We didn't make this choice for our development environment. The more powerful the box the faster it will run. But also the more it will cost.

Choose the t2.micro option as it is more than sufficient for our needs.

**Make sure you click next and not Launch as this will skip the remaining steps**

> NOTE : Power vs Cost is a common trade off for DevOps engineers.

#### Instance Details

There are quite a few options here that we could use in more complex architectures but for now all the default options are fine.

Do make sure you choose "Protect against accidental termination", however. This will ensure that you cannot accidently delete your instance.

> NOTE : After this is checked only the administrators will be able to terminate an instance

Next!

#### Storage

Most machines will need some storage. This is no different from the hard drive on your computer except that there is a good chance it will not be physically connected to our machine.

Leave the default at 8GB. This is more than enough.

Next!

#### Tags

Tags allow us to put our instances in to groups to make our lives easier. Make sure your instance has a "devops_bootcamp_yourname" tag

> NOTE : Make sure you do this correctly. We have set up permissions such that you only have control over instances that have your name on them.

#### Security Groups

This is another step that we skipped in the dev environment. This is a firewall. Here we can choose which IP address are allowed to access our machine and on which ports.

Because we'll be using it for testing we'll need HTTP access ( PORT 80 ) but we'll restrict this to only people who are physically in our building. We'll need SSH access to be restricted too. SSH access you will remember is how we control and work with out box so this is an important safety feature.

Check "Create a new security group"

> NOTE : We can create more complex security groups and access rights but this is the minimal configuration for a websever

You should have created two rules. One that opens connections on port 80 with a source of "My IP" and another that open port 22 with the source set to "My IP".

> QUESTION: Will you be able to SSH to your instance from home?

As we have restricted port 22 to only allow connections from the Academy IP you will be blocked if you try to access it from home!

Time to launch!

#### Review and Launch

Take a moment to review your instance details. We have restricted most of the options you are allowed to choose but in the real world it's perfectly possible to accidently spin up a very expensive machine. When you are ready click "Launch".

#### SSH Key Pairs

When we log on to just about anything these days we need a username and password. When it comes to infrastructure however this is not always practical. So instead we use a concept called a public and private key. Anyone that has a copy of the public key can log in to the instance that accepts the corresponding private key.


Check the box to say that you have access to the key. Which you actually don't yet...


Do not share this key with anyone else and do not email it to anyone.

You can now finish launching your instance.

If all goes well you should see the message

"Your instances are now launching"

## SSH to your machine

When you machine has finished loading you can log on to the box. In our dev environment Vagrant handled using SSH to log on to the box for us with a simple command ``vagrant ssh``. On the live box we will have to do this ourselves. For this we need to two things:

* The Public key ( which you now have )
* The IP address or hostname of the machine

Click on your machine in the list and you should see it's details at the bottom of the screen. Find the "Public DNS" section and copy the address.

> NOTE : You can also use the public IP if you prefer but it can change if you restart your box so be careful.

In your terminal type the following using your public DNS name or IP:

```bash
ssh  -i ~/.ssh/file_name.pem ubuntu@yourpublicdnshere
```

This command opens a connection to the server at your IP using a username of ubuntu and a password of sorts provided by the SSH key. But the first time we try this we'll get an error.

SSH is very security conscious. The error message is telling you that SSH isn't happy with how many people on your system have access to your key. It's a private key so it should be private!

To fix this error we need to change the permissions so that key is only readable by the user that owns it ( us ).

```
chmod 400 ~/.ssh/file_name.pem
```

Ok now that's done we can try again.

If all goes well you will most likely see a message like this:

```bash
The authenticity of host 'ec2-52-210-182-187.eu-west-1.compute.amazonaws.com (52.210.182.187)' can't be established.
ECDSA key fingerprint is SHA256:t29QfsK5Payj7DH6trVF5GmUi4IWDxpx5UsMJ4UDRHE.
Are you sure you want to continue connecting (yes/no)?
```

This is SSH warning you that it can't authenticate your host automatically. But that's ok. We are happy so type "yes".

> NOTE: Remember this step. Any steps that require you to type yes or no can cause problems with automation. We'll look at ways of skipping this later on.

If all goes well you should now have a command prompt just like in vagrant and just like on your machine.

```bash
ubuntu@ip-172-31-45-161:~$
```
#### Next

- Transfer data from your Machine to AWS EC2 instace
- `scp -i "file_name.pem" -r /local/directory/ ec2@ec2_ip:/remote/directory/`

- [Next steps for 2 Tier Deployment](https://github.com/khanmaster/devops_bootcamp_2Tier_deployment_AWS/blob/main/2-Tier-app-deployment.md)
