# Rear Window
Outsourcing the suspicious neighbor to machines

## Description
One machine (let's name him Jeff) sits at the window and takes pictures.
If something happens Jeff tells another machine (Let's call her Lisa).
Lisa takes a look at the picture and tells everyone about what they saw.

## Technical Description
A Raspberry Pi with a webcam uploads photos to a webserver - if there is enough change to the previous one.
The webserver analyses the picture with [Google's Tensorflow](https://www.tensorflow.org/versions/r0.7/tutorials/image_recognition/index.html) and tweets the best match.

## Possible improvements
- Tweet sentences: Provide a dictionary with some randomly picked sentence: e.g. 'Just saw a {{word}} passing by.' or 'Oh look, a {{word}}!'
- AllInOne: Due to hardware limitiations it's currently not possible to install tensorflow on a raspberry - See [this issue](https://github.com/tensorflow/tensorflow/issues/254) for details.

## Requirements

### Photo collection - Jeff
-) Raspberry Pi or similar device
-) cheap USB-webcam

### Image processing and publishing - Lisa
-) a webserver (Let's name her Lisa) for processing

## Configuration

### Generate a passphrase for secure storage
```
cd <<project_source>>
openssl rand -base64 64 > .vault_passphrase
```
Create the vault - delete the existing file if necessary
```
ansible-vault create group_vars/jeff/vault.yml
```
Add this content to `group_vars/jeff/vault.yml`:
```
---
vault_wifi_name:
vault_wifi_passphrase:
vault_lisa_token:
```

### Configure the raspberry
- Install the [default raspbian-image](https://www.raspberrypi.org/downloads/raspbian/)
- Find out the raspberry's ip address
- I strongly recommend to [enable passwordless SSH access](https://www.raspberrypi.org/documentation/remote-access/ssh/passwordless.md) on the raspberry and disable password-based login: `sudo vi /etc/ssh/sshd_config` and set `PasswordAuthentication no`
- Set the found ip address as the ip address of jeff in the file named `hosts`.
- If you want to enable wifi, set the name and passphrase in `ansible-vault edit group_vars/jeff/vault.yml`

## Installation/Update
```
ansible-playbook jeff.yml
```
