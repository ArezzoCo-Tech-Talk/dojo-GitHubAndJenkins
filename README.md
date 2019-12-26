# Integração contínua Jenkins + GitHub

Passo a passo para configurar e utilizar as duas ferramentas.

### Requisitos prévios

- Jenkins já funcionando
- Um repositório no GitHub

## Intalar os Plugins necessários no Jenkins

- Hudson Post Build Task
- GitHub integration plugin
- [Blue Ocean plugin](https://jenkins.io/projects/blueocean/)

Estes serão explicados abaixo.

## Criar um Webhook no GitHub

No repositório `/settings/hooks` configuramos um webhook.

- Payload URL: `<http://your_jenkins_server_url>/github-webhook/`
- Which events would you like to trigger this webhook? Just the push event.

Dessa forma "avisamos" o Jenkins que houve um push.

## Criar um Pipeline no Jenkins

Pipeline é um conjunto de tarefas executadas quando disparado um certo evento, no nosso caso um Push no GitHub.

Então na navegação da esquerda Jenkins acessamos esse plugin.

![image](https://user-images.githubusercontent.com/27368585/71487294-4bc89d00-27f9-11ea-8c1d-13ce2d31e714.png)

Essa ferramenta também auxilia a visualizar os Pipeline e suas tarefas

![image](https://user-images.githubusercontent.com/27368585/71487383-baa5f600-27f9-11ea-908d-382dee0ec64f.png)

- Agora acessamos "New Pipeline".
- Em "Where do you store your code?" marcamos GitHub.
- Em "Connect to GitHub" podemos clicar em "Create an access token here." que nos levará ao GitHub já marcando as permissões necessárias.
- Em "Which organization does the repository belong to?" é o GitHub que estarás configurando.
- Em "Choose a repository" o repositório

![image](https://www.vogella.com/tutorials/Jenkins/img/jenkins_pipeline30.png)

O Pipeline ficará com o nome do repositório a não ser que renomeamos.

## Criar o Jenkinsfile

É a configuração das tarefas que serão executadas no respectivo Pipeline.

Essa configuração fica no arquivo [JenkinsFile](https://jenkins.io/doc/book/pipeline/jenkinsfile/) na raíz do repositório. O plugin Blue Ocean faz o trabalho de criar esse arquivo para nós de forma mais intuitiva.

![image](https://user-images.githubusercontent.com/27368585/71489803-37d76800-2806-11ea-8bb2-e413de7caf98.png)
