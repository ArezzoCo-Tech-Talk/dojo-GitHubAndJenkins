# Integração contínua Jenkins + GitHub

Passo a passo para configurar e utilizar as duas ferramentas.

Este documento contém:

- Configurar Jenkins
- Configurar o GitHub
- Criar o arquivo de pipelines Jenkinsfile no repositório

## Requisitos prévios

- Jenkins já funcionando
- Um repositório no GitHub

### Intalar os Plugins necessários no Jenkins

- Hudson Post Build Task
- GitHub integration plugin
- [Blue Ocean plugin](https://jenkins.io/projects/blueocean/)
- [Embeddable Build Status](https://wiki.jenkins.io/display/JENKINS/Embeddable+Build+Status+Plugin)

Estes serão explicados abaixo.

## Criar um Webhook no GitHub

No repositório `/settings/hooks` configuramos um webhook.

- Payload URL: `<http://your_jenkins_server_url>/github-webhook/`
- Which events would you like to trigger this webhook? Just the push event.

Dessa forma "avisamos" que houve um push e o Jenkins fica sabendo pelo plugin "GitHub integration".

## Criar um [Pipeline](https://jenkins.io/doc/book/pipeline/) no Jenkins

Pipeline é um conjunto de tarefas executadas quando disparado um certo evento, no nosso caso um Push no GitHub.

Estamos criando um Pipeline "Multibranch".

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

![image](https://user-images.githubusercontent.com/27368585/71491295-ac160980-280e-11ea-8588-fa9336c750a4.png)

Vamos agora apenas simular que estamos executando tarefas de verdade colocando apenas mensagens no console.

Começamos pela tarefa "Build" que fingirá compilar nosso projeto.

- Em "Name your stage" colocar "Build".
- "+ Add step."
- "Print Message".
- O comando do "Build / Print Message" será `echo 'build'`.
- Save

Como mencionado acima o "Blue Ocean" auxilia na criação do arquivo "Jenkinsfile", então agora temos que escolher em que branch vamos salvar esse arquivo.

![image](https://user-images.githubusercontent.com/27368585/71490677-d36ad780-280a-11ea-837b-0092dccc9012.png)

Após ele rodar podemos ver o resultado daquela tarefa na respectiva branch.

**Jenkinsfile**
```groovy
pipeline {
  agent any
  stages {
    stage('build') {
      steps {
        echo 'build'
      }
    }

  }
}
```

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

Agora a última tarefa "deploy" é dependente dessas 3. O resultado será mais ou menos isso.

![image](https://user-images.githubusercontent.com/27368585/71491490-bdabe100-280f-11ea-8576-75f31f0c9844.png)

## Configurar o GitHub

Algo que já temos funcionando é quando abrirmos um "Pull Request" já vemos se o pipeline teve sucesso, isso graças ao plugin "Hudson Post Build Task".

![image](https://user-images.githubusercontent.com/27368585/71515418-db199300-2881-11ea-850d-f2cd98dd4b64.png)

Mas não por que uma checagem falhou que você não possa fazer "merge" dessa branch.

### [Definindo se uma branch é "mergeável"](https://help.github.com/en/github/administering-a-repository/enabling-required-status-checks)

Para uma checagem ser bloqueante precisamos, no repositório:

- ir em "settings".
- ir em "branches".
- se não existe regra para a branch ir em "Add rule".
- marcar "Require status checks to pass before merging".
- marcar "Require branches to be up to date before merging" para sempre re-verificar quando houver commit.
- marcar "WIP", deveria ser padrão ativado para evitar merge de branch com trabalho inacabado.
- marcar "continuous-integration/jenkins/pr-request" e "continuous-integration/jenkins/pr-request" (não sei muito bem a diferença, as vezes só aparece um desses no check).

![image](https://user-images.githubusercontent.com/27368585/71518054-a3b1e300-288f-11ea-846d-1802000aaad5.png)

### Mostrando o "status" da branch no "README" do GitHub

Agora usaremos o plugin "Embeddable Build Status" que instalamos no Jenkins.

Vamos incluir no README.md do nosso projeto a seguinte marcação.

```markdown
[![Build Status](http://caminho-do-jenkins/buildStatus/icon?job=nome-do-pipeline%2Fbranch)](http://caminho-do-jenkins/blue/organizations/jenkins/nome-do-pipeline/activity)
```

**Explicando essa linha:** primeiro adicionamos a imagem do status `![qualquer coisa aqui](caminho da imagem de status no Jenkins)`, por tratar-se de um "Multibranch Pipeline" precisamos especificar, a branch também `?job=continuous-integration/master`, porém como na [documentação](https://github.com/jenkinsci/embeddable-build-status-plugin/blob/master/README.md) do plugin é preciso fazer encode dessa string, que fica `?job=continuous-integration%2Fmaster`. Na segunda parte apenas envolvemos isso em um link que leva para o Jenkins.

Resultado: ![image](https://raw.githubusercontent.com/jenkinsci/embeddable-build-status-plugin/master/src/doc/plastic_unconfigured.svg?sanitize=true)

## Sugestão de próximos passos

### Integração com o slack

Os passos de um pileline pode ser de vários tipos, entre eles de integração com o Slack. Pode-se então avisar quando começar o pipeline e avisar quando acaba, se deu certo ou não.

![image](https://user-images.githubusercontent.com/27368585/71516530-38184780-2888-11ea-833c-6cea3cf7e25b.png)

### Não rodar todos os testes de um "Monorepo" a cada commit

No caso dos Monorepos, ou Monolitos, é preciso adotar uma estratégia para evitar que todas as tarefas sejam rodadas a cada push que feito.

Algumas formas:

- Com o atributo "changeset" no pipeline, [StackExchange](https://devops.stackexchange.com/questions/4355/triggering-specific-pipeline-builds-for-monorepos-in-jenkins/6678#6678), [Jenkins Pipeline](https://jenkins.io/doc/book/pipeline/syntax/).
- Usando [Lerna](https://github.com/lerna/lerna) e [Bazel](https://github.com/bazelbuild/bazel)

Um material que pode ajudar na escolha é o [Monorepo vs. polyrepo](https://github.com/joelparkerhenderson/monorepo_vs_polyrepo) do joelparkerhenderson.

### Quebrar o pipeline em tarefas

O pipeline seja um acionador de "jobs" com seu próprio fluxo, evitando assim um pipeline muito extenso.

### Continuous Deployment

Deploy automático quando chegar na branch master.

## Referências

- https://www.vogella.com/tutorials/Jenkins/article.html
- https://applitools.com/blog/how-to-update-jenkins-build-status-in-github-pull-requests-step-by-step-tutorial?utm_referrer=https://www.google.com/
