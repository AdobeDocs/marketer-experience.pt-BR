---
title: Evento unitário
description: Esta é uma página de instruções para simular o tipo de validação da jornada “[!UICONTROL Evento unitário]”.
source-git-commit: 0a52611530513fc036ef56999fde36a32ca0f482
workflow-type: ht
source-wordcount: '749'
ht-degree: 100%

---


# Evento unitário

## Etapas a seguir {#steps-to-follow}

>[!CONTEXTUALHELP]
>id="marketerexp_sampledata_unitaryevent"
>title="Como usar?"
>abstract="Acesse o link para obter mais detalhes"

>[!IMPORTANT]
>
>Estas instruções podem mudar entre **[!UICONTROL manuais de estratégia]**. Consulte sempre a seção de dados de amostra do respectivo **[!UICONTROL manual de estratégia]**.

## Pré-requisito

* É necessário ter o software Postman instalado
* Use o manual de estratégia para criar ativos de instâncias como **[!UICONTROL Jornadas]**, **[!UICONTROL Esquemas]**, **[!UICONTROL Segmentos]**, **[!UICONTROL Mensagens]**, etc.

Os ativos criados serão mostrados na página `Bill Of Material`

![Página da Lista de materiais](../assets/bom-page.png)

## Preparar o Postman com a coleção necessária

1. Visite o aplicativo **[!UICONTROL Manual de estratégia de casos de uso]**.
1. Clique no cartão do respectivo **[!UICONTROL manual de estratégias]** para visitar a página de detalhes do **[!UICONTROL manual de estratégias]**.
1. Visite a página da **[!UICONTROL Lista de materiais]** e localize a seção **[!UICONTROL Dados de amostra]**.
1. Faça o download do `postman.json` clicando nos respectivos botões na interface.
1. Importe o `postman.json` no **[!DNL Postman Software]**.
1. Crie um ambiente do Postman dedicado para esta validação (por exemplo, `Adobe <PLAYBOOK_NAME>`).

## Buscar token IMS

>[!NOTE]
>
>Todas as variáveis de ambiente diferenciam maiúsculas de minúsculas, portanto, use sempre o nome exato da variável.

