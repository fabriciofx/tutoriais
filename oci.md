# Oracle Cloud Infrastructure (OCI)

**Instance** é uma máquina virtual da Oracle Cloud Infrastructure (OCI).

## Para criar uma instance (VM)

Crie antes um par de chaves privada e pública usando o algoritmo `rsa` e, as
armazene, respectivamente, nos arquivos `oci` e `oci.pub` (esse passo só é
necessário uma vez e o `ssh-keygen` fará isso)

```bash
ssh-keygen -t rsa -b 4096 -C "seu_email@example.com" -N "" -f ~/.ssh/oci
```
**Atenção:** a Oracle Cloud ainda não suporta chaves ED25519. Por isso, você
terá que criar chaves RSA.

## Criar uma conexão ssh com uma instance (VM)

Caso você não tenha criado sua OCI com uma chave SSH, ainda é possível adicionar
uma a uma OCI já criada. Para isso, siga os seguintes passos:

1. Crie um par de chaves privada e pública usando o algoritmo `rsa` e, as
   armazene, respectivamente, nos arquivos `oci` e `oci.pub` (esse passo só é
   necessário uma vez e o `ssh-keygen` fará isso)

```bash
ssh-keygen -t rsa -b 4096 -C "seu_email@example.com" -N "" -f ~/.ssh/oci
```

2. Inicialize o agent SSH (se ele já não estiver sendo executado) na sua máquina

```bash
ssh-agent -s
```

3. Adicione as chaves ao agente SSH (este passo é necessário toda a vez que
   iniciar o agente SSH)

```bash
ssh-add ~/.ssh/oci
```

4. Adicione a chave pública (`oci.pub`) à instância da Oracle Cloud

**Se você já criou uma instância, logue-se na Oracle Cloud e:**

- Selecione o menu hambúrguer, **Compute**, **Instances**;
- Selecione a instância que você quer se conectar;
- Selecione a opção **OS Management**;
- Vá até a opção **Console connection** e aperte o botão **Create local
  connection**;
- Na tela que se abre (**Create console conection**), marque a opção **Upload
  public key file (.pub)**;
- Irá aparecer uma caixa escrito **Drop a file or select one**. Selecione o
  arquivo que contém a sua chave pública (`oci.pub`) que foi criado no **Passo
  1**;
- Aperte o botão **Create console conection**.

5. Vá até a aba **Details** e copie o endereço IP público **Public IP Address**

6. Vá até o terminal do seu computador e faça a conexão SSH:

```bash
ssh ubuntu@endereco-ip-publico
```

7. Se aparecer uma mensagem assim:

