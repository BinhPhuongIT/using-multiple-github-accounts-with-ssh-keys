
## Problem
I have two Github accounts: *binhphuongit* (personal) and *lebinhphuong-tech* (for work).
I want to use both accounts on same computer (without typing password everytime, when doing git push or pull).

## Solution
Use ssh keys and define host aliases in ssh config file (each alias for an account).

## How to?
1. [Generate ssh key pairs for accounts](https://help.github.com/articles/generating-a-new-ssh-key/) and [add them to GitHub accounts](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/).

2. Edit/Create ssh config file (`~/.ssh/config`):

   ```conf
     # Default github account: oanhnn
     Host *
      StrictHostKeyChecking no

    # Default github account: binhphuongit
    Host github.com
       HostName github.com
       IdentityFile ~/.ssh/id_rsa
       IdentitiesOnly yes
    
    # Other github account: lebinhphuong-tech
    Host github-lebinhphuong-tech
       HostName github.com
       IdentityFile ~/.ssh/2024/id_rsa
       IdentitiesOnly yes
   ```
   
   > NOTE: If you use any account frequently, you should use the default hostname (`github.com`).
   
3. [Add ssh private keys to your agent](https://help.github.com/articles/adding-a-new-ssh-key-to-the-ssh-agent/):

   ```shell
   $ ssh-add ~/.ssh/oanhnn_private_key
   $ ssh-add ~/.ssh/superman_private_key
   ```

4. Test your connection

   ```shell
   $ ssh-keyscan github.com >> ~/.ssh/known_hosts
   $ ssh -T git@github.com
   $ ssh -T git@github-lebinhphuong-tech
   ```

   If everything is OK, you will see these messages:

   ```shell
   Hi BinhPhuongIT! You've successfully authenticated, but GitHub does not provide shell access.
   ```
   
   ```shell
   Hi github-lebinhphuong-tech! You've successfully authenticated, but GitHub does not provide shell access.
   ```

5. Now all are set, you need remeber 

   ```
   git@github-lebinhphuong-tech:org/project.git => user is lebinhphuong-tech
   git@github.com:org/project.git.     => user is binhphuongit
   ```

  - If you need clone a repository, just do:

   ```shell
   $ git clone git@github-lebinhphuong-tech:org1/project1.git /path/to/project1
   $ cd /path/to/project1
   $ git config user.email "lebinhphuong-tech@example.com"
   $ git config user.name  "lebinhphuong-tech"
   ```

   - If you already have the repo set up, after the ssh config instructions, you need change the URL of `origin`, just do:

   ```
   $ cd /path/to/project2
   $ git remote set-url origin git@github-lebinhphuong-tech:org2/project2.git
   $ git config user.email "lebinhphuong-tech@example.com"
   $ git config user.name  "lebinhphuong-tech"
   ```

   - If you are creating a new repository on local:

   ```
   $ cd /path/to/project3
   $ git init
   $ git remote add origin git@github-lebinhphuong-tech:org3/project3.git
   $ git config user.email "lebinhphuong-tech@example.com"
   $ git config user.name  "lebinhphuong-tech"
   $ git add .
   $ git commit -m "Initial commit"
   $ git push -u origin master
   ```

Done! Goodluck!

## Addon: 

The bash script that prompts for your git account.

   ```
   #!/bin/bash

   # silent prompt
   read -p 'GIT profile: ' profile

   # switch
   case $profile in
     superman)
       git config user.email "lebinhphuong-tech@example.com"
       git config user.name "lebinhphuong-tech" 
       git config user.signingKey "lebinhphuong-tech_gpg_public_key"
       ;;
     oanhnn)
       git config user.email "binhphuongit@example.com"
       git config user.name "binhphuongit" 
       git config user.signingKey "binhphuongit_gpg_public_key"
       ;;
     # default case: raise error
     *)
       >&2 echo "ERR: Unknown profile: $profile"
       exit 1
   esac
   ```
