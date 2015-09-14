<properties
   pageTitle="Azure でのリソース グループのデプロイのトラブルシューティング"
	description="Azure でリソースをデプロイする際の一般的な問題について説明するとともに、Azure ポータルと、Mac、Linux、Windows 用 Azure コマンド ライン インターフェイス (Azure CLI)、 PowerShell を使用してデプロイを確認し、問題を検出する方法を示しています。"
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor=""/>

<tags
   ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="command-line-interface"
	ms.workload="infrastructure"
	ms.date="08/26/2015"
	ms.author="rasquill"/>

# Azure でのリソース グループのデプロイのトラブルシューティング

デプロイの失敗には、さまざま原因が考えられます。デプロイ エラーを防ぐため、いくつかの点を事前にチェックすることをお勧めします。このドキュメントでは、単純なミスの防止、テンプレート ファイルのダウンロードや、デプロイ ログの確認を行うためのツールと操作について説明します。また、エラーに関してデプロイメントのログを確認するときに考慮する主な領域についても説明します。

## Azure とやり取りする便利なツール
コマンドラインから Azure リソースを使用する場合に、作業に役立つツールがあります。Azure リソース グループのテンプレートは JSON ドキュメントで、Azure リソース マネージャー API は JSON を受信して返します。このため、JSON 解析ツールは、リソースに関する情報を参照したり、テンプレートとテンプレートのパラメーター ファイルを設計し、操作する際に最初に使用するツールの 1 つになります。

