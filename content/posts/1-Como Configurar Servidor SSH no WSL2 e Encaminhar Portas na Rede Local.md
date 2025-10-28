---
title: "Como Configurar Servidor SSH no WSL2 e Encaminhar Portas na Rede Local"
draft: false
type: docs
tags: ["SSH", "Servidor", "WSL2", "Ubuntu"]
date: 2025-10-27 00:00:00.000 -0300
---

Servidor SSH no WSL2 (Ubuntu): como configurar e encaminhar portas na Rede LAN (Local) — guia passo a passo

{{< youtube "TSDQpz8iRdc">}}

Neste post demonstro como configurar um servidor SSH no WSL2 usando a distribuição Ubuntu e a forma correta de expor o serviço na sua rede LAN.

Futuramente pretendo publicar outro post ensinando como se conectar ao servidor mesmo estando fora da rede local, de modo que, independentemente da rede à qual você esteja conectado, você tenha acesso ao seu servidor e possa executar ou gerenciar suas aplicações, containerizadas ou não.

Pessoalmente, prefiro trabalhar com containers. Observação: o WSL2 funciona integrado ao Docker Desktop, o que facilita bastante — não é necessário instalar o Docker Engine dentro do subsistema para utilizá‑lo.

Também fiz um vídeo explicando todos os comandos de maneira mais detalhada: [Como criar servidor ssh com wsl+ubuntu](https://youtube.com/ofcoliva/video_id)

## Instalando Ubuntu
```powershell
wsl --install -d Ubuntu
```

## Instalando o OpenSSH Server
```bash
sudo apt update
sudo apt install openssh-server
```

## Adicionando systemd à inicialização
```bash
sudo nano /etc/wsl.conf
```
Cole o código abaixo no arquivo wsl.conf:
```ini
[boot]
systemd=true
```

## Editando a configuração do servidor SSH
Descomente a linha Port e ajuste as demais configurações pertinentes do servidor SSH; altere a porta para a mesma que será redirecionada na interface de rede e, posteriormente, liberada no firewall:
```bash
sudo nano /etc/ssh/sshd_config
```

## Criando diretório para configurações do socket SSH (se necessário)
```bash
sudo mkdir -p /etc/systemd/system/ssh.socket.d
```

## Editando listen.conf (somente se o Ubuntu for 22.10 ou posterior)
```bash
sudo nano /etc/systemd/system/ssh.socket.d/listen.conf
```

## Ouvindo em uma porta específica
Adicione o código abaixo ao listen.conf (somente se o Ubuntu for 22.10 ou posterior):
```ini
[Socket]
ListenStream=[WSL_PORT]
```

## Reiniciando o daemon do systemd
```bash
sudo systemctl daemon-reload
```

---
---
---

## Conectando ao subsistema Linux (do próprio host)
```powershell
ssh user@localhost -p [WSL_PORT]
```

<a id="aparecera-a-seguinte-mensagem"></a>
 Aparecerá a seguinte mensagem
```text
The authenticity of host '[localhost]:[PORT] ([127.0.0.1]:[PORT])' can't be established.
ED25519 key fingerprint is SHA256:7OmoApSrmRvZR4aSdf98712NSPQIGepNkqu9GYOMWewE.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Digite: yes

---

Após digitar sua senha, aparecerá uma mensagem parecida com esta:
```text
Welcome to Ubuntu 24.04.3 LTS (GNU/Linux 6.6.87.2-microsoft-standard-WSL2 x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Fri Oct 17 23:36:39 -03 2025

  System load:  0.0                 Processes:             42
  Usage of /:   0.3% of 1006.85GB   Users logged in:       1
  Memory usage: 6%                  IPv4 address for eth0: x.x.x.x
  Swap usage:   0%

Last login: Fri Oct 17 23:20:22 2025 from 127.0.0.1
```


## Reiniciando o servidor SSH
```bash
sudo systemctl restart ssh
```

## Liberando a porta [HOST_PORT] no firewall do Windows
Nota: os passos abaixo são necessários apenas se desejar conectar ao servidor SSH a partir de outra máquina na rede LAN.
```powershell
New-NetFirewallRule -Name sshd -DisplayName 'OpenSSH Server (sshd) for WSL' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort [HOST_PORT]
```

## Port forwarding (encaminhamento do tráfego do Windows para o WSL)
```powershell
netsh interface portproxy add v4tov4 listenport=[HOST_PORT] listenaddress=0.0.0.0 connectport=[WSL_PORT] connectaddress=[WSL_IP]
```

## Verificando se o encaminhamento de porta foi criado
```powershell
netsh interface portproxy show all
```

Deverá retornar algo parecido com:

# Listen on IPv4
| Address  | Port      | Address  | Port     |
| -------- | --------- | -------- | -------- |
| 0.0.0.0  | [HOST_PORT] | [WSL_IP] | [WSL_PORT] |

## Conectando ao servidor a partir de outra máquina na rede LAN (ou do próprio host)
```powershell
ssh [WSL_USER]@[HOST_IP] -p [HOST_PORT] -v
```
O parâmetro -v significa verbose; é opcional, mas útil para diagnosticar a conexão.

## Novamente deverá aparecer a mensagem de verificação da autenticidade do host — você pode usar este link para pular diretamente para ela: [Aparecerá a seguinte mensagem](#aparecera-a-seguinte-mensagem)

Basta seguir os mesmos passos e seu servidor SSH estará pronto.

---


## Referências
- DEVMEDIA: [Comandos Importantes Linux](https://www.devmedia.com.br/comandos-importantes-linux/23893)
- Microsoft: [Comandos básicos para WSL](https://learn.microsoft.com/pt-br/windows/wsl/basic-commands)
- Digital Ocean: [Como usar systemctl para gerenciar serviços e unidades do systemd](https://www.digitalocean.com/community/tutorials/how-to-use-systemctl-to-manage-systemd-services-and-units-pt)
- Microsoft: [Acessando uma distribuição do WSL 2 na rede local (LAN)](https://learn.microsoft.com/pt-br/windows/wsl/networking#accessing-a-wsl-2-distribution-from-your-local-area-network-lan)
- Microsoft: [New-NetFirewallRule](https://learn.microsoft.com/en-us/powershell/module/netsecurity/new-netfirewallrule?view=windowsserver2025-ps)