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

7. Pode-se criar alias no arquivo '.gitconfig' na 'home':
```
	setpersonalemail = "config user.email 'pessoalemail@gmail.com'"
	setempresarialemail = "config user.email 'empresarialemail@empresa.com.br'"
	changeremotehost = !sh -c \"git remote -v | grep '$1.*fetch' | sed s/..fetch.// | sed s/$1/$2/ | xargs git remote set-url\"
	gopersonal = !sh -c \"git changeremotehost github.com github_seualiaspessoal && git setpersonalemail\"
	goempresarial = !sh -c \"git changeremotehost github.com github_seualiasempresarial && git setempresarialemail\"
```

## Uso:
```
git gopersonal
```
Ou
```
git changeremotehost github.com github_seualiaspessoal
```

Referência: [ArnaudRinquin](https://github.com/ArnaudRinquin/blog/blob/master/2014-03-11-one-command-github-account-switch.md)


