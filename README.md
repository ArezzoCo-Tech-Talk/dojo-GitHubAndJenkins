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

Essa configuração fica no arquivo[JenkinsFile](https://jenkins.io/doc/book/pipeline/jenkinsfile/) na raíz do repositório. O plugin Blue Ocean faz o trabalho de criar esse arquivo para nós de forma mais intuitiva.

Então na navegação da esquerda Jenkins acessamos esse plugin.

![image](https://user-images.githubusercontent.com/27368585/71487294-4bc89d00-27f9-11ea-8c1d-13ce2d31e714.png)
