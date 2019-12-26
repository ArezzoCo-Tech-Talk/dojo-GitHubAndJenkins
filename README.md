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

![image](https://www.vogella.com/tutorials/Jenkins/img/jenkins_pipeline10.png)

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

É a configuração das tarefas ou passos que serão executadas no respectivo Pipeline.

Essa configuração fica no arquivo [JenkinsFile](https://jenkins.io/doc/book/pipeline/jenkinsfile/) na raíz do repositório. O plugin Blue Ocean faz o trabalho de criar esse arquivo para nós de forma mais intuitiva.

![image](https://user-images.githubusercontent.com/27368585/71489803-37d76800-2806-11ea-8bb2-e413de7caf98.png)

Agora se clicarmos em "Create Pipeline" vamos incluir os passos que queremos executar.

Agora na tela do editor do pipeline vamos no (+) para criar um novo passo.

![image](https://user-images.githubusercontent.com/27368585/71490212-82f27a80-2808-11ea-982c-8c0c11492a9d.png)

Vamos agora apenas simular que estamos executando tarefas de verdade colocando apenas mensagens no console.

Começamos pela tarefa "Build" que fingirá compilar nosso projeto.

- Em "Name your stage" colocar "Build".
- "+ Add step."
- "Shell Script".
- O comando do "Build / Shell Script" será `echo 'build'`.
- Save

Como mencionado acima o "Blue Ocean" auxilia na criação do arquivo "Jenkinsfile", então agora temos que escolher em que branch vamos salvar esse arquivo.

![image](https://user-images.githubusercontent.com/27368585/71490677-d36ad780-280a-11ea-837b-0092dccc9012.png)

Após ele rodar podemos ver o resultado daquela tarefa na respectiva branch.

![image](https://user-images.githubusercontent.com/27368585/71490862-fea1f680-280b-11ea-97f2-dd3bf9c807db.png)

Vamos criar mais alguns para ficar visualmente melhor e mais parecido com a realidade.

Como o Pipeline é um arquivo versionado e ele não está presente em todas as branches do projeto vamos na guia "Branches", colocar o mouse sobre a branch que salvamos o Pipeline e editar clicando no lápis.

![image](https://user-images.githubusercontent.com/27368585/71491182-dd420a00-280d-11ea-9a99-89ac560d90ff.png)

Agora vamos clicar no (+) da direita do build e "dizer" que ele vai executar algumas outras tarefas após o build.

Criaremos as seguintes tarefas para serem rodadas em paralelo:

- Test
- Lint
- Coverage

No mesmo padrão de mostrar apenas um console e fingir que deu tudo certo. Toda vez ele vai pedir para salvar.




