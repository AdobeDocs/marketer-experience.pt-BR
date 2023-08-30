---
title: Evento unitário
description: Esta é uma página de instrução para simular o '[!UICONTROL Evento unitário]Tipo de validação de Jornada '.
source-git-commit: 0a52611530513fc036ef56999fde36a32ca0f482
workflow-type: tm+mt
source-wordcount: '749'
ht-degree: 0%

---


# Evento unitário

## Etapas a serem seguidas {#steps-to-follow}

>[!CONTEXTUALHELP]
>id="marketerexp_sampledata_unitaryevent"
>title="Como usar?"
>abstract="Siga o link para obter mais detalhes"

>[!IMPORTANT]
>
>Essas instruções podem mudar entre **[!UICONTROL Manual]** portanto, sempre consulte a seção Dados de amostra dos respectivos **[!UICONTROL Manual]**.

## Pré-requisito

* É necessário ter o software Postman instalado
* Use o manual para criar os ativos das instâncias como **[!UICONTROL Jornada]**, **[!UICONTROL Esquemas]**, **[!UICONTROL Segmentos]**, **[!UICONTROL Mensagens]** etc.

Os ativos criados serão exibidos em `Bill Of Material` Página

![Página da Lista de Materiais](../assets/bom-page.png)

## Preparar o Postman com a coleção necessária

1. Visita **[!UICONTROL Manual de casos de uso]** aplicação.
1. Clique no respectivo **[!UICONTROL Manual]** cartão a visitar **[!UICONTROL Manual]** página de detalhes.
1. Visita **[!UICONTROL Lista de Materiais]** e localize a **[!UICONTROL Dados de exemplo]** seção.
1. Baixe o `postman.json` clicando nos respectivos botões na interface do usuário do.
1. Importar `postman.json` no **[!DNL Postman Software]**.
1. Crie um Ambiente Postman dedicado para essa validação (por exemplo, `Adobe <PLAYBOOK_NAME>`).

## Buscar token IMS

>[!NOTE]
>
>Todas as variáveis de ambiente diferenciam maiúsculas de minúsculas, portanto, sempre use o nome exato da variável.

