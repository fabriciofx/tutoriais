# Como Configurar Corretamente o SSH

## macOS

1. Por padrão, o macOS já inicializa o `ssh-agent` não precisando de nenhuma
   configuração a este respeito;

2. Crie suas chaves privada e pública localmente, usando o comando `ssh-keygen`.
   Recomendo criar chaves usando o algoritmo `ED25519` (default):

```bash
ssh-keygen -C "seu_email@example.com" -N "" -f ~/.ssh/minha-chave
```

3. O comando do item anterior irá criar dois arquivos no diretório `~/.ssh`:
   `minha-chave`, que é a sua chave privada e `minha-chave.pub` que é a sua
   chave pública. Guarde com TODO O CUIDADO o arquivo que contém a sua chave
   privada (`minha-chave`)

4. Adicione (ou crie se não existir) ao seu arquivo `~/.ssh/config` uma
associação do host com a chave, conforme os exemplos abaixo:

```
# Global configuration (for all hosts)
Host *
  ServerAliveInterval 60
  ServerAliveCountMax 3

# GitHub
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/github
  UseKeychain yes
  AddKeysToAgent yes

# Nyx
Host nyx
  HostName 192.168.0.2
  User fbc
  IdentityFile ~/.ssh/local
  UseKeychain yes
  AddKeysToAgent yes
```

Em que:

- `Host`: define um apelido ou padrão de host. Pode usar coringas como
  `*.example.com`. Se for usado apenas um `*` as configurações contidas nele
  aplica-se a todos os hosts
- `HostName`: especifica o nome real do host ou o endereço IP
- `User`: define o nome do usuário SSH que será usado na autenticação
- `IdentifyFile`: indica o arquivo da chave privada a ser utilizada para
  autenticar a conexão
- `UseKeychain`: permite que `Keychain` da Apple armazene a senha da chave
  privada
- `AddKeysToAgent`: adiciona automaticamente a chave ao `ssh-agent` quando você
  se conecta
- `ServerAliveInterval`: mantém a conexão ativa, enviando um "ping" a cada
  (`60`) segundos, evitando o timeout
- `ServerAliveCountMax`: especifica quantas tentativas (`3`) são permitidas
  antes de fechar a conexão

5. Certifique-se que o diretório `.ssh` e os arquivos que contém cada chave
   possuem a permissão correta:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/minha-chave
chmod 644 ~/.ssh/minha-chave.pub
chmod 600 ~/.ssh/authorized_keys
chown -R $(whoami):$(whoami) ~/.ssh
```

6. Para permitir a conexão segura via SSH, adicione sua chave pública (`*.pub`)
   ao arquivo `~/.ssh/authorized_keys` do servidor (aquele que você está
   tentando se conectar), assim:

```bash
ssh-copy-id -i ~/.ssh/minha-chave.pub usuario@ip-do-servidor
```

ou

```bash
cat ~/.ssh/minha-chave.pub | ssh usuario@ip-do-servidor "cat >> ~/.ssh/authorized_keys"
```

Caso alguma das duas formas, você obtenha um `usuario@ip-do-servidor: Permission
denied (publickey).`, isto significa que o servidor desativou o login por senha
e você terá que usar um dos métodos abaixo para enviar a sua chave pública para
o `~/.ssh/authorized_keys` do servidor:

- **Console físico ou KVM**: se logue fisicamente no servidor e copie o conteúdo
  do arquivo `~/.ssh/minha-chave.pub` para o arquivo `~/.ssh/authorized_keys` do
  usuario do servidor que você deseja utilizar para se conectar;
- **Usando outra conta de usuário no servidor**: utilize outra conta do usuário,
  que pode ser via SSH, para se conectar ao servidor e copiar o conteúdo da
  chave pública para o arquivo `~/.ssh/authorized_keys` do usuário que você
  deseja utilizar para se conectar;
- **Transferindo chave via outro serviço (ex: SCP/SFTP, Git, etc.)**: utilize
  outro serviço que o servidor tenha disponível para copiar o conteúdo da chave
  pública para o arquivo `~/.ssh/authorized_keys` do usuário que você deseja
  utilizar para se conectar. Alguns servidores possuem um serviço de console via
  web (como o Oracle Cloud Infrastructure) para se logar na máquina e poder
  executar essa tarefa.

Caso não tenha nenhuma das formas acima, você estará **BLOQUEADO** de se
conectar via SSH nessa máquina.

## Linux (com systemd)

1. Crie suas chaves privada e pública localmente, usando o comando `ssh-keygen`.
   Recomendo criar chaves usando o algoritmo `ED25519` (default):

```bash
ssh-keygen -C "seu_email@example.com" -N "" -f ~/.ssh/minha-chave
```

2. O comando do item anterior irá criar dois arquivos no diretório `~/.ssh`:
   `minha-chave`, que é a sua chave privada e `minha-chave.pub` que é a sua
   chave pública. Guarde com TODO O CUIDADO o arquivo que contém a sua chave
   privada (`minha-chave`)

3. Adicione (ou crie se não existir) ao seu arquivo `~/.ssh/config` uma
associação do host com a chave, conforme os exemplos abaixo:

```
# Global configuration (for all hosts)
Host *
  ServerAliveInterval 60
  ServerAliveCountMax 3