```
The authenticity of host 'endereco-ip-da-vm (endereco-ip-da-vm)' can't be established.
ED25519 key fingerprint is SHA256:xj/HJKAJKHAHjadjshadhjs2376hjkeh223sdhjb2332
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Digite **yes** e depois aperte a tecla **[ENTER]**.

Esta mensagem não aparecerá mais nas próximas conexões.

Parabéns! Você deve ter conseguido se conectar a sua instância (VM) da Oracle
Cloud

## Instalar e configurar o locale

Por padrão, a instância (OCI) não vem com suporte a `locales`. É preciso
instalar os pacotes para que não apareça a mensagem:

```
-bash: warning: setlocale: LC_ALL: cannot change locale (en_US.UTF-8)
```

Para instalar os pacotes responsáveis pela localização, use os comandos:

```bash
sudo apt-get update
sudo apt-get -y install locales language-pack-en
```

Depois, para configurar, faça:

```bash
sudo update-locale LANG=en_US.UTF-8 LC_ALL=en_US.UTF-8
```

Faça logout e depois um novo login. A mensagem de `warning` relacionada ao
locale *deve* não aparecer mais.

Observação: caso queira instalar o locale para suporte ao português do Brasil,
faça:

```bash
sudo apt-get update
sudo apt-get -y install locales language-pack-pt
```

e para configurar:

```bash
sudo update-locale LANG=pt_BR.UTF-8 LC_ALL=pt_BR.UTF-8
```

## Instalar o vi (vim)

Por padrão, o vi (vim) não acompanha as instâncias (OCI) devendo ser instalado.
Para isso, faça:

```bash
sudo apt-get -y install vim
```

# Instalar outros programas úteis

Talvez seja interessante instalar os seguintes programas:

```bash
sudo apt-get -y install less netcat-openbsd
```

## Remover o motd

Se a cada vez que você se loga a uma instância, aparecer (por exemplo) o
seguinte aviso:

```
Welcome to Ubuntu 24.04.2 LTS (GNU/Linux 6.14.0-1009-oracle aarch64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
Last login: Sun Sep  7 18:23:16 2025 from 192.168.1.1
```

trata-se do **motd**, uma mensagem exibida automaticamente quando você faz login
em um sistema Unix/Linux, geralmente via SSH ou terminal local. Para removê-la,
faça o seguinte:

1. Comente as seguintes linhas no arquivo `/etc/pam.d/sshd`

```
session    optional     pam_motd.so  motd=/run/motd.dynamic
session    optional     pam_motd.so noupdate
```

2. Comente as seguintes linhas no arquivo `/etc/pam.d/login`

```
session    optional     pam_motd.so  motd=/run/motd.dynamic
session    optional     pam_motd.so noupdate
```

3. Verifique se no arquivo `/etc/ssh/sshd_config` possui a seguinte linha
   descomentada:

```
PrintMotd no
```

Depois de fazer estas modificações, faça o logout e conecte-se novamente. A
mensagem não deve aparecer mais.

## Criar um novo usuário

Para criar um novo usuário siga os seguintes passos:

1. Utilize o comando `adduser` para cria um novo usuário

```bash
sudo adduser <username>
```

Em que `<username>` é o nome do usuário que você deseja criar.

2. Configure um nova senha (password)

3. Preencha as informações solicitadas (Full name, etc.)

4. Edite o arquivo `/etc/group` e coloque seu novo usuário nos seguintes grupos:
   `adm`, `dialout`, `cdrom`, `floppy`, `sudo`, `audio`, `dip`, `video`,
   `plugdev`, `users`, `lxd` e `netdev`.

Atenção: os groupos pode variar a depender da arquitetura do seu OCI. Se for
x86 a quantidade de grupos é menor do que a ARM.

## Permitir conexão SSH pelo novo usuário

1. Crie um diretório `.ssh` no diretório home do novo usuário

```bash
mkdir ~/.ssh
```

2. Copie o arquivo `authorized_keys` do diretório `.ssh` do usuário `ubuntu`
   para dentro deste diretório criado

```bash
sudo cp /home/ubuntu/.ssh/authorized_keys ~/.ssh
```

3. Mude as permissões do arquivo `authorized_keys` e do diretório `.ssh`

```bash
sudo chown <username>:<username> -R .ssh
chmod 600 .ssh/authorized_keys
chmod 700 .ssh
```

Em que `<username>` é o username do usuário.

## Instalar o nodejs mais recente

Para instalar o `nodejs` mais recente, execute:

```bash
curl -fsSL https://deb.nodesource.com/setup_current.x | sudo -E bash -
sudo apt-get -y install nodejs
```

Depois, você pode verificar a versão instalada:

```bash
node --version
npm --version
```

## Instalar o Angular mais recente

```bash
sudo npm install -g @angular/cli
```

## Liberar portas usando o iptables

### No Oracle Cloud Console

1. No menu hambúrguer, selecione a aba **Networking**
2. Em **Networking** escolha a opção **Virtual cloud networks**
3. Na seção **Virtual Cloud Networks** selecione a rede que contém a instância
   que você quer abrir as portas
4. Na seção **<Nome da sua VCN>** vá até a aba **Security**
5. Em **Security Lists**, selecione **Default Security List for <Nome da sua
   VCN>**
6. Em **Ingress Rules** adicione a regra que você deseja aplicar. No caso, vamos
   supor que você queira abrir a porta TCP 4200. Então clique no botão **Add
   Ingress Rules**
7. Na aba que se abre, informe:
   Stateless: OFF
   Source Type: CIDR
   Source CIDR: 0.0.0.0/0
   IP Protocol: TCP
   Source Port Range: <empty>
   Destination Port: 4200
   Description: Coloque uma descrição para esta regra

### Na instância

8. Edite o arquivo `/etc/iptables/rules.v4`e coloque as seguintes regras:

-A INPUT -p tcp --dport 4200 -j ACCEPT
-A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
-A OUTPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

## Liberar portas usando o ufw

### No Oracle Cloud Console

1. No menu hambúrguer, selecione a aba **Networking**
2. Em **Networking** escolha a opção **Virtual cloud networks**
3. Na seção **Virtual Cloud Networks** selecione a rede que contém a instância
   que você quer abrir as portas
4. Na seção **<Nome da sua VCN>** vá até a aba **Security**
5. Em **Security Lists**, selecione **Default Security List for <Nome da sua
   VCN>**
6. Em **Ingress Rules** adicione a regra que você deseja aplicar. No caso, vamos
   supor que você queira abrir a porta TCP 4200. Então clique no botão **Add
   Ingress Rules**
7. Na aba que se abre, informe:
   Stateless: OFF
   Source Type: CIDR
   Source CIDR: 0.0.0.0/0
   IP Protocol: TCP
   Source Port Range: <empty>
   Destination Port: 4200
   Description: Coloque uma descrição para esta regra

### Na instância

1. Primeiramente, instale o `ufw`:

```bash
sudo apt-get -y install ufw
```

Atenção: ele irá desinstalar o `iptables-persistent` e `netfilter-persistent`.

2. Ative o `ufw`:

```bash
sudo ufw enable
```

3. Permita o tráfego na porta 4200 do protocolo TCP:

```bash
sudo ufw allow 4200/tcp
```

Atenção 2: Ao instalar o `ufw` ele remove a liberação da porta SSH (tcp 22).
Portanto, para não perder o acesso SSH à instância, libere a porta do SSH pelo
`ufw`:

```bash
sudo ufw allow 22/tcp
```
