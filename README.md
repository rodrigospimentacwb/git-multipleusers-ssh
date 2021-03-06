# Configurar multiplas contas SSH no github

### Importante:

Antes de iniciar este tutorial, verifique se o arquivo /home/.gitconfig (global) do seu sistema esta sem nenhuma configuração prefixada (conforme exemplo abaixo). Preferencialmente faça um backup do arquivo.

```
[filter "lfs"]
	clean = git-lfs clean -- %f
	smudge = git-lfs smudge -- %f
	process = git-lfs filter-process
	required = true
	
[alias]

```

## Criar chaves e registrar no github

1. Gerar uma chave por usuario desejado

	```$ ssh-keygen -t rsa -C "usuario1@gmail.com" -f ~/.ssh/id_alias_pessoa```

	```$ ssh-keygen -t rsa -C "usuario.trabalho@empresa.com.br" -f ~/.ssh/id_alias_empresarial```

2. Acessar via terminal a pasta '.ssh' na home/.ssh

3. Abrir os arquivos '.pub', copiar seu conteudo e colar dentro de cada conta do github em settings/SSH and GPG keys/new SSH key (Dar um nome a chave)

4. Criar dentro da pasta '.ssh' (caso não exista) o arquivo .config

	```$ touch ~/.ssh/config```

5. Editar este arquivo adicionando:

	```
	# Pessoal github alias
	Host github.seualiaspessoalsemespacos
	  HostName github.com
	  User git
	  IdentityFile ~/.ssh/id_alias_pessoa

	# Professional github alias
	Host github.seualiasempresarialsemespacos
	  HostName github.com
	  User git
	  IdentityFile ~/.ssh/id_alias_empresarial
	```
  
6. Para o sistema auto-identificar a chave SSH, basta na pasta de cada projeto mudar o repositorio de 'git@github.com' para 'git@github_seualiaspessoal' ou 'git@github_seualiasempresarial': 

	```$ git remote origin set-url git@github_seualiaspessoal:seuusuariogit/seurepositoriogit.git```

7. Para clonar um novo projeto, alterar a url utilizando seu alias:

	```
	git@github.com:repodasuaempresa/repoprojeto.git
	Para:
	git@github_seualiasempresarial:repodasuaempresa/repoprojeto.git

	Obs: Será solicitada a senha do arquivo SSH gerado no item 1 deste README para acesso ao repositório.
	```

## Criando Alias + Bash para clonar os arquivos sem trocar a URL na mão

Utilizando a forma abaixo, não precisará alterar a URL do git clone SSH quando for baixar um projeto.


1. Criar um alias no .gitconfig global como no exemplo abaixo:

	```
	xclone = !sh -c \"$HOME/myBashGitUrlSSH.sh $1 \" 
	```

2. Criar o bash 'myBashGitUrlSSH.sh' na pasta '/home' onde esta seu .gitconfig global:

	```$ touch ~/myBashGitUrlSSH.sh```
	
	Dentro do arquivo adicionar e substituir os valores em CAPS para seus valores:
	```
	#!/bin/bash
	if [[ "$1" == *":SEUREPO/"* ]]
	then
		echo "Utilizando SSH Pessoal"
		echo "clonando SSH: " $(echo $1 | sed '\=\:SEUREPO/= s=\.com:=\.SEUALIASPESSOALSEMESPACOS:=g')
		git clone $(echo $1 | sed '\=\:SEUREPO/= s=\.com:=\.SEUALIASPESSOALSEMESPACOS:=g')
		cd "$(basename "$1" .git)"
		git config user.name "SEU_USUARIO_PESSOAL_GIT"
		git config user.email "SEU_EMAIL_PESSOAL_GIT"

	elif [[ "$1" == *":REPOEMPRESA/"* ]] 
	then
		echo "Utilizando SSH Empresa"
		echo "clonando SSH: " $(echo $1 | sed '\=\:REPOEMPRESA/= s=\.com:=\.SEUALIASEMPRESARIALSEMESPACOS:=g')
		git clone $(echo $1 | sed '\=\:REPOEMPRESA/= s=\.com:=\.SEUALIASEMPRESARIALSEMESPACOS:=g')
		cd "$(basename "$1" .git)"
		git config user.name "SEU_USUARIO_EMPRESARIAL_GIT"
		git config user.email "SEU_EMAIL_EMPRESARIAL_GIT"
	else
		echo "Dominio não configurado para SSH"
	fi
	```
	Exemplo:
	
	```
	Arquivo config:
	# Pessoal github alias
	Host github.rodrigospimentacwb
	  HostName github.com
	  User git
	  IdentityFile ~/.ssh/id_chave_ssh
	  ...
	```
	```
	Arquivo myBashGitUrlSSH.sh:
	if [[ "$1" == *":rodrigospimentacwb/"* ]]
	then
		echo "Utilizando SSH Pessoal"
		echo "clonando SSH: " $(echo $1 | sed '\=\:rodrigospimentacwb/= s=\.com:=\.rodrigospimentacwb:=g')
		git clone $(echo $1 | sed '\=\:rodrigospimentacwb/= s=\.com:=\.rodrigospimentacwb:=g')
		cd "$(basename "$1" .git)"
		git config user.name "seuusuario"
		git config user.email "seuemail@gmail.com"
		...
	```

3. Liberar acesso ao 'myBashGitUrlSSH.sh' para não solitar sudo:

	```
	$ chmod +x ~/myBashGitUrlSSH.sh
	```

4. Uso:

	```
	$ git xclone git@github.com:SEUREPO/SEUPROJETO.git

	ou

	$ git@github.com:REPOEMPRESA/PROJETOEMPRESA.git

	Obs: Será solicitada a senha do arquivo SSH gerado no item 1 deste README para acesso ao repositório.
	```

METODO LOCAL:
```
[user]
        name = usuario
        email = email@gmail.com
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
	ignorecase = true
	precomposeunicode = true

	sshCommand = "ssh -i ~/.ssh/id_rsa_alias"

[remote "origin"]
	url = git@github.com:repogit/myapp.git
	fetch = +refs/heads/*:refs/remotes/origin/*
```

### Referências: 

[ArnaudRinquin](https://github.com/ArnaudRinquin/blog/blob/master/2014-03-11-one-command-github-account-switch.md)

[Lukas Lukac](https://dev.to/web3coach/how-to-configure-a-local-git-repository-to-use-a-specific-ssh-key-4aml)