1. Siga [Autenticar e acessar APIs de Experience Platform](https://experienceleague.adobe.com/docs/experience-platform/landing/platform-apis/api-authentication.html) documentação para gerar o token de acesso.
1. Armazene o valor do token de acesso em variáveis de ambiente chamadas `ACCESS_TOKEN`.
1. Armazenar outros valores relacionados à autenticação, como `API_KEY`, `IMS_ORG` e `SANDBOX_NAME` em Variáveis de ambiente.

>[!IMPORTANT]
>
>Antes de executar qualquer API do Postman, verifique se todas as variáveis de Ambiente necessárias devem ser adicionadas.

## Publicar a Jornada criada pelo manual

Há duas maneiras de publicar a jornada: você pode escolher qualquer uma delas:

1. **Uso da interface do AJO** - clique no link Jornada em `Bill Of Material Page`; isso redirecionará você para a página do Jornada onde você pode clicar **[!UICONTROL Publish]** e a Jornada serão publicadas.

   ![Jornada objeto](../assets/journey-object.png)

1. **Uso da API do Postman**

   1. Acionador **[!DNL Publish Journey]** solicitação de **[!DNL Journey Publish]** > **[!DNL Queue journey publish job]**.
   1. A publicação do Jornada pode levar algum tempo, portanto, para verificar o status, execute a API Verificar status de publicação do Jornada, até que o `response.status` é `SUCCESS`, aguarde de 10 a 15 segundos se a publicação do jornada levar tempo.

   >[!NOTE]
   >
   >Todas as variáveis de ambiente diferenciam maiúsculas de minúsculas, portanto, sempre use o nome exato da variável.

## Assimilar o perfil do cliente

>[!TIP]
>
>Você pode reutilizar o mesmo endereço de email anexando `+<variable>` no seu email, por exemplo, `usertest@email.com` pode ser retomado como `usertest+v1@email.com` ou `usertest+24jul@email.com`. Isso seria útil para ter um perfil novo toda vez, mas ainda usando a mesma id de email.

1. O usuário iniciante precisa criar o **[!DNL customer dataset]** e **[!DNL HTTP Streaming Inlet Connection]**.
1. Se você já tiver criado o **[!DNL customer dataset]** e **[!DNL HTTP Streaming Inlet Connection]**, pule para a etapa `5`.
1. Acionador **[!DNL Customer Profile Ingestion]** > **[!DNL Create Customer Profile InletId]** > **[!DNL Create Dataset]** para criar **[!DNL customer dataset]**; isso armazenará uma `CustomerProfile_dataset_id` em variáveis de ambiente do postman.
1. Criar **[!DNL HTTP Streaming Inlet Connection]**, use APIs do Postman em **[!DNL Customer Profile Ingestion > Create Customer Profile InletId]**.

   1. `CustomerProfile_dataset_id` deve estar disponível nas variáveis de ambiente do postman, caso contrário, consulte a etapa `3`.
   1. Acionador **[!DNL `CREATE Base Connection`]** para [!DNL create base connection].
   1. Acionador **[!DNL `CREATE Source Connection`]** para [!DNL create source connection].
   1. Acionador **[!DNL `CREATE Target Connection`]** para [!DNL create target connection].
   1. Acionador **[!DNL `CREATE Dataflow`]** para [!DNL create dataflow].
   1. Acionador **[!DNL `GET Base Connection`]**- armazenará automaticamente `CustomerProfile_inlet_id` nas variáveis de ambiente do postman.

1. Nesta etapa, você deve ter `CustomerProfile_dataset_id` e `CustomerProfile_inlet_id` em variáveis de ambiente do postman; caso contrário, consulte a etapa `3` ou `4` respectivamente.
1. Para assimilar clientes, o usuário precisa armazenar `customer_country_code`, `customer_mobile_no`, `customer_first_name`, `customer_last_name` e `email` em variáveis de ambiente do postman.

   1. `customer_country_code` seria o código do país do número do celular, por exemplo `91` ou `1`
   1. `customer_mobile_no` seria o número do celular, por exemplo `9987654321`
   1. `customer_first_name` seria o nome do usuário
   1. `customer_last_name` seria o sobrenome do usuário
   1. `email` seria o endereço de email do usuário, é essencial usar uma id de email distinta para que um novo perfil possa ser assimilado.

1. Atualizar a solicitação do Postman **[!DNL Customer Ingestion]** > **[!DNL Customer Streaming Ingestion]** para alterar o canal preferido do cliente; por padrão [!DNL `email`] está configurado na solicitação.

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

1. Alterar canal preferido para `sms` ou `push` e atribuir o respectivo valor de canal a `y` e `n` para outros valores, por exemplo,

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

1. Acionador final **[!DNL `Customer Profile Ingestion > Customer Profile Streaming Ingestion`]** para assimilar o perfil do cliente.

## Evento de assimilação

1. Primeiro usuário precisa criar a variável **[!DNL event dataset]** e **[!DNL HTTP Streaming Inlet Connection for events]**
1. Se você já tiver criado o **[!DNL event dataset]** e **[!DNL HTTP Streaming Inlet Connection for events]**, pule para a etapa `5`.
1. Acionador **[!DNL `Schemas Data Ingestion > AEP Demo Schema Ingestion > Create AEP Demo Schema InletId > Create Dataset`]** para criar **[!DNL event dataset]**, armazenará uma `AEPDemoSchema_dataset_id` em variáveis de ambiente do postman
1. Criar **[!DNL HTTP Streaming Inlet Connection for events]**, use APIs do Postman em **[!DNL Schemas Data Ingestion]** > **[!DNL AEP Demo Schema Ingestion]** > **[!DNL Create AEP Demo Schema InletId]**.

   1. `AEPDemoSchema_dataset_id` deve estar disponível nas variáveis de ambiente do postman, caso contrário, consulte a etapa `3`
   1. Acionador **[!DNL `CREATE Base Connection`]** para [!DNL create base connection]
   1. Acionador **[!DNL `CREATE Source Connection`]** para [!DNL create source connection]
   1. Acionador **[!DNL `CREATE Target Connection`]** para [!DNL create target connection]
   1. Acionador **[!DNL `CREATE Dataflow`]** para [!DNL create dataflow]
   1. Acionador **[!DNL `GET Base Connection`]**- armazenará automaticamente `AEPDemoSchema_inlet_id` nas variáveis de ambiente do postman

1. Nesta etapa, você deve ter `AEPDemoSchema_dataset_id` e `AEPDemoSchema_inlet_id` em variáveis de ambiente do postman, se não, consulte a etapa `3` ou `4` respectivamente
1. Para assimilar um evento, o usuário precisa alterar a variável de tempo `timestamp` no corpo da solicitação de **[!DNL Schemas Data Ingestion]** > **[!DNL AEP Demo Schema Ingestion]** > **[!DNL AEP Demo Schema Streaming Ingestion]** no carteiro.

   1. `timestamp` Caso contrário, use o carimbo de data e hora atual, por exemplo: `2023-07-21T16:37:52+05:30` ajuste o fuso horário de acordo com sua necessidade.

1. Acionador **[!DNL Schemas Data Ingestion > AEP Demo Schema Ingestion > AEP Demo Schema Streaming Ingestion]** para assimilar o evento, para que a jornada possa ser acionada

## Validação Final

Você deve receber uma mensagem no canal preferido selecionado usado em **[!DNL Ingest the Customer Profile]** etapa `8`

* `SMS` se o canal preferencial for `sms` em `customer_country_code` e `customer_mobile_no`
* `Email` se o canal preferencial for `email` em `email`

Como alternativa, você pode verificar `Journey Report`, para verificá-lo, clique em `Journey Object` em `Bill of Materials page` isso redirecionará você para `Journey Details page`.

Para qualquer usuário do Jornada publicado, é necessário obter uma **[!UICONTROL Exibir relatório]** botão
![Jornada página de relatório](../assets/journey-report-page.png)


## Limpar

Não use as várias instâncias de `Journey` em execução simultânea, interrompa a Jornada se ela for apenas para validação depois que a validação for concluída.