1. Siga a documentação [Autenticar e acessar APIs da Experience Platform](https://experienceleague.adobe.com/docs/experience-platform/landing/platform-apis/api-authentication.html?lang=pt-BR) para gerar o token de acesso.
1. Armazene o valor do token de acesso em variáveis de ambiente denominadas `ACCESS_TOKEN`.
1. Armazene outros valores relacionados à autenticação, como `API_KEY`, `IMS_ORG` e `SANDBOX_NAME`, em variáveis de ambiente.

>[!IMPORTANT]
>
>Antes de executar qualquer API a partir do Postman, certifique-se de que todas as variáveis de ambiente necessárias sejam adicionadas.

## Publicar a jornada criada pelo manual de estratégia

Existem 2 maneiras de publicar a jornada. Você pode escolher qualquer uma delas:

1. **Usando a interface do AJO**: clique no link da jornada em `Bill Of Material Page`. Isso irá redirecioná-lo para a página da jornada, onde você poderá clicar no botão **[!UICONTROL Publicar]** e a jornada será publicada.

   ![Objeto da jornada](../assets/journey-object.png)

1. **Utilização da API do Postman**

   1. Acione a solicitação **[!DNL Publish Journey]** em **[!DNL Journey Publish]** > **[!DNL Queue journey publish job]**.
   1. A publicação da jornada pode levar algum tempo. Para verificar o status, execute a API Verificar status da publicação da jornada, até que o `response.status` seja `SUCCESS`. Certifique-se de aguardar de 10 a 15 segundos se a publicação da jornada demorar.

   >[!NOTE]
   >
   >Todas as variáveis de ambiente diferenciam maiúsculas de minúsculas, portanto, use sempre o nome exato da variável.

## Assimilar o perfil do cliente

>[!TIP]
>
>É possível reutilizar o mesmo endereço de email anexando `+<variable>` no email, por exemplo, `usertest@email.com` pode ser reutilizado como `usertest+v1@email.com` ou `usertest+24jul@email.com`. Isso é útil para criar um perfil novo a cada vez, mas ainda usando a mesma ID de email.

1. Usuários e usuárias iniciantes precisam criar o **[!DNL customer dataset]** e o **[!DNL HTTP Streaming Inlet Connection]**.
1. Se você já criou o **[!DNL customer dataset]** e o **[!DNL HTTP Streaming Inlet Connection]**, pule para a etapa `5`.
1. Acione **[!DNL Customer Profile Ingestion]** > **[!DNL Create Customer Profile InletId]** > **[!DNL Create Dataset]** para criar um **[!DNL customer dataset]**. Isso armazenará um `CustomerProfile_dataset_id` em variáveis de ambiente do Postman.
1. Crie uma **[!DNL HTTP Streaming Inlet Connection]**, use APIs do Postman em **[!DNL Customer Profile Ingestion > Create Customer Profile InletId]**.

   1. O `CustomerProfile_dataset_id` precisa estar disponível nas variáveis de ambiente do Postman, caso contrário, consulte a etapa `3`.
   1. Acione **[!DNL `CREATE Base Connection`]** para [!DNL create base connection].
   1. Acione **[!DNL `CREATE Source Connection`]** para [!DNL create source connection].
   1. Acione **[!DNL `CREATE Target Connection`]** para [!DNL create target connection].
   1. Acione **[!DNL `CREATE Dataflow`]** para [!DNL create dataflow].
   1. Acione **[!DNL `GET Base Connection`]**. Isso armazenará `CustomerProfile_inlet_id` automaticamente nas variáveis de ambiente do Postman.

1. Nesta etapa, é necessário possuir o `CustomerProfile_dataset_id` e o `CustomerProfile_inlet_id` nas variáveis de ambiente do Postman, caso contrário, consulte a etapa `3` ou `4`, respectivamente.
1. Para assimilar clientes, é necessário armazenar `customer_country_code`, `customer_mobile_no`, `customer_first_name`, `customer_last_name` e `email` nas variáveis de ambiente do Postman.

   1. `customer_country_code` seria o código do país do número de celular, por exemplo `91` ou `1`
   1. `customer_mobile_no` seria o número do celular, por exemplo `9987654321`
   1. `customer_first_name` seria o nome da pessoa
   1. `customer_last_name` seria o sobrenome da pessoa
   1. `email` seria o endereço de email da pessoa. É crucial usar uma ID de email distinta para que um novo perfil possa ser assimilado.

1. Atualize a solicitação do Postman **[!DNL Customer Ingestion]** >**[!DNL Customer Streaming Ingestion]** para alterar o canal preferencial do cliente. Por padrão, [!DNL `email`] está configurado na solicitação.

   ```js
   "consents": {
       "marketing": {
           "preferred": "email",
           "email": {
               "val": "y"
           },
           "push": {
               "val": "n"
           },
           "sms": {
               "val": "n"
           }
       }
   }
   ```

1. Altere o canal preferencial para `sms` ou `push` e torne o respectivo valor do canal `y` e `n` para outros valores, por exemplo

   ```js
   "consents": {
       "marketing": {
           "preferred": "sms",
           "email": {
               "val": "n"
           },
           "push": {
               "val": "n"
           },
           "sms": {
               "val": "y"
           }
       }
   }
   ```

1. Finalmente, acione **[!DNL `Customer Profile Ingestion > Customer Profile Streaming Ingestion`]** para assimilar o perfil do cliente.

## Evento de assimilação

1. Usuários e usuárias iniciantes precisam criar o **[!DNL event dataset]** e o **[!DNL HTTP Streaming Inlet Connection for events]**
1. Se você já criou o **[!DNL event dataset]** e o **[!DNL HTTP Streaming Inlet Connection for events]**, pule para a etapa `5`.
1. Acione **[!DNL `Schemas Data Ingestion > AEP Demo Schema Ingestion > Create AEP Demo Schema InletId > Create Dataset`]** para criar **[!DNL event dataset]**. Isso armazenará um `AEPDemoSchema_dataset_id` nas variáveis de ambiente do Postman
1. Crie **[!DNL HTTP Streaming Inlet Connection for events]**. Use as APIs do Postman em **[!DNL Schemas Data Ingestion]** > **[!DNL AEP Demo Schema Ingestion]** > **[!DNL Create AEP Demo Schema InletId]**.

   1. O `AEPDemoSchema_dataset_id` precisa estar disponível nas variáveis de ambiente do Postman, caso contrário, consulte a etapa `3`
   1. Acione **[!DNL `CREATE Base Connection`]** para [!DNL create base connection]
   1. Acione **[!DNL `CREATE Source Connection`]** para [!DNL create source connection]
   1. Acione **[!DNL `CREATE Target Connection`]** para [!DNL create target connection]
   1. Acione **[!DNL `CREATE Dataflow`]** para [!DNL create dataflow]
   1. Acione **[!DNL `GET Base Connection`]**. Isso irá armazenar `AEPDemoSchema_inlet_id` automaticamente nas variáveis de ambiente do Postman

1. Nesta etapa, é necessário ter `AEPDemoSchema_dataset_id` e `AEPDemoSchema_inlet_id` nas variáveis de ambiente do Postman, caso contrário, consulte a etapa `3` ou `4`, respectivamente
1. Para assimilar o evento, será necessário alterar a variável de tempo `timestamp` no corpo da solicitação de **[!DNL Schemas Data Ingestion]** > **[!DNL AEP Demo Schema Ingestion]** > **[!DNL AEP Demo Schema Streaming Ingestion]** no Postman.

   1. `timestamp` seria a hora da ocorrência do evento. Use o carimbo de data/hora atual, por exemplo, `2023-07-21T16:37:52+05:30`. Ajuste o fuso horário conforme necessário.

1. Acione **[!DNL Schemas Data Ingestion > AEP Demo Schema Ingestion > AEP Demo Schema Streaming Ingestion]** para assimilar o evento, para que a jornada possa ser acionada

## Validação final

É necessário receber uma mensagem no canal de sua preferência selecionado, usado na etapa `8` do **[!DNL Ingest the Customer Profile]**

* `SMS` se o canal preferido for `sms` em `customer_country_code` e `customer_mobile_no`
* `Email` se o canal preferido for `email` em `email`

Também é possível verificar o `Journey Report`. Para verificá-lo, clique em `Journey Object` na `Bill of Materials page`, isso irá redirecioná-lo para `Journey Details page`.

Em qualquer jornada publicada, é necessário obter um botão **[!UICONTROL Exibir relatório]** 
![Página do relatório da jornada](../assets/journey-report-page.png)


## Limpar

Não tenha múltiplas instâncias do `Journey` executando simultaneamente. Interrompa a jornada se for apenas para validação assim que esta for concluída.
