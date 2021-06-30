
# Installation

## Windows

## Linux

# Configuration

## Windows

## Linux

## Add ssh keys to GitLab

- Test the ssh connection
shh -Tvvv git@git-repo



- Set putty as the ssh client for git/.
setx GIT_SSH "C:\Program Files\PuTTY\plink.exe"

git where
C:\Users\dayres\AppData\Local\Programs\Git\cmd\git.exe 


# Setup remote access to ATH server

create an authorized_keys file in the ~/.ssh directory on the server.
copy the public key from the machine you are using to access the ATH server and append it to the authorized_keys file.
set the permission on the authorized_keys file to 600


# Useful sites

https://kb.iu.edu/d/aews