### Mac、Linux、Windows の各ツール
Mac、Linux、Windows 用の Azure CLI を使用する場合、標準のダウンロード ツール (**[curl](http://curl.haxx.se/)** と **[wget](https://www.gnu.org/software/wget/)** または **[Resty](https://github.com/beders/Resty)** など)、JSON ユーティリティ (**[jq](http://stedolan.github.io/jq/download/)**、**[jsawk](https://github.com/micha/jsawk)** など)や、JSON を適切に処理する言語ライブラリを既に使い慣れていることと思います(これらのツールの多くには、[wget](http://gnuwin32.sourceforge.net/packages/wget.htm) などの Windows 用のポートもあります。実際、Linux とその他のオープン ソース ソフトウェア ツールを Windows でも実行させるいくつかの方法があります)。

このトピックには、**jq** とともに使用して、正確な情報をより効率的に取得する Azure CLI コマンドが含まれています。Azure リソースの使用状況を理解するには、使い慣れているツールセットを選択する必要があります。

### Windows PowerShell

Windows PowerShell には、同じ手順を実行するいくつかの基本的なコマンドがあります。

- **[Invoke-WebRequest](https://technet.microsoft.com/library/hh849901%28v=wps.640%29)** コマンドレットを使用すると、リソース グループ テンプレートやパラメーターの JSON ファイルなどのファイルをダウンロードできます。
- **[ConvertFrom-Json](https://technet.microsoft.com/library/hh849898%28v=wps.640%29.aspx)** コマンドレットを使用すると、JSON 文字列を、JSON 文字列の各フィールドにプロパティを持つカスタム オブジェクト ([PSCustomObject](https://msdn.microsoft.com/library/windows/desktop/system.management.automation.pscustomobject%28v=vs.85%29.aspx)) に変換できます。

## Mac、Linux、および Windows 用の Azure CLI でエラーを回避する

Azure CLI には、エラーを防止し、またエラー発生時には不具合を検出する複数のコマンドがあります。

- **azure location list**。このコマンドでは、Virtual Machines のプロバイダーなどの各種リソースをサポートする場所を取得します。リソースの場所を入力する前に、このコマンドを使用して、その場所がリソースの種類をサポートしていることを確認します。

    場所の一覧は長くなることがあり、多数のプロバイダーがあるため、ツールを使用して、まだ使用可能でない場所を使用する前に、プロバイダーと場所を確認します。次のスクリプトでは、**jq** を使用して、Azure Virtual Machines のリソース プロバイダーが使用可能な場所を探索します。

        azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
        {
          "name": "Microsoft.Compute/virtualMachines",
          "location": "East US,West US,West Europe,East Asia,Southeast Asia,North Europe"
        }

- **azure group template validate <resource group>**。このコマンドでは、テンプレートとテンプレート パラメーターを使用する前に検証します。カスタム テンプレートまたはギャラリー テンプレートと、使用するテンプレート パラメーター値を入力します。

    次の例では、テンプレートと、必要なパラメーターを検証する方法を示します。Azure CLI が、必要なパラメーター値を要求します。

        azure group template validate \
        > --template-uri "https://contoso.com/templates/azuredeploy.json" \
        > resource-group
        info:    Executing command group template validate
        info:    Supply values for the following parameters
        adminUserName: UserName
        adminPassword: PassWord
        + Initializing template configurations and parameters
        + Validating the template
        info:    group template validate command OK

## Azure CLI を使用してデプロイメントの問題を修正する情報を取得する

- **azure group log show <resource group>**: このコマンドは、リソース グループの各デプロイメントのログのエントリを取得します。問題が生じた場合は、デプロイ ログを調べることにから開始します。

        info:    Executing command group log show
        info:    Getting group logs
        data:    ----------
        data:    EventId:              <guid>
        data:    Authorization:
        data:                          action: Microsoft.Network/networkInterfaces/write
        data:                          role:   Subscription Admin
        data:                          scope:  /subscriptions/xxxxxxxxxxx/resourcegroups/templates/
                                               providers/Microsoft.Network/
                                               networkInterfaces/myNic
        data:    ResourceUri:          /subscriptions/xxxxxxxxxxxx/resourcegroups/templates/providers/
                                       Microsoft.Network/networkInterfaces/myNic
        data:    SubscriptionId:       <guid>
        data:    EventTimestamp (UTC): Wed Apr 22 2015 05:53:31 GMT+0000 (UTC)
        data:    OperationName:        Microsoft.Network/networkInterfaces/write
        data:    OperationId:          <guid>
        data:    Status:               Started
        data:    SubStatus:
        data:    Caller:
        data:    CorrelationId:        <guid>
        data:    Description:
        data:    HttpRequest:          clientRequestId: <guid>
                                       clientIpAddress: 000.000.00.000
                                       method:          PUT

        data:    Level:                Informational
        data:    ResourceGroup:        templates
        data:    ResourceProvider:     Microsoft.Network
        data:    EventSource:          Microsoft Resources
        data:    Properties:           requestbody: {"location":"West US","properties
                                       ":{"ipConfigurations":[{"
                                       name":"ipconfig1","properties":{"
                                       privateIPAllocationMethod

                                       ":"Dynamic","publicIPAddress":{"id":"/
                                       subscriptions/
                                       <guid>/
                                       resourceGroups/
                                       templates/providers/Microsoft.Network/
                                       publicIPAddresses/
                                       myPublicIP"},"subnet":{"idThe AzureResourceManager module includes cmdlets that ":"/subscriptions/
                                       <guid>/resourceGroups/templates/
                                       providers/
                                       Microsoft.Network/virtualNetworks/myVNET/subnets/
                                       Subnet-1
                                       "}}}]}}

**--last-deployment** オプションを使用して、最新のデプロイのログのみを取得します。次のスクリプトは **--json** オプションと **jq** を使用して、デプロイ エラーのログを検索します。

        azure group log show templates --json | jq '.[] | select(.status.value == "Failed")'

        {
          "claims": {
            "aud": "https://management.core.windows.net/",
            "iss": "https://sts.windows.net/<guid>/",
            "iat": "1429678549",
            "nbf": "1429678549",
            "exp": "1429682449",
            "ver": "1.0",
            "http://schemas.microsoft.com/identity/claims/tenantid": "<guid>",
            "http://schemas.microsoft.com/identity/claims/objectidentifier": "<guid>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "ahmet@contoso.onmicrosoft.com",
            "puid": "XXXXXXXXXXXXXX",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "ahmet",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "",
            "name": "Friendly Name",
            "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
            "groups": "<guid>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "ahmet@contoso.onmicrosoft.com",
            "appid": "<guid>",
            "appidacr": "0",
            "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
            "http://schemas.microsoft.com/claims/authnclassreference": "1"
          },
          "properties": {},
          "authorization": {
            "action": "Microsoft.Resources/subscriptions/resourcegroups/deployments/write",
            "role": "Subscription Admin",
            "scope": "/subscriptions/<guid>/resourcegroups/templates/deployments/basic-vm-version-0.1"
          },
          "eventChannels": "Operation",
          "eventDataId": "<guid>",
          "correlationId": "<guid>",
          "eventName": {
            "value": "EndRequest",
            "localizedValue": "End request"
          },
          "eventSource": {
            "value": "Microsoft.Resources",
            "localizedValue": "Microsoft Resources"
          },
          "level": "Error",
          "resourceGroupName": "templates",
          "resourceProviderName": {
            "value": "Microsoft.Resources",
            "localizedValue": "Microsoft Resources"
          },
          "resourceUri": "/subscriptions/<guid>/resourcegroups/templates/deployments/basic-vm-version-0.1",
          "operationId": "<guid>",
          "operationName": {
            "value": "Microsoft.Resources/subscriptions/resourcegroups/deployments/write",
            "localizedValue": "Update deployment"
          },
          "status": {
            "value": "Failed",
            "localizedValue": "Failed"
          },
          "subStatus": {},
          "eventTimestamp": "2015-04-22T05:53:40.8150293Z",
          "submissionTimestamp": "2015-04-22T05:54:00.6728843Z",10037FFE8E80BB65
          "subscriptionId": "<guid>"
        }


- **--verbose および -vv オプション**: **--verbose** オプションを使用すると、モードを verbose に設定して、`stdout` の操作手順を表示します。**--verbose** が可能にする手順を含む完全な要求の履歴を表示するには、**-vv** オプションを使用します。多くの場合、メッセージはエラーの原因に関する重要な手掛かりを提供します。

- **Azure の資格情報が設定されていないか、期限が切れています**: Azure CLI セッションで資格情報を更新するには、「`azure login`」と入力します。認証エラーのヘルプを表示する場合は、[Azure CLI が正しく構成されている](../xplat-cli-connect.md)ことを確認してください。

## Windows PowerShell でエラーを回避する

AzureResourceManager モジュールにはエラーを防止するためのコマンドレットが含まれています。


- **Get-AzureLocation**: このコマンドレットは、各種のリソースをサポートする場所を取得します。リソースの場所を入力する前に、このコマンドレットを使用してその場所がリソースの種類をサポートしていることを確認します。


- **Test-AzureResourceGroupTemplate**: テンプレートとテンプレート パラメーターを使用する前にテストします。カスタム テンプレートまたはギャラリー テンプレートと、使用するテンプレート パラメーター値を入力します。このコマンドレットは、テンプレートが内部的に一貫性があるかどうか、およびパラメーター値セットがテンプレートと一致するかどうかをテストします。

## Windows PowerShell でデプロイメントの問題を修正するための情報を取得する

- **Get-AzureResourceGroupLog**: このコマンドレットは、リソース グループの各デプロイのログのエントリを取得します。問題が生じた場合は、デプロイ ログを調べることにから開始します。

- **Verbose および Debug**: AzureResourceManager モジュールのこのコマンドレットは、実際の作業を行う REST API を呼び出します。API が返すメッセージを表示するには、$DebugPreference 変数を "Continue" に設定して、コマンドで Verbose 共通パラメーターを使用します。多くの場合、メッセージはエラーの原因に関する重要な手掛かりを提供します。

- **Azure の資格情報が設定されていないか、期限が切れています**: Windows PowerShell セッションの資格情報を更新するには、**Add-AzureAccount** コマンドレットを使用します。発行設定ファイルの資格情報は、AzureResourceManager モジュールのコマンドレットには十分ではありません。

## 認証、サブスクリプション、ロール、クォータの問題

認証と承認および Azure Active Directory に関連する、デプロイの成功を妨げる 1 つ以上の問題があることがあります。Azure リソース グループの管理方法に関係なく、アカウントへのサインインに使用する ID は、Azure Active Directory のオブジェクトか、サービス プリンシパル (仕事または学校のアカウント、あるいは組織の ID とも呼ばれるもの) のいずれかである必要があります。

しかし、Azure Active Directory では、ユーザーまたはユーザーの管理者が、どの ID がどのリソースにアクセスできるかを高い精度で制御できるようにします。デプロイが失敗する場合は、認証または承認の問題の兆候があるかどうかについて要求自体を調べ、リソース グループのデプロイメントのログを調べます。自分が一部のリソースに対するアクセス許可を持つ一方、他のリソースに対するアクセス許可のないことがわかる場合があります。Azure CLI を使用すると、`azure ad` コマンドを使用して、Azure Active Directory テナントおよびユーザーを確認することができます。(Azure CLI コマンドの完全な一覧については、「[Azure リソース マネージャーでの、Mac、Linux、および Windows 用 Azure CLI の使用](azure-cli-arm-commands.md)」をご覧ください)。

また、デプロイが既定のクォータに達すると、問題が発生する場合があります。既定のクォータは、リソース グループごと、サブスクリプションごと、アカウントごと、その他のスコープごとである可能性があります。正しくデプロイするために使用可能なリソースがあるかどうかを十分に確認します。クォータに関する完全な情報については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」をご覧ください。

サブスクリプションのコアのクォータを調べるには、Azure CLI では `azure vm list-usage` コマンド、 PowerShell では **Get-AzureVMUsage** コマンドレットを使用する必要があります。次に Azure CLI のコマンドを示します。また、無料試用版アカウントのコア クォータが 4 であることも示しています。

    azure vm list-usage
    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

上記のサブスクリプションで、米国西部リージョンに 4 つ以上のコアを作成するテンプレートをデプロイしようとした場合、 (ポータルでまたはデプロイ ログを調査して) 次のようなデプロイ エラーになります。

    statusCode:Conflict
    serviceRequestId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    statusMessage:{"error":{"code":"OperationNotAllowed","message":"Operation results in exceeding quota limits of Core. Maximum allowed: 4, Current in use: 4, Additional requested: 2."}}

このような場合はポータルに移動し、ファイルをデプロイするリージョンのクォータを増加させるように、サポートに問題を報告してください。

> [AZURE.NOTE] リソース グループの場合、クォータはサブスクリプション全体ではなく個々 のリージョンに対するものであることに注意してください。米国西部に 30 のコアをデプロイする必要がある場合は、米国西部に 30 のリソース マネージャーのコアを要求する必要があります。アクセスできるリージョンのいずれかで 30 のコアをデプロイする必要がある場合は、すべてのリージョンで 30 のリソース　マネージャー コアを要求する必要があります。
<!-- -->
コアについて具体的に把握するには、たとえば、次のコマンドを使用して適切なクォータ量を要求すべきリージョンを確認できます。このコマンドは、**jq** にパイプ出力して json 解析を行います。
<!-- -->
        azure provider show Microsoft.Compute --json | jq '.resourceTypes[] | select(.name == "virtualMachines") | { name,apiVersions, locations}'
        {
          "name": "virtualMachines",
          "apiVersions": [
            "2015-05-01-preview",
            "2014-12-01-preview"
          ],
          "locations": [
            "East US",
            "West US",
            "West Europe",
            "East Asia",
            "Southeast Asia"
          ]
        }


## Azure CLI および PowerShell のモードの問題

サービス管理 API を使用して、またはポータルを使用してデプロイされた Azure のリソースが、リソース マネージャー API または Azure ポータルを使用して表示されないという経験をお持ちかもしれません。リソースを、作成に使用したのと同じリソース マネージャー API またはポータルを使用して管理することが重要です。リソースが表示されなくなった場合は、他の管理 API またはポータルで使用できるかどうかを確認します。

## Azure リソース プロバイダーの登録に関する問題

リソースは、リソース プロバイダーによって管理され、特定のプロバイダーを使用できるようアカウントやサブスクリプションが有効になっている場合があります。プロバイダーを使用できるようになったら、使用登録もする必要があります。ほとんどのプロバイダーは、Azure ポータルまたはご使用のコマンド ライン インターフェイスによって自動的に登録されますが、登録されない場合もあります。

Azure CLI を使用してプロバイダーが登録されているかどうかを確認するには、`azure provider list` コマンドを使用します (切り捨てられた出力の例を次に示します)。

        azure provider list
        info:    Executing command provider list
        + Getting ARM registered providers
        data:    Namespace                        Registered
        data:    -------------------------------  -------------
        data:    Microsoft.Compute                Registered
        data:    Microsoft.Network                Registered  
        data:    Microsoft.Storage                Registered
        data:    microsoft.visualstudio           Registered
        data:    Microsoft.Authorization          Registered
        data:    Microsoft.Automation             NotRegistered
        data:    Microsoft.Backup                 NotRegistered
        data:    Microsoft.BizTalkServices        NotRegistered
        data:    Microsoft.Features               Registered
        data:    Microsoft.Search                 NotRegistered
        data:    Microsoft.ServiceBus             NotRegistered
        data:    Microsoft.Sql                    Registered
        info:    provider list command OK

この場合も、リージョン別の提供状況などのプロバイダーの詳細については、`azure provider list --json` と入力します。次のコードはリストの先頭にあるのみを選択して表示します。

        azure provider list --json | jq '.[0]'
        {
          "resourceTypes": [
            {
              "apiVersions": [
                "2014-02-14"
              ],
              "locations": [
                "North Central US",
                "East US",
                "West US",
                "North Europe",
                "West Europe",
                "East Asia"
              ],
              "properties": {},
              "name": "service"
            }
          ],
          "id": "/subscriptions/<guid>/providers/Microsoft.ApiManagement",
          "namespace": "Microsoft.ApiManagement",
          "registrationState": "Registered"
        }


プロバイダーが登録を必要としている場合は、`azure provider register <namespace>` コマンドを使用します。ここで、*namespace* の値は、上記の一覧から取得します。

## カスタム テンプレートでいつデプロイが成功したかを確認する

自分で作成したテンプレートを使用している場合は、すべてのプロバイダーがデプロイから正常に返されたときに、Azure リソース マネージャー システムがデプロイの成功を報告することを理解しておくことが重要です。これは、すべてのテンプレート項目が使用法に応じてデプロイされたことを意味します。

ただし、これは必ずしもリソース グループが「アクティブで、ユーザーが使用できる状態」であるとは限りません。たとえば、ほとんどのデプロイメントでは、アップグレードのダウンロード、他の非テンプレート リソースの待機、または複雑なスクリプトや、Azure が認識していない他の実行可能なアクティビティのインストールをデプロイメントに要求します。これは、アクティビティが、プロバイダーが追跡しているアクティビティではないためです。このような場合、リソースが実際に使用できる状態になるまで時間がかかります。その結果、デプロイが使用できるまでのある時にデプロイが成功の状態になることを予期する必要があります。

ただし、(たとえば [CustomScriptExtension](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/) を使用して) カスタム テンプレートにカスタム スクリプトを作成することで、Azure がデプロイの成功を報告できないようにすることができます。CustomScriptExtension は、デプロイメント全体がシステム規模で準備ができていることを監視し、ユーザーがデプロイ全体と対話できる場合のみ「成功」を返す方法を認識しています。拡張機能が最後に実行されるようにしたい場合は、テンプレートで **dependsOn** プロパティを使用します。例は[こちら](https://msdn.microsoft.com/library/azure/dn790564.aspx)で確認できます。

## テンプレートのマージ

2 つのテンプレートのマージや、親テンプレートからの子テンプレートの開始が必要になる場合があります。これは、子テンプレートをデプロイするマスター テンプレート内のデプロイメント リソースを使用して実現できます。


    {
            "name": "instance01",
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2015-01-01",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "https://mystore.blob.windows.net/azurermtemplates/my-child-template.json",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "storageAccountName": { "value": "[variables('stgAcctName1')]" },
                    "adminUsername": { "value": "[parameters('adminUsername')]" },
                    "adminPassword": { "value": "[parameters('adminPassword')]" }
                }
            }
    }


## リソース グループを超える

テンプレートがデプロイされている現在のリソース グループの外からリソースを使用する必要があることがよくあります。この動作の最も一般的なケースは、代替のリソース グループで、ストレージ アカウントや仮想ネットワークを使用する場合です。これは、仮想マシンが含まれているリソース グループを削除しても、複数のリソース グループで使用される VHD BLOB または VNet が削除されないようにするためによく行われる操作です。次の例は、外部のリソース グループのリソースを使用する方法を示しています。


    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]

    }

## 次のステップ

テンプレートの作成をマスターするには、「[Azure リソース マネージャーのテンプレートの作成](../resource-group-authoring-templates.md)」をお読みください。さらに、デプロイできる使用例については [Azure のクイック スタートテンプレート リポジトリ](https://github.com/Azure/azure-quickstart-templates)をご覧ください。**dependsOn** プロパティの例については、[アクセス可能な複数の NIC や RDP を使用した VM の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-1-vm-loadbalancer-2-nics)に関するページを参照してください。

<!--Image references-->

<!--Reference style links - using these makes the source content way more readable than using inline links-->

<!---HONumber=September15_HO1-->