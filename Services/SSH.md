## Introduction

## Install SSH in Linux
``` bash
sudo apt update
sudo apt install openssh-server -y
```
Check ssh service `sudo systemctl status ssh`

`sudo sshd -T`

``` bash
sudo sshd -T | grep -E '(password|permit)'
permitrootlogin without-password
passwordauthentication yes
permittty yes
permituserrc yes
permitemptypasswords no
permittunnel no
permitopen any
permitlisten any
permituserenvironment no
```
Vị trí file cấu hình ssh 
``` bash
 /etc/ssh/sshd_config (server)
 ~/.ssh/config (client)
```
`sudo nano /etc/ssh/sshd_config`

sau khi chỉnh sửa `sudo systemctl restart ssh`

## Configuring SSH key

