<properties
   pageTitle="Azure CLI でのデプロイ操作の表示 | Microsoft Azure"
   description="Azure CLI を使用して、リソース マネージャーのデプロイからの問題を検出する方法について説明します。"
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/13/2016"
   ms.author="tomfitz"/>

# Azure CLI でのデプロイ操作の表示

> [AZURE.SELECTOR]
- [ポータル](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

Azure にリソースをデプロイするときにエラーが発生した場合、実行したデプロイ操作に関して、より詳しい情報が必要になることがあります。Azure CLI では、エラーを見つけて、可能性のある修正を確認できるように、コマンドを提供します。

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

デプロイの前にテンプレートおよびインフラストラクチャを検証して、いくつかのエラーを回避できます。後からトラブルシューティングに役立つと思われるデプロイメント中の追加の要求および応答の情報を記録することもできます。検証、ログ記録の要求および応答の情報については、「[Azure リソース マネージャーのテンプレートを使用したリソース グループのデプロイ](resource-group-template-deploy-cli.md)」を参照してください。

## 監査ログを使用してトラブルシューティングを行う

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

デプロイのエラーを表示するには、次の手順を使用します。

1. 監査ログを表示するには、**azure group log show** コマンドを実行します。**--last-deployment** オプションを含めると、最新のデプロイのログのみを取得できます。

        azure group log show ExampleGroup --last-deployment

2. **azure group log show** コマンドを使用すると、多数の情報が返される可能性があります。通常、トラブルシューティングを行う場合は、失敗した操作に重点的に取り組みます。次のスクリプトは **--json** オプションと [jq](https://stedolan.github.io/jq/) JSON ユーティリティを使用して、デプロイ エラーのログを検索します。

        azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'
        
        {
        "claims": {
          "aud": "https://management.core.windows.net/",
          "iss": "https://sts.windows.net/<guid>/",
          "iat": "1442510510",
          "nbf": "1442510510",
          "exp": "1442514410",
          "ver": "1.0",
          "http://schemas.microsoft.com/identity/claims/tenantid": "{guid}",
          "http://schemas.microsoft.com/identity/claims/objectidentifier": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
          "puid": "XXXXXXXXXXXXXXXX",
          "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",
          "altsecid": "1:example.com:XXXXXXXXXXX",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string="">",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
          "name": "Tom FitzMacken",
          "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
          "groups": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
          "wids": "{guid}",
          "appid": "{guid}",
          "appidacr": "0",
          "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
          "http://schemas.microsoft.com/claims/authnclassreference": "1",
          "ipaddr": "000.000.000.000"
        },
        "properties": {
          "statusCode": "Conflict",
          "statusMessage": "{"Code":"Conflict","Message":"Website with given name mysite already exists.","Target":null,"Details":[{"Message":"Website with given name
            mysite already exists."},{"Code":"Conflict"},{"ErrorEntity":{"Code":"Conflict","Message":"Website with given name mysite already exists.","ExtendedCode":
            "54001","MessageTemplate":"Website with given name {0} already exists.","Parameters":["mysite"],"InnerErrors":null}}],"Innererror":null}"
        },
        ...

    json の **properties** には失敗した操作の情報が含まれます。

    **--verbose** および **-vv** オプションを使用すると、ログからさらに詳細を得られます。操作の進行手順を `stdout` に表示するには、**--verbose** を使用してください。要求の完全な履歴については、**-vv** オプションを使用してください。多くの場合、メッセージはエラーの原因に関する重要な手掛かりを提供します。

3. 失敗したエントリのステータス メッセージに注目するには、次のコマンドを使用します。

        azure group log show ExampleGroup --json | jq -r ".[] | select(.status.value == "Failed") | .properties.statusMessage"


## デプロイ操作を使用してトラブルシューティングを行う

1. **azure group deployment show** コマンドを使用して、デプロイ全体の状態を取得できます。以下の例では、デプロイは失敗しています。

        azure group deployment show --resource-group ExampleGroup --name ExampleDeployment
        
        info:    Executing command group deployment show
        + Getting deployments
        data:    DeploymentName     : ExampleDeployment
        data:    ResourceGroupName  : ExampleGroup
        data:    ProvisioningState  : Failed
        data:    Timestamp          : 2015-08-27T20:03:34.9178576Z
        data:    Mode               : Incremental
        data:    Name             Type    Value
        data:    ---------------  ------  ------------
        data:    siteName         String  ExampleSite
        data:    hostingPlanName  String  ExamplePlan
        data:    siteLocation     String  West US
        data:    sku              String  Free
        data:    workerSize       String  0
        info:    group deployment show command OK

2. デプロイに失敗した操作のメッセージを表示するには、次を使用します。

        azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json  | jq ".[] | select(.properties.provisioningState == "Failed") | .properties.statusMessage.Message"


## 次のステップ

- 特定のデプロイ エラーの解決については、[Azure Resource Manager を使用してリソースを Azure にデプロイするときに発生する一般的なエラーの解決](resource-manager-common-deployment-errors.md)に関するページを参照してください。
- 他の種類のアクションを監視するために監査ログを使用する方法については、「[Resource Manager の監査操作](resource-group-audit.md)」を参照してください。
- デプロイを実行する前に検証するには、「[Azure Resource Manager のテンプレートを使用したリソース グループのデプロイ](resource-group-template-deploy.md)に関するページを参照してください。

<!---HONumber=AcomDC_0615_2016-->