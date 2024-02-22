## Implemente microsserviços com Red Hat OpenShift em IBM Cloud
Este tutorial demonstra como implementar uma aplicação no [Red Hat OpenShift on IBM Cloud](https://cloud.ibm.com/kubernetes/catalog/about?platformType=openshift&CAMPAIGN_CODE). O Red Hat OpenShift em IBM Cloud oferece uma ótima experiência para desenvolvedores implementarem aplicativos de software e para administradores de sistema escalarem e observarem os aplicativos em produção.

### Objetivos
- Implantar um microsserviço
- Dimensione o microsserviço
- Observe o cluster usando o IBM Cloud Monitoring

  ![Diagrama de arquitetura LAB IBM Cloud Day](https://github.com/mguedes352/ibmcloud-rhoic/assets/79527238/a26be12d-49df-4525-8a9c-bb8d2a135ff8)


1. Um desenvolvedor inicializa uma aplicação Red Hat OpenShift com uma URL de repositório resultando em **Builder, DeploymentConfig e Service**.
2. O Builder clona a origem, cria uma imagem e envia-a para o registro do Red Hat OpenShift para provisionamento do **DeploymentConfig**.
3. Os usuários acessam o aplicativo frontend.
4. O Log Analysis é provisionado e o agente implantado.
5. O monitoramento é provisionado e o agente implantado.
6. Um administrador monitora o aplicativo com análise e monitoramento de log.

Existem [roteiros](https://github.com/IBM-Cloud/patient-health-frontend/tree/master/scripts). Se você tiver problemas e quiser recomeçar, basta executar o ```destroy.sh``` script e percorrer sequencialmente os scripts que correspondem às etapas de recuperação.

### Configure o acesso ao seu cluster
1. Efetue login no console do IBM Cloud.
2. Selecione a conta para a qual você foi convidado clicando no nome da conta na barra superior (ao lado de ```Manage```).
3. Encontre o cluster atribuído a você na [lista de clusters](https://cloud.ibm.com/kubernetes/clusters?platformType=openshift&CAMPAIGN_CODE).

### Inicializar um Cloud Shell
A [CLI do Red Hat OpenShift Container Platform](https://docs.openshift.com/container-platform/4.12/cli_reference/openshift_cli/getting-started-cli.html) expõe comandos para gerenciar suas aplicações, bem como ferramentas de nível inferior para interagir com cada componente do seu sistema. A CLI está disponível usando o ```oc``` comando.

Para evitar a instalação das ferramentas de linha de comandos, a abordagem recomendada é usar o IBM Cloud Shell.

IBM Cloud Shell é uma área de trabalho de shell baseada em nuvem que pode ser acessada por meio de seu navegador. Ele é pré-configurado com a CLI completa do IBM Cloud e muitos plug-ins e ferramentas que podem ser usados ​​para gerenciar aplicativos, recursos e infraestrutura.

Nesta etapa, você usará o shell do IBM Cloud e o configurará ```oc``` para apontar para o cluster designado a você.

1. Quando o cluster estiver pronto, clique no botão (ao lado da sua conta) no canto superior direito no icone ![image](https://github.com/mguedes352/ibmcloud-rhoic/assets/28928720/3e0f523a-d8c8-4286-9735-14df2356417d) para iniciar uma sessão Cloud Shell . Certifique-se de não fechar esta janela/guia .
2. Verifique a versão da CLI do OpenShift:
```
oc version
```
> A versão precisa ser no mínimo 4.12.x, caso contrário, instale a versão mais recente seguindo [estas instruções](https://cloud.ibm.com/docs/solution-tutorials?topic=solution-tutorials-tutorials&CAMPAIGN_CODE=#getting-started-cloud-shell_oc).
3. A validação do seu cluster é mostrada ao listar todos os clusters:
```
ibmcloud oc clusters
```
4. Inicialize o ```oc``` ambiente de comando substituindo o espaço reservado <seu-nome-do-cluster>:
```
ibmcloud oc cluster config -c openshift-cloud-day-sp --admin
```
5. Verifique se o `oc` comando está funcionando:
```
oc get projects
```

### Implantando um aplicativo
Nesta seção, você implantará um aplicativo Node.js Express chamado ```patient-health-frontend-<seu id único>```, uma interface de usuário para um sistema de registros de saúde de pacientes para demonstrar os recursos do Red Hat OpenShift. Você pode encontrar o repositório GitHub do aplicativo de amostra aqui: https://github.com/IBM-Cloud/paciente-health-frontend

### Criar projeto
Um projeto é uma coleção de recursos gerenciados por uma equipe DevOps. Um administrador criará o projeto e os desenvolvedores poderão criar aplicativos que podem ser construídos e implantados.
1. Navegue até o console da web do Red Hat OpenShift clicando no botão do **console da web do OpenShift** no **Cluster** selecionado.
2. No painel de navegação esquerdo, na perspectiva do **Administrador**, selecione **Início > Visualização Projetos** para exibir todos os projetos.
3. Crie um novo projeto clicando em **Criar Projeto**. No pop-up **Nomeie** o projeto ```example-health-<seu id único>```, adicione caracteres aleatórios ao nome do projeto para que o mesmo seja único entre os participantes do evento, deixe **Nome de exibição** e **descrição** em branco e clique em **Criar**.
4. A página **Detalhes do Projeto** do novo projeto é exibida. Observe que seu contexto é **Administrador > Página inicial > Projetos** à esquerda e **Projetos > Detalhes do projeto > example-health-<seu id único>** na parte superior.

### Construir e implantar aplicativo
1. Mude da perspectiva do **Administrador** para a perspectiva do **Desenvolvedor**. Seu contexto deve ser **Desenvolvedor > +Adicionar** à esquerda e **Projeto: exemplo-saúde** no topo.

<img width="1308" alt="ocp48-project-view" src="https://github.com/mguedes352/ibmcloud-rhoic/assets/79527238/25bfb295-2f59-49fd-be3c-7f18f85d5d32">

2. Vamos construir e implantar o aplicativo selecionando **Import from Git**.
3. Insira o repositório ```https://github.com/IBM-Cloud/patient-health-frontend.git``` no campo URL do repositório Git.
   - Observe a marca verde ```Builder image detected``` e o Node.js 16 (UBI 8).
   - Observe que a imagem do construtor detectou automaticamente a linguagem ```Node.js.``` Se não for detectado, selecione **Node.js** na lista fornecida.
   - **Versão da imagem do construtor** deixe no padrão.
   - **Nome do aplicativo** exclua todos os caracteres e deixe-o vazio (o padrão será **Name**)
   - **Nome**: patient-health-frontend-<seu id único>.
   - Clique no link **Tipo de recurso** e escolha **DeploymentConfig**.
   - Deixe os padrões para outras seleções.
4. Clique em **Criar** na parte inferior da janela para construir e implementar o aplicativo.

### Ver aplicação
1. Você deverá ver o aplicativo que acabou de implantar. Observe que você está na visualização **Topologia** do projeto de integridade de exemplo na perspectiva **Desenvolvedor**. Todos os aplicativos do projeto são exibidos.
2. Selecione o **nó patient-health-frontend-<seu id único>** para exibir a visualização de detalhes do arquivo ```DeploymentConfig```. Observe o **DC** ao lado de **patient-health-frontend-<seu id único>**. Os Pods, Builds, Serviços e Rotas ficam visíveis.

![ocp45-topo-app-details](https://github.com/mguedes352/ibmcloud-rhoic/assets/79527238/e7d65105-8881-4c16-b83f-aa58adaa4e4b)
    
- **Pods**: seus contêineres de aplicativos Node.js
- **Builds** : a compilação gerada automaticamente que criou uma imagem Docker a partir do seu código-fonte Node.js, implantou-a no registro de contêiner do Red Hat OpenShift e iniciou sua configuração de implantação
- **Serviços** : informa ao Red Hat OpenShift como acessar seus pods agrupando-os como um serviço e definindo a porta para escutar
- **Rotas** : expõe seus serviços ao mundo externo usando o LoadBalancer fornecido pela rede IBM Cloud
    
3. Clique em **Exibir logs** ao lado de sua compilação concluída. Isso mostra o processo que o Red Hat OpenShift realizou para instalar as dependências do seu aplicativo Node.js e criar/enviar uma imagem Docker. A última entrada deve ficar assim:
```
Successfully pushed image-registry.openshift-image-registry.svc:5000/example-health/patient-health-frontend-<seu id único>@sha256:f9385e010144f36353a74d16b6af10a028c12d005ab4fc0b1437137f6bd9e20a
Push successful
```
4. Clique novamente na **Topologia** e selecione seu aplicativo novamente.
5. Clique no URL em **Rotas** para visitar seu aplicativo. Insira qualquer string para nome de usuário e senha, por exemplo, ```test:test``` porque o aplicativo está sendo executado em modo de demonstração.

O ```Node.js``` aplicativo foi implantado no Red Hat OpenShift Container Platform. Para recapitular:
 - O aplicativo Node.js "Example Health" foi implantado diretamente do GitHub em seu cluster.
 - O aplicativo foi examinado no console do Red Hat OpenShift on IBM Cloud.
 - Uma **configuração de build** foi criada - um novo commit pode ser compilado e implantado clicando em **Iniciar compilação** na seção Builds dos detalhes do aplicativo.

### Simular Carga na Aplicação

Crie um script para simular carga.

1. Certifique-se de estar conectado ao projeto onde implantou seu aplicativo.
   ```
   oc project example-health-<seu id único>
   ```
2. Recupere a rota pública para acessar seu aplicativo:
   ```
   oc get routes
   ```
   A saída é semelhante a esta, observe seu valor para Host:
   ```
   NAME         HOST/PORT                                                                                                 PATH      SERVICES     PORT       TERMINATION   WILDCARD
   patient-health-frontend   patient-health-frontend-example-health.roks07-872b77d77f69503584da5a379a38af9c-0000.eu-de.containers.appdomain.cloud             patient-health-frontend   8080-tcp                 None
   ```
3. Defina uma variável com o host:
   ```
   HOST=$(oc get routes -o json | jq -r '.items[0].spec.host')
   ```
4. Verifique o acesso ao aplicativo. Ele produz informações do paciente:
   ```
   curl -s -L http://$HOST/info
   ```
   A saída deve ser semelhante a:
   ```
   $ curl -s -L http://$HOST/info
   {"personal":{"name":"Ralph DAlmeida","age":38,"gender":"male","street":"34 Main Street","city":"Toronto","zipcode":"M5H 1T1"},"medications":["Metoprolol","ACE inhibitors","Vitamin D"],"appointments":["2018-01-15 1:00 - Dentist","2018-02-14 4:00 - Internal Medicine","2018-09-30 8:00 - Pediatry"]}
   ```
5. Execute o seguinte script que enviará solicitações infinitamente ao aplicativo e gerará tráfego:
   ```
   while sleep 0.2; do curl --max-time 2 -s -L http://$HOST/info >/dev/null; echo -n "."
   done
   ```
> Para interromper o script, pressione ```CTRL + c``` no teclado.

### Dimensionando o aplicativo

O Red Hat OpenShift fornece uma interface web para executar consultas e examinar as métricas visualizadas em um gráfico. Essa funcionalidade fornece uma visão geral abrangente do estado do cluster e permite solucionar problemas.
Nesta seção, as métricas mencionadas podem ser usadas para dimensionar o aplicativo de UI em resposta ao carregamento.

### Habilitar Limites de Recursos

Antes do escalonamento automático, os limites máximos de recursos de CPU e memória devem ser estabelecidos.

Os painéis anteriores mostraram que a carga estava consumindo algo entre “0,002” e “0,02” núcleos. Isso se traduz em 2 a 20 "milicores". Por segurança, vamos aumentar o limite superior para 30 milicores. Além disso, os dados mostraram que o aplicativo consome cerca de `25` - `65MB` de RAM. As etapas a seguir definirão os limites de recursos no arquivo deployConfig.

1. Certifique-se de que o script para gerar tráfego esteja em execução.
2. Mude para a perspectiva do **Administrador**.
3. Navegue até **Cargas de trabalho > DeploymentConfigs**.
4. Selecione o projeto **example-health-<seu id único>**.
5. No menu **Ações** (os três pontos verticais) de `patient-health-frontend-<seu id único>` , escolha **Editar DeploymentConfig**.

![ocp48-deploymentconfigs](https://github.com/mguedes352/ibmcloud-rhoic/assets/79527238/dc862fb2-5e99-4bc5-b228-6bd8ba60f807)

6. Na **visualização YAML**, encontre a seção **spec > template > spec > containers** e adicione os seguintes limites de recursos aos recursos vazios. Substitua o `resources {}` e certifique-se de que o espaçamento esteja correto - o YAML usa recuo estrito.
```
           resources:
            limits:
              cpu: 30m
              memory: 100Mi
            requests:
              cpu: 3m
              memory: 40Mi
```
Aqui está um trecho depois de fazer as alterações:
```
       ports:
         - containerPort: 8080
           protocol: TCP
       resources:
         limits:
           cpu: 30m
           memory: 100Mi
         requests:
           cpu: 3m
           memory: 40Mi
       terminationMessagePath: /dev/termination-log
```
7. **Salve** para aplicar as alterações.
8. Verifique se o controlador de replicação foi alterado navegando até a guia **Eventos**:

![ocp48-dc-events](https://github.com/mguedes352/ibmcloud-rhoic/assets/79527238/acafae15-f336-46a9-94cc-00156ac1b13e)

### Ativar escalonador automático

Agora que os limites de recursos estão configurados, o escalonador automático de pod pode ser habilitado.

Por padrão, o escalonador automático permite dimensionar com base na CPU ou na memória. Os pods são balanceados entre o número mínimo e máximo de pods que você especifica. Com o escalonador automático, os pods são criados ou excluídos automaticamente para garantir que o uso médio da CPU dos pods esteja abaixo da meta de solicitação da CPU, conforme definido. Em geral, você provavelmente desejará começar a aumentar a escala quando chegar perto de `50 - 90` % do uso da CPU de um pod. No nosso caso, `1` % pode ser usado com a carga fornecida.

1. Navegue até a perspectiva **do administrador Cargas de trabalho > HorizontalPodAutoscalers** e clique em **Criar HorizontalPodAutoscaler**.

![ocp48-hpa](https://github.com/mguedes352/ibmcloud-rhoic/assets/79527238/f324109b-fd35-4c9e-b5fa-78d9a026289b)

Substitua o conteúdo do editor por este yaml(**Inclua no nome "patient-health-frontend" e "example-health" o seu id único**):

```
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: patient-hpa
  namespace: example-health
spec:
  scaleTargetRef:
    apiVersion: apps.openshift.io/v1
    kind: DeploymentConfig
    name: patient-health-frontend
  minReplicas: 1
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          averageUtilization: 1
          type: Utilization
```
2. Clique em **Criar**.

### Teste do escalonador automático

Se você não estiver executando o script para simular a carga, o número de pods deverá permanecer em 1.

1. Verifique abrindo a página **Visão geral** da configuração de implantação. Clique em **Cargas de trabalho > DeploymentConfigs** e clique em **patient-health-frontend-<seu id único>** e certifique-se de que o painel **Detalhes** esteja selecionado.
2. Comece a simular carga.

![ocp48-hpa-after](https://github.com/mguedes352/ibmcloud-rhoic/assets/79527238/c08df654-f31b-4e9b-8457-c0dece938fc6)

> Pode levar alguns minutos para que o autoescalador faça ajustes.

É isso! Agora você tem um aplicativo Node.js front-end altamente disponível e dimensionado automaticamente. O Red Hat OpenShift está escalando automaticamente seus pods de aplicativos, já que o uso de CPU dos pods excedeu em muito a `1` % do limite de recursos, `30` milicores.

### Escalonamento automático a partir da linha de comando

Você também pode excluir e criar recursos como escalares automáticos com a linha de comando.

1. Comece verificando se o contexto é o seu projeto:
```
oc project example-health-<seu id único>
```
2. Obtenha o escalonador automático criado anteriormente:
```
oc get hpa
```
3. Exclua o escalonador automático criado anteriormente:
```
oc delete hpa/patient-hpa
```
4. Crie um novo escalonador automático com no máximo 4 pods(**Inclua no nome "patient-health-frontend" o seu id único**):
```
oc autoscale deploymentconfig/patient-health-frontend --name patient-hpa --min 1 --max 4 --cpu-percent=1
```
5. Visite novamente a página **Cargas de trabalho** > Detalhes de DeploymentConfigs para `patient-health-frontend-<seu id único>` implantação e veja como funciona.

### Conecte o Log Analysis e o Monitoring ao cluster do Red Hat OpenShift em IBM Cloud

Pode levar alguns minutos para que os dados de registro e métricas fluam pelos sistemas de análise, por isso é melhor conectar ambos neste momento para uso posterior.

> Se você foi convidado para uma conta de laboratório à qual uma instância do Monitoring ou Log Analysis já foi conectada, ignore as etapas de conexão. Encontre sua instância do Monitoring observando o nome do cluster nas tags anexadas à instância.

1. Navegue até [os clusters do Red Hat OpenShift on IBM Cloud](https://cloud.ibm.com/kubernetes/clusters?platformType=openshift&CAMPAIGN_CODE)
2. Clique no seu cluster e verifique se a guia **Visão geral** à esquerda está selecionada
3. Role até **Integrações** e se o **Logging** não estiver conectado, clique no botão **Logging Connect**. Use uma instância existente do Log Analysis ou crie uma nova instância conforme mostrado abaixo:
   1. Clique em **Criar uma instância**.
   2. Selecione o mesmo local onde seu cluster foi criado.
   3. Selecione **Pesquisa de registro de 7 dias** como seu plano.
   4. Insira um **nome de serviço** exclusivo , como `.<your-initials>-logging`
   5. Use o grupo de recursos associado ao seu cluster e clique em **Criar**.
4. De volta à guia **Visão geral** do cluster , siga o mesmo procedimento para **Conectar** o monitoramento. Use uma instância existente do Monitoring ou crie uma nova instância conforme mostrado abaixo:
   1. Clique em **Criar uma instância**.
   2. Selecione o mesmo local onde seu cluster foi criado.
   3. Selecione **Nível Graduado** como seu plano.
   4. Insira um **nome de serviço** exclusivo , como `.<your-initials>-monitoring`
   5. Use o grupo de recursos associado ao seu cluster.
   6. Deixe as métricas da plataforma IBM como **Desativar** e clique em **Criar**.

### Configurar monitoramento

A IBM Cloud fornece um serviço de monitoramento totalmente gerenciado. Vamos criar uma instância de monitoramento e, em seguida, integrá-la ao cluster do Red Hat OpenShift on IBM Cloud usando um script que cria um projeto e uma conta de serviço privilegiada para o agente de monitoramento.

### Verifique se o agente do Monitoring foi implementado com sucesso

Verifique se os `sysdig-agentpods` em cada nó têm o status **Em execução**.

Execute o seguinte comando:
```
oc get pods -n ibm-observe
```

Exemplo de saída:
```
NAME                 READY     STATUS    RESTARTS   AGE
sysdig-agent-qrbcq   1/1       Running   0          1m
sysdig-agent-rhrgz   1/1       Running   0          1m
```

### Monitore seu cluster

O IBM Cloud Monitoring é um sistema de gerenciamento de inteligência de contêiner nativo da nuvem que pode ser incluído como parte de sua arquitetura do IBM Cloud. Use-o para obter visibilidade operacional do desempenho e da integridade de seus aplicativos, serviços e plataformas. Ele oferece aos administradores, equipes de DevOps e desenvolvedores telemetria full stack com recursos avançados para monitorar e solucionar problemas de desempenho, definir alertas e projetar painéis personalizados. [Saber mais](https://cloud.ibm.com/docs/monitoring?topic=monitoring-getting-started&CAMPAIGN_CODE=).

Nas próximas etapas, você aprenderá como usar painéis e métricas para monitorar a integridade da sua aplicação.

### Visualize visualizações e painéis de monitoramento predefinidos

Use visualizações e painéis para monitorar sua infraestrutura, aplicativos e serviços. Você pode usar painéis predefinidos. Você também pode criar painéis personalizados por meio da UI da Web ou de forma programática. Você pode fazer backup e restaurar painéis usando scripts Python.

A tabela a seguir lista os diferentes tipos de painéis predefinidos:

| Tipo |	Descrição |
| --- | ---|
| Status e desempenho da carga de trabalho | Painéis que você pode usar para monitorar seus pods. |
| Status e desempenho do nó |	Painéis que você pode usar para monitorar a utilização de recursos e a atividade do sistema em seus hosts e contêineres. |
| Rede |	Painéis que você pode usar para monitorar suas conexões e atividades de rede. |

### Veja o painel de monitoramento

1. Navegue até [os clusters do Red Hat OpenShift on IBM Cloud](https://cloud.ibm.com/kubernetes/clusters?platformType=openshift&CAMPAIGN_CODE) e observe os clusters do Red Hat OpenShift
2. Clique no seu cluster e verifique se a guia **Visão geral** à esquerda está selecionada
3. Na seção **Integrações ao lado de Monitoramento**, clique no botão **Iniciar**.

Os dados iniciais podem NÃO estar disponíveis em instâncias **de monitoramento** recém-criadas.

- Após alguns minutos, os dados brutos serão exibidos
- Após cerca de uma hora, a indexação fornecerá os detalhes necessários para prosseguir com este tutorial
- Na seção **Painéis**, selecione **Kubernetes > Status e desempenho do pod** para visualizar métricas brutas de todas as cargas de trabalho em execução no cluster.
- Defina o filtro de **namespace** como **example-health-<seu id único>** para focar nos pods do seu aplicativo.
- Em **Painéis** no painel esquerdo, expanda **Aplicações** em **Dashboard Templates**. Em seguida, selecione **HTTP** para obter uma visão global da carga HTTP do cluster.

### Explore o cluster e a capacidade do nó

1. Selecione **Dashboards**, confira os dois modelos de dashboard:
   - **Contêineres > Uso de recursos de contêiner**
   - **Infraestrutura de host > Uso de recursos de host**
2. Selecione **Kubernetes** > modelo de **redimensionamento de pod e otimização de capacidade de carga de trabalho**. Este painel ajuda você a otimizar sua infraestrutura e controlar melhor os gastos do cluster, garantindo que os pods sejam dimensionados corretamente. Entenda se você pode liberar recursos reduzindo solicitações de memória e/ou CPU.

### Explore a aplicação

1. Selecione **Dashboards** e o modelo **Kubernetes > Workload Status & Performance**.
   Um painel detalhado mostrando todos os pods do cluster.

2. Crie um painel personalizado e, em seguida, coloque-o em um namespace específico.
   - No canto superior direito, clique em **Copiar para meus painéis** e nomeie-o `Workload Status & Performanceapp example-health-<seu id único>`
   - Clique em **Criar e abrir** para criar seu próprio painel.
   - Edite o escopo do painel.
   - Defina o filtro para `kube_namespace_name, is, example-health-<seu id único>`.
   - Clique em **Salvar**.

O painel agora mostra informações focadas no namespace example-health-<seu id único>.

Role para baixo até TimeCharts para solicitações HTTP, latência, erro, ... para entender o desempenho do aplicativo.

<img width="1612" alt="dashboard-img-5" src="https://github.com/mguedes352/ibmcloud-rhoic/assets/79527238/5573f477-b20c-46df-b784-1bcfa835f9d5">

Encontre mais sobre o IBM Cloud Monitoring na [documentação do IBM Cloud](https://cloud.ibm.com/docs/monitoring?topic=monitoring-getting-started&CAMPAIGN_CODE=).

### Remover recursos
> [!WARNING]
> Exclua todos os objetos de recursos do aplicativo:
```
oc delete all --selector app=$MYPROJECT
```
> [!WARNING]
> Exclua o projeto:
```
oc delete project $MYPROJECT
```

### Conteúdo Relacionado
- [Red Hat OpenShift on IBM Cloud
](https://cloud.ibm.com/docs/openshift?CAMPAIGN_CODE)
- [Analise logs e monitore a integridade do aplicativo](https://cloud.ibm.com/docs/solution-tutorials?topic=solution-tutorials-application-log-analysis&CAMPAIGN_CODE=#application-log-analysis)
- [Escalonamento automático horizontal de pods](https://docs.openshift.com/container-platform/4.12/nodes/pods/nodes-pods-autoscaling.html)