# GitHub
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/github
  UseKeychain yes
  AddKeysToAgent yes

# Nyx
Host nyx
  HostName 192.168.0.2
  User fbc
  IdentityFile ~/.ssh/local
  UseKeychain yes
  AddKeysToAgent yes
```

Em que:

- `Host`: define um apelido ou padrão de host. Pode usar coringas como
  `*.example.com`. Se for usado apenas um `*` as configurações contidas nele
  aplica-se a todos os hosts
- `HostName`: especifica o nome real do host ou o endereço IP
- `User`: define o nome do usuário SSH que será usado na autenticação
- `IdentifyFile`: indica o arquivo da chave privada a ser utilizada para
  autenticar a conexão
- `UseKeychain`: permite que `Keychain` da Apple armazene a senha da chave
  privada
- `AddKeysToAgent`: adiciona automaticamente a chave ao `ssh-agent` quando você
  se conecta
- `ServerAliveInterval`: mantém a conexão ativa, enviando um "ping" a cada
  (`60`) segundos, evitando o timeout
- `ServerAliveCountMax`: especifica quantas tentativas (`3`) são permitidas
  antes de fechar a conexão

4. Certifique-se que o diretório `.ssh` e os arquivos que contém cada chave
   possuem a permissão correta:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/minha-chave
chmod 644 ~/.ssh/minha-chave.pub
chmod 600 ~/.ssh/authorized_keys
chown -R $(whoami):$(whoami) ~/.ssh
```

5. Crie (se já não existir) o diretório `~/.config/systemd/user`, assim:

```bash
mkdir -p ~/.config/systemd/user
```

6. Crie (se já não existir) o arquivo `ssh-agent.service` no diretório do item
   anterior, com o seguinte conteúdo:

```
[Unit]
Description=User SSH Agent
After=default.target

[Service]
Type=simple
Environment=SSH_AUTH_SOCK=%t/ssh-agent.socket
ExecStart=/usr/bin/ssh-agent -D -a $SSH_AUTH_SOCK

[Install]
WantedBy=default.target
```

**Observação**: se necessário, faça as devidas adaptações, como o path correto
do programa `/usr/bin/ssh-agent`.

7. Recarregue o `systemd`, ative (`enable`) e inicie (`start`) o serviço
   `ssh-agent.service` do `systemd`, com os comandos abaixo:

```bash
systemctl --user daemon-reload
systemctl --user enable ssh-agent.service
systemctl --user start ssh-agent.service
```

8. Verifique se o serviço está executando corretamente, com o comando abaixo:

```bash
systemctl --user status ssh-agent.service
```

Ele deve exibir algo assim:

