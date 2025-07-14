# Instalando adicionais para convidados em Sistemas Debian Like

```bash
sudo apt update
sudo apt install -y build-essential linux-headers-$(uname -r) dkms
```

```bash
sudo mkdir /mnt/cdrom
```

```bash
sudo mount /dev/sr0 /mnt/cdrom
cd /mnt/cdrom
sudo ./VBoxLinuxAdditionals.run
```