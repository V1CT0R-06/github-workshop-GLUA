# Workshop: Introdução ao Git e GitHub

**Grupo Linux da Universidade de Aveiro (GLUA)**

Guião prático do workshop de Introdução ao Git e GitHub.

Este documento acompanha os exercícios do workshop. Os comandos são executados no terminal do Ubuntu, salvo indicação em contrário. Os exemplos usam a branch `main` e autenticação SSH no GitHub.

## 1. Objetivos

No final do workshop, cada participante deverá conseguir:

- Distinguir Git de GitHub.
- Criar e configurar um repositório local.
- Consultar alterações e criar commits.
- Publicar um repositório no GitHub através de SSH.
- Criar uma branch e abrir um Pull Request.
- Integrar alterações na `main` e atualizar a cópia local.

## 2. Preparação

### 2.1. Conta GitHub e terminal

É necessária uma conta em [github.com](https://github.com) e acesso ao terminal do Ubuntu.

### 2.2. Instalar o Git

No terminal do Ubuntu:

```bash
sudo apt update
sudo apt install git openssh-client
```

Confirmar a instalação:

```bash
git --version
```

### 2.3. Configuração do Git

Substituir os valores pelos dados de cada participante:

```bash
git config --global user.name "Nome Apelido"
git config --global user.email "email@example.com"
git config --global init.defaultBranch main
```

Verificar:

```bash
git config --global --list
```

O nome e o email identificam os autores dos commits. O email deve estar associado à conta GitHub, ou pode ser utilizado o endereço `noreply` fornecido pelo GitHub.

## 3. Configurar SSH para o GitHub

SSH permite autenticar operações Git sem introduzir uma palavra-passe do GitHub a cada `push`.

### 3.1. Verificar se já existe uma chave

```bash
ls -al ~/.ssh
```

Procurar ficheiros como `id_ed25519` e `id_ed25519.pub`. Se já existir uma chave configurada para o GitHub, não é necessário criar outra. Se não existir, continuar.

### 3.2. Criar uma chave SSH

```bash
ssh-keygen -t ed25519 -C "email@example.com"
```

Aceitar a localização sugerida, desde que não substitua uma chave existente. Definir uma *passphrase* para proteger a chave privada.

> Nunca partilhar o ficheiro `id_ed25519`. O ficheiro `id_ed25519.pub` é a chave pública que pode ser registada no GitHub.

### 3.3. Adicionar a chave ao agente SSH

No terminal do Ubuntu:

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

### 3.4. Registar a chave pública no GitHub

Mostrar a chave pública:

```bash
cat ~/.ssh/id_ed25519.pub
```

1. Copiar **toda** a linha apresentada.
2. No GitHub, abrir **Settings → SSH and GPG keys → New SSH key**.
3. Dar um nome à chave (por exemplo, `Portatil pessoal`).
4. Selecionar **Authentication Key** e colar a chave pública.
5. Guardar.

### 3.5. Testar a ligação

```bash
ssh -T git@github.com
```

Na primeira ligação, confirmar a impressão digital do servidor através da [documentação oficial do GitHub](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/githubs-ssh-key-fingerprints) antes de aceitar.

Uma ligação bem-sucedida apresenta uma mensagem semelhante a:

```text
Hi utilizador! You've successfully authenticated, but GitHub does not provide shell access.
```

## 4. Git e GitHub: conceitos iniciais

**Git** é um sistema de controlo de versões distribuído: permite registar alterações e consultar o histórico de um projeto localmente.

**GitHub** é uma plataforma que aloja repositórios Git e facilita a colaboração, por exemplo através de Pull Requests.

| Conceito | Significado |
| --- | --- |
| Repositório | Projeto acompanhado pelo Git |
| Working directory | Ficheiros em que estamos a trabalhar |
| Staging area | Alterações selecionadas para o próximo commit |
| Commit | Registo de uma versão no histórico local |
| Remote | Referência a outro repositório, por exemplo no GitHub |
| Push | Envia commits para o remoto |
| Pull | Obtém e integra alterações do remoto |
| Branch | Linha de desenvolvimento independente |
| Pull Request | Proposta de integração de alterações no GitHub |

## 5. Criar o primeiro repositório

Criar uma pasta e entrar nela:

```bash
mkdir workshop-git
cd workshop-git
```

Inicializar o repositório:

```bash
git init
```

Verificar o estado:

```bash
git status
```

**Resultado esperado:** estamos na branch `main`, ainda sem commits.

## 6. Criar o primeiro commit

Criar o ficheiro inicial:

```bash
echo "# Workshop Git - GLUA" > README.md
```

Verificar:

```bash
git status
```

**Resultado esperado:** `README.md` aparece como ficheiro não rastreado (*untracked*).

Preparar o ficheiro para o commit:

```bash
git add README.md
git status
```

**Resultado esperado:** o ficheiro passa a aparecer em *Changes to be committed*.

Criar o commit:

```bash
git commit -m "Adiciona README inicial"
```

Consultar o histórico:

```bash
git log --oneline
```

## 7. Publicar o projeto no GitHub

### 7.1. Criar o repositório remoto

1. Abrir [github.com/new](https://github.com/new).
2. Indicar o nome `workshop-git`.
3. Escolher **Public** ou **Private**.
4. Não adicionar README, `.gitignore` nem licença neste momento.
5. Selecionar **Create repository**.
6. Na página seguinte, escolher a opção **SSH** e copiar o endereço apresentado.

O endereço terá este formato:

```text
git@github.com:UTILIZADOR/workshop-git.git
```

### 7.2. Associar o remoto e enviar os commits

Substituir `UTILIZADOR` pelo nome da conta GitHub:

```bash
git remote add origin git@github.com:UTILIZADOR/workshop-git.git
git remote -v
git push -u origin main
```

**Resultado esperado:** o ficheiro `README.md` aparece na página do repositório no GitHub.

## 8. Alterar ficheiros e consultar diferenças

Adicionar uma linha ao README:

```bash
echo "Primeira alteração ao projeto." >> README.md
```

Verificar o estado e as diferenças:

```bash
git status
git diff README.md
```

**Resultado esperado:** o `git diff` mostra a linha adicionada com o sinal `+`.

Adicionar a alteração à staging area:

```bash
git add README.md
```

Consultar as diferenças que serão incluídas no commit:

```bash
git diff --staged
```

Criar e enviar o commit:

```bash
git commit -m "Atualiza descrição do projeto"
git log --oneline
git push
```

## 9. Trabalhar com branches

### Branch ou fork?

Uma **branch** é uma linha de desenvolvimento dentro de um repositório. Permite desenvolver uma funcionalidade ou corrigir um problema sem alterar imediatamente a branch principal (`main`). As branches partilham o histórico do mesmo repositório.

Um **fork** é uma cópia de um repositório criada na nossa conta do GitHub. É útil quando queremos contribuir para um projeto de outra pessoa ou organização sem termos permissões para enviar alterações diretamente para o repositório original.

| Branch | Fork |
| --- | --- |
| É criada dentro de um repositório. | Cria outro repositório, associado ao original no GitHub. |
| É habitual em projetos em que temos acesso de escrita. | É habitual em contribuições para projetos em que não temos acesso de escrita. |
| Permite propor alterações à `main` através de um Pull Request. | Permite propor alterações ao repositório original através de um Pull Request entre repositórios. |

Por exemplo, num projeto de equipa do GLUA, quem tem permissões de escrita pode criar uma branch para desenvolver uma funcionalidade e abrir um Pull Request. Quem quiser contribuir para um projeto público sem essas permissões pode fazer um fork, criar uma branch no seu fork e abrir um Pull Request para o projeto original.

Para criar um fork no GitHub, abrir a página do repositório original, selecionar **Fork** e confirmar a criação. Depois, é possível clonar o fork através do endereço SSH apresentado na sua página. Não precisamos de criar um fork para o exercício seguinte, porque estamos a trabalhar no nosso próprio repositório.

### Criar uma branch

Criar uma branch e mudar para ela:

```bash
git switch -c feature/nova-ferramenta
```

Verificar a branch atual:

```bash
git branch
```

Criar um ficheiro:

```bash
echo "Nova ferramenta do workshop" > ferramenta.txt
```

Registar a alteração:

```bash
git add ferramenta.txt
git commit -m "Adiciona exemplo de ferramenta"
```

Enviar a nova branch para o GitHub:

```bash
git push -u origin feature/nova-ferramenta
```

## 10. Abrir um Pull Request

No GitHub:

1. Abrir o repositório `workshop-git`.
2. Selecionar **Compare & pull request**, caso apareça. Em alternativa, abrir **Pull requests → New pull request**.
3. Confirmar que a branch de destino (*base*) é `main`.
4. Confirmar que a branch de origem (*compare*) é `feature/nova-ferramenta`.
5. Indicar o título `Adiciona exemplo de ferramenta`.
6. Consultar a secção **Files changed**.
7. Selecionar **Create pull request**.
8. Depois de rever as alterações, selecionar **Merge pull request** e confirmar.

## 11. Atualizar o repositório local

Depois de fazer merge no GitHub, regressar à `main` local:

```bash
git switch main
```

Obter e integrar as alterações do remoto:

```bash
git pull
```

Verificar o resultado:

```bash
ls
git log --graph --oneline --decorate --all
```

**Resultado esperado:** o ficheiro `ferramenta.txt` está agora na `main` local.

## 12. Boas práticas

- Consultar `git status` antes de adicionar alterações.
- Fazer commits pequenos, com mensagens que expliquem o que mudou.
- Utilizar branches para desenvolver funcionalidades e corrigir problemas.
- Abrir Pull Requests para propor alterações à `main`, em vez de fazer `push` diretamente para essa branch em projetos colaborativos.
- Não incluir palavras-passe, tokens, chaves privadas ou outros dados sensíveis no repositório.
- Utilizar `.gitignore` para ficheiros gerados, dependências locais e outros conteúdos que não devam ser versionados.
- Consultar as alterações antes de fazer merge.

### Exemplo de `.gitignore`

```bash
printf "__pycache__/\n.venv/\n.env\n" > .gitignore
git add .gitignore
git commit -m "Adiciona regras ao gitignore"
git push
```

**Nota:** `.gitignore` não remove automaticamente ficheiros que já estejam a ser acompanhados pelo Git.

## 13. Problemas frequentes

### `fatal: not a git repository`

Confirmar que estamos na pasta certa:

```bash
pwd
ls -a
```

Entrar na pasta `workshop-git` antes de executar comandos Git.

### `Author identity unknown`

Configurar `user.name` e `user.email` conforme a secção 2.3.

### `Permission denied (publickey)`

Verificar se a chave pública foi adicionada à conta GitHub correta e repetir:

```bash
ssh -T git@github.com
```

Verificar também se o remoto usa SSH:

```bash
git remote -v
```

### `remote origin already exists`

O remoto já foi configurado. Consultar:

```bash
git remote -v
```

Se estiver errado, corrigir o endereço:

```bash
git remote set-url origin git@github.com:UTILIZADOR/workshop-git.git
```

### `nothing to commit, working tree clean`

Não existem alterações pendentes para guardar num novo commit.

### `rejected` ao fazer push

Pode significar que a branch remota tem alterações que ainda não existem localmente. Consultar `git status` e obter as alterações com `git pull` quando for apropriado. Se existirem alterações divergentes ou conflitos, pedir ajuda antes de executar comandos que reescrevam o histórico.

## 14. Resumo de comandos

| Comando | Utilização |
| --- | --- |
| `git init` | Inicializar um repositório |
| `git status` | Consultar o estado |
| `git add ficheiro` | Preparar alterações |
| `git commit -m "mensagem"` | Registar um commit |
| `git log --oneline` | Consultar o histórico |
| `git diff` | Ver alterações ainda não preparadas |
| `git diff --staged` | Ver alterações preparadas |
| `git remote -v` | Consultar os remotos |
| `git push` | Enviar commits |
| `git pull` | Obter e integrar alterações |
| `git branch` | Listar branches |
| `git switch -c nome` | Criar e mudar de branch |
| `git switch main` | Mudar para a branch principal |

## 15. Recursos

- [Documentação oficial do Git](https://git-scm.com/docs)
- [Pro Git : livro gratuito](https://git-scm.com/book/en/v2)
- [GitHub Docs : SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
- [GitHub Docs : Pull Requests](https://docs.github.com/en/pull-requests)
- [GLUA no GitHub](https://github.com/GLUA-UA)

---

**Grupo Linux da Universidade de Aveiro**

Documento de apoio ao workshop de Introdução ao Git e GitHub.