```
● ssh-agent.service - User SSH Agent
     Loaded: loaded (/home/fbc/.config/systemd/user/ssh-agent.service; enabled; preset: enabled)
     Active: active (running) since Sat 2025-09-27 21:43:15 UTC; 8min ago
   Main PID: 4449 (ssh-agent)
      Tasks: 1 (limit: 14221)
     Memory: 920.0K (peak: 1.5M)
        CPU: 6ms
     CGroup: /user.slice/user-1002.slice/user@1002.service/app.slice/ssh-agent.service
             └─4449 /usr/bin/ssh-agent -D -a /run/user/1002/ssh-agent.socket

Sep 27 21:43:15 nyx systemd[4436]: Started ssh-agent.service - User SSH Agent.
Sep 27 21:43:15 nyx ssh-agent[4449]: SSH_AUTH_SOCK=/run/user/1002/ssh-agent.socket; export SSH_AUTH_SOCK;
Sep 27 21:43:15 nyx ssh-agent[4449]: echo Agent pid 4449;
```

Observe os `enabled` e em `Active:` estar `active (running)`.

9. Configure da variável de ambiente `SSH_AUTH_SOCK`, adicionando a linha abaixo
   ao final do seu `~/.bashrc`, `~/.zshrc` ou equivalente:

```
export SSH_AUTH_SOCK="$XDG_RUNTIME_DIR/ssh-agent.socket"
```

10. Recarregue o arquivo de configuração `~/.bashrc`, `~/.zshrc` ou equivalente
   com o comando:

```bash
source ~/.bashrc
```

ou

```bash
source ~/.zshrc
```

11. Verifique se o `ssh-agent` está ativo e recebendo chaves, com o comando:

```bash
ssh-add -l
```

Ele deve exibir algo como:

```
The agent has no identities.
```

Ou listar as chaves registradas, como, por exemplo:

```
256 SHA256:AJhjdsmkweu2347adsjna+wekdshsw2 seu_email@example.com (ED25519)
```

12. Adicione a sua chave privada ao `ssh-agent` com o comando:

```bash
ssh-add ~/.ssh/minha-chave
```

Caso queria adicioná-la permanentemente, pode-se colocar o comando acima no
final do seu arquivo de configuração `~/.bashrc`, `~/.zshrc` ou equivalente

13. Para permitir a conexão segura via SSH, adicione sua chave pública (`*.pub`)
   ao arquivo `~/.ssh/authorized_keys` do servidor (aquele que você está
   tentando se conectar), assim:

```bash
ssh-copy-id -i ~/.ssh/minha-chave.pub usuario@ip-do-servidor
```

ou

```bash
cat ~/.ssh/minha-chave.pub | ssh usuario@ip-do-servidor "cat >> ~/.ssh/authorized_keys"
```

Caso alguma das duas formas, você obtenha um `usuario@ip-do-servidor: Permission
denied (publickey).`, isto significa que o servidor desativou o login por senha
e você terá que usar um dos métodos abaixo para enviar a sua chave pública para
o `~/.ssh/authorized_keys` do servidor:

- **Console físico ou KVM**: se logue fisicamente no servidor e copie o conteúdo
  do arquivo `~/.ssh/minha-chave.pub` para o arquivo `~/.ssh/authorized_keys` do
  usuario do servidor que você deseja utilizar para se conectar;
- **Usando outra conta de usuário no servidor**: utilize outra conta do usuário,
  que pode ser via SSH, para se conectar ao servidor e copiar o conteúdo da
  chave pública para o arquivo `~/.ssh/authorized_keys` do usuário que você
  deseja utilizar para se conectar;
- **Transferindo chave via outro serviço (ex: SCP/SFTP, Git, etc.)**: utilize
  outro serviço que o servidor tenha disponível para copiar o conteúdo da chave
  pública para o arquivo `~/.ssh/authorized_keys` do usuário que você deseja
  utilizar para se conectar. Alguns servidores possuem um serviço de console via
  web (como o Oracle Cloud Infrastructure) para se logar na máquina e poder
  executar essa tarefa.

Caso não tenha nenhuma das formas acima, você estará **BLOQUEADO** de se
conectar via SSH nessa máquina.
