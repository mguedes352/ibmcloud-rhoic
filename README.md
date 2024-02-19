# ibmcloud-rhoic
Implemente microsserviços com Red Hat OpenShift em IBM Cloud.

## Implemente microsserviços com Red Hat OpenShift em IBM Cloud
Este tutorial demonstra como implementar aplicativos no [Red Hat OpenShift on IBM Cloud](https://cloud.ibm.com/kubernetes/catalog/about?platformType=openshift&CAMPAIGN_CODE). O Red Hat OpenShift on IBM Cloud oferece uma ótima experiência para desenvolvedores implementarem aplicativos de software e para administradores de sistema escalarem e observarem os aplicativos em produção.

### Objetivos
- Implantar um microsserviço
- Dimensione o microsserviço
- Observe o cluster usando análise de log *
- Observe o cluster usando o IBM Cloud Monitoring

  *Diagrama

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

1. Quando o cluster estiver pronto, clique no botão (ao lado da sua conta) no canto superior direito para iniciar um Cloud Shell . Certifique-se de não fechar esta janela/guia .
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
ibmcloud oc cluster config -c <seu-nome-do-cluster> --admin
```
5. Verifique se o occomando está funcionando:
```
oc get projects
```
