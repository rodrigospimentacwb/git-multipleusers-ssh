# Configurar multiplas contas SSH no github

## Criar chaves e registrar no github

1. Gerar uma chave por usuario desejado

```ssh-keygen -t rsa -C "usuario1@gmail.com" -f ~/.ssh/id_alias_pessoa```

```ssh-keygen -t rsa -C "usuario.trabalho@empresa.com.br" -f ~/.ssh/id_alias_empresarial```

2. Acessar via terminal a pasta '.ssh' na home/.ssh

3. Abrir os arquivos '.pub', copiar seu conteudo e colar dentro de cada conta do github em settings/SSH and GPG keys/new SSH key (Dar um nome a chave)

4. Criar dentro da pasta '.ssh' (caso não exista) o arquivo .config

```touch ~/.ssh/config```

5. Editar este arquivo adicionando:

```
# Pessoal github alias
Host github_seualiaspessoal
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_alias_pessoa
  
# Professional github alias
Host github_seualiasempresarial
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_alias_empresarial
```
  
6. Para o sistema auto-identificar a chave SSH, basta na pasta de cada projeto mudar o repositorio de 'git@github.com' para 'git@github_seualiaspessoal' ou 'git@github_seualiasempresarial': 

```git remote origin set-url git@github_seualiaspessoal:seuusuariogit/seurepositoriogit.git```

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

1.1 Para o exemplo do item 2:

```
PESSOAL SSH: git@github.com:SEUREPO/SEUPROJETO.git -> git@github.ALIASSSHPESSOAL:SEUREPO/SEUPROJETO.git
EMPRESARIAL SSH: git@github.com:REPOEMPRESA/PROJETOEMPRESA.git -> git@github.ALIASSSHEMPRESA:REPOEMPRESA/PROJETOEMPRESA.git
```

2. Criar o bash 'myBashGitUrlSSH.sh' na pasta '/home' onde esta seu .gitconfig global:

```
#!/bin/bash
if [[ "$1" == *":REPOEMPRESA/"* ]]
then
	echo "Utilizando SSH Empresa"
	git clone $(echo $1 | sed '\=\:REPOEMPRESA/= s=\.com:=\.ALIASSSHEMPRESA:=g')
elif [[ "$1" == *":SEUREPO/"* ]] 
then
	echo "Utilizando SSH Pessoal"
	git clone $(echo $1 | sed '\=\:SEUREPO/= s=\.com:=\.ALIASSSHPESSOAL:=g')
else
	echo "Dominio não configurado para SSH"
fi
```

3. Liberar acesso ao 'myBashGitUrlSSH.sh' para não solitar sudo:

```
chmod +x myBashGitUrlSSH.sh
```

4. Uso:

```
git xclone git@github.com:SEUREPO/SEUPROJETO.git

ou

git@github.com:REPOEMPRESA/PROJETOEMPRESA.git

Obs: Será solicitada a senha do arquivo SSH gerado no item 1 deste README para acesso ao repositório.
```

### Referência: [ArnaudRinquin](https://github.com/ArnaudRinquin/blog/blob/master/2014-03-11-one-command-github-account-switch.md)


