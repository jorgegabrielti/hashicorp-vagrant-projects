
# Criação de uma Vagrant Box para Sistemas Operacionais Linux **Debian like**

### Requisitos:
- Virtualbox instlado.
- Máquina virtual criada.
---

### Criação de redirecionamento de portas
Na VM -> Rede -> Adaptador 1 -> Redirecionamento de Portas:
- Rule 1:
	- **Protocolo**: TCP.
	- **Endereço IP do Hospedeiro**: 127.0.0.1.
	- **Porta do hospedeiro**: 2222;
	- **IP do Convidado**: 10.0.2.15.
	- **Porta do Convidado**: 22
---

### Atualização e requisitos iniciais

```bash
sudo apt update
sudo apt install -y build-essential linux-headers-$(uname -r) dkms
```
---
### Instalação dos Adicionais para convidados (Vbox Additionals)

#### Configurando editor default
```bash
sudo update-alternatives --config editor
```

Output:
```text
There are 4 choices for the alternative editor (providing /usr/bin/editor).

  Selection    Path                Priority   Status
------------------------------------------------------------
* 0            /bin/nano            40        auto mode
  1            /bin/ed             -100       manual mode
  2            /bin/nano            40        manual mode
  3            /usr/bin/vim.basic   30        manual mode
  4            /usr/bin/vim.tiny    15        manual mode

Press <enter> to keep the current choice[*], or type selection number:
```

Output:
```text
update-alternatives: using /usr/bin/vim.basic to provide /usr/bin/editor (editor) in manual mode
```
---
### Criação do usuário vagrant
```bash
sudo useradd vagrant 
```
---
#### Adicionando usuário vagrant no /etc/sudoers
```bash
sudo visudo
```

Inserir o trecho abaixo:
```text
vagrant ALL=(ALL) NOPASSWD:ALL
```
---
### Instalação da chave pública SSH
```bash
sudo mkdir -p /home/vagrant/.ssh

sudo wget --no-check-certificate https://raw.github.com/mitchellh/vagrant/master/keys/vagrant.pub -O /home/vagrant/.ssh/authorized_keys

sudo chmod 0700 /home/vagrant/.ssh

sudo chmod 0600 /home/vagrant/.ssh/authorized_keys

sudo chown -R vagrant.  /home/vagrant/.ssh
```

---
### Instalação e configuração do OpenSSH Server
```bash
sudo apt-get install -y openssh-server
sudo vi /etc/ssh/sshd_config
```
Ajustar conforme abaixo:
```text
Port 22
PubKeyAuthentication yes
AuthorizedKeysFile %h/.ssh/authorized_keys
PermitEmptyPasswords no
```

```bash
sudo systemctl restart ssh
sudo systemctl restart sshd
```

---
### Limpeza final
```bash
sudo apt autoremove
sudo apt autoclean
sudo apt clean

sudo dd if=/dev/zero of=/EMPTY bs=1M
sudo rm -f /EMPTY

$ sudo cat /dev/null > ~/.bash_history && history -c && exit
```
---
### Criando uma box com Vagrant
```bash
vagrant package --base <VM NAME> --output <NAME.box>
```

Output:
```text
==> <VM NAME>: Exporting VM...
```

Artefato output:
```text
<NAME>.box
```

#### Adicionando a box
```bash
vagrant box add --name <VM NAME> <NAME>.box
```

#### Listando a box
```bash
vagrant box list
```

---
### Checksum box
```bash
sha1sum <NAME>.box
```
---
### Upload da box para Vagrant Cloud

- Acesse https://portal.cloud.hashicorp.com/sign-in:
	- Create box
		- Create a Vagrant box
			- 1.Define your box
			- 2.Add a version
			- 3.Add providers
			- 4.Upload & Release