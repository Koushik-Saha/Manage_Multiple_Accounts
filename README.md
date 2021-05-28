# Manage_Multiple_Accounts
How to manage multiple GitHub accounts on a single machine with SSH keysss


Check your `SSH KEY` with `ls -al ~/.ssh`

If `~/.ssh/id_rsa` is available, we can reuse it, 
or else we can first generate a key to the default `~/.ssh/id_rsa` by running:

```
ssh-keygen -t rsa
```

When asked for the location to save the keys, 
accept the default location by pressing enter.
 A private key and public key `~/.ssh/id_rsa.pub` 
 will be created at the default ssh location `~/.ssh/`.

Let’s use this default key pair for our personal account.

For the work accounts, we will create different SSH keys. 
The below code will generate the SSH keys, and saves the public 
key with the tag `email@work_mail.com` to `~/.ssh/id_rsa_work_user1.pub`

```
$ ssh-keygen -t rsa -C "email@work_mail.com"
```

after this command you need to enter the path and name of your ssh key 

Example for my local path 

```
/c/Users/User/.ssh/id_res_work_user1
```

Enter this path to create your work ssh key 

After add your work ssk key check it with `ls -al ~/.ssh`

copy your ssh key 

```
pbcopy < ~/.ssh/id_rsa.
```

```
1. Go to Settings
2. Select SSH and GPG keys from the menu to the left.
3. Click on New SSH key, provide a suitable title, and paste the key in the box below
4. Click Add key — and you’re done!
```

For the work accounts, use the corresponding public keys 
`(pbcopy < ~/.ssh/id_rsa_work_user1.pub)` and repeat the above steps 
in your GitHub work accounts.


To use the keys, we have to register them with the ssh-agent
on our machine. Ensure ssh-agent is running using the command 
`eval "$(ssh-agent -s)"`.
Add the keys to the ssh-agent like so:

```
eval "$(ssh-agent -s)"
```

```
1. ssh-add ~/.ssh/id_rsa
2. ssh-add ~/.ssh/id_rsa_work_user1
```


Here we are actually adding the SSH configuration rules
 for different hosts, stating which identity file to use for which domain.

The SSH config file will be available at `~/.ssh/config`. 
Edit it if it exists, or else we can just create it.

```
$ cd ~/.ssh/
$ touch config           // Creates the file if not exists
$ code config            // Opens the file in VS code, use any editor

```

Make configuration entries for the relevant GitHub accounts similar to the one below in your `~/.ssh/config` file:


```
# Personal account, - the default config
Host github.com
   HostName github.com
   User git
   IdentityFile ~/.ssh/id_rsa
   
# Work account-1
Host github.com-work_user1    
   HostName github.com
   User git
   IdentityFile ~/.ssh/id_rsa_work_user1

```

`work_user1` is the GitHub user id for the work account.

`github.com-work_user1` is a notation used to differentiate the multiple Git accounts. You can also use “work_user1.github.com” notation as well. Make sure you’re consistent with what hostname notation you use. This is relevant when you clone a repository or when you set the remote origin for a local repository

The above configuration asks ssh-agent to:

Use `id_rsa` as the key for any Git URL that uses `@github.com`
Use the `id_rsa_work_user1` key for any Git URL that uses `@github.com-work_user1`


`ssh-add -l` will list all the SSH keys attached to the ssh-agent. Remove all of them and add the one key you are about to use.

If it’s to a personal Git account that you are about to push:

```
$ ssh-add -D            //removes all ssh entries from the ssh-agent
$ ssh-add ~/.ssh/id_rsa                 // Adds the relevant ssh key
```


To push to your work GitHub account-1, change the SSH key mapped with the ssh-agent by removing the existing key and adding the SSH key mapped with the GitHub work account.


```
$ ssh-add -D
$ ssh-add ~/.ssh/id_rsa_work_user1
```

Sometimes you have issue wtih authntication of github then you sould follow this step:

```
1. eval "$(ssh-agent -s)"
2. $ ssh-add -D            //removes all ssh entries from the ssh-agent
3. $ ssh-add ~/.ssh/id_rsa                 // Adds the relevant ssh key
4. $ ssh-add ~/.ssh/id_rsa_work_user1
```

Then use `git push` for push your code to your spacific account it will automaticaly push your relevant code your relevant account 


To list the config name and email in the local Git directory,
do `git config user.name` and `git config user.email`. 
If it’s not found, update accordingly.

```
git config user.name "User 1"   // Updates git config user name
git config user.email "user1@workMail.com"

```
