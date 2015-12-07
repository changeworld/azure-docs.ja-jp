<properties
   pageTitle="リソース グループのデプロイのトラブルシューティング | Microsoft Azure"
   description="リソース マネージャーのデプロイ モデルを使用して作成したリソースのデプロイ時の一般的な問題、およびこれらの問題を検出および修正する方法について説明します。"
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="10/14/2015"
   ms.author="tomfitz;rasquill"/>

# Azure でのリソース グループのデプロイのトラブルシューティング

デプロイ中に問題を発生した場合、その原因を究明する必要があります。リソース マネージャーでは、発生した問題とその原因を 2 通りの方法で発見できます。リソース グループの特定のデプロイに関する情報を取得するには、デプロイ コマンドを使用します。または、リソース グループに対して実行されたすべての操作に関する情報を取得するには、監査ログを使用します。この情報を使用すると、問題を解決して、ソリューションの稼働を再開できます。

このトピックでは、デプロイのトラブルシューティングを行うための、デプロイ コマンドの使用に重点を置いて説明します。リソースに対するすべての操作を追跡するための監査ログを使用する方法の詳細については、「[リソース マネージャーの監査操作](../resource-group-audit.md)」を参照してください。

このトピックでは、Azure PowerShell、Azure CLI および REST API を使用してのトラブルシューティング情報を取得する方法を示します。プレビュー ポータルを使用してデプロイをトラブルシューティングする方法の詳細については、「[Azure プレビュー ポータルを使用した Azure リソースの管理](../azure-portal/resource-group-portal.md)」を参照してください。

ユーザーに表示される一般的なエラーの対処方法もこのトピックで説明しています。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル。クラシック デプロイ モデルを使用してリソース グループを作成することはできません。


## PowerShell でのトラブルシューティング

[AZURE.INCLUDE [powershell-preview-inline-include](../../includes/powershell-preview-inline-include.md)]

**Get-AzureRmResourceGroupDeployment** コマンドを使用すると、デプロイ全体の状態を取得できます。以下の例では、デプロイは失敗しています。

    PS C:\> Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment

    DeploymentName    : ExampleDeployment
    ResourceGroupName : ExampleGroup
    ProvisioningState : Failed
    Timestamp         : 8/27/2015 8:03:34 PM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    siteName         String                     ExampleSite
                    hostingPlanName  String                     ExamplePlan
                    siteLocation     String                     West US
                    sku              String                     Free
                    workerSize       String                     0

    Outputs           :

通常、各デプロイは、それぞれの操作がデプロイ処理の 1 手順を示す、複数の操作で構成されています。デプロイの問題を検出するには、通常デプロイ操作に関する詳細を確認する必要があります。操作の状態は、**Get-AzureRmResourceGroupDeploymentOperation** で確認できます。

    PS C:\> Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup
    Id                        OperationId          Properties         
    -----------               ----------           -------------
    /subscriptions/xxxxx...   347A111792B648D8     @{ProvisioningState=Failed; Timestam...
    /subscriptions/xxxxx...   699776735EFC3D15     @{ProvisioningState=Succeeded; Times...

ここではデプロイの 2 つの操作が示されています。1 つのプロビジョニングの状態は Failed になっており他は Succeeded になっています。

状態のメッセージは、次のコマンドを使用して取得できます。

    PS C:\> (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties.StatusMessage

    Code       : Conflict
    Message    : Website with given name mysite already exists.
    Target     :
    Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
    Innererror :

## Azure CLI を使用したトラブルシューティング

**azure group deployment show** コマンドを使用すると、デプロイ全体の状態を取得できます。以下の例では、デプロイは失敗しています。

    azure group deployment show ExampleGroup ExampleDeployment

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


デプロイメントが失敗した理由の詳細は、監査ログで調べることができます。監査ログを表示するには、**azure group log show** コマンドを実行します。**--last-deployment** オプションを含めると、最新のデプロイのログのみを取得できます。

    azure group log show ExampleGroup --last-deployment

**azure group log show** コマンドを使用すると、多数の情報が返される可能性があります。通常、トラブルシューティングを行う場合は、失敗した操作に重点的に取り組みます。次のスクリプトは **--json** オプションと **jq** を使用して、デプロイ エラーのログを検索します。**jq** などのツールについて学習するには、「[Azure とやり取りする便利なツール](#useful-tools-to-interact-with-azure)」を参照してください。

    azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'

    {
      "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/<guid>/",
        "iat": "1442510510",
        "nbf": "1442510510",
        "exp": "1442514410",
        "ver": "1.0",
        "http://schemas.microsoft.com/identity/claims/tenantid": "<guid>",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "<guid>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
        "puid": "XXXXXXXXXXXXXXXX",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",

        "altsecid": "1:example.com:XXXXXXXXXXX",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
        "name": "Tom FitzMacken",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
        "groups": "<guid>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
        "wids": "<guid>",
        "appid": "<guid>",
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

## REST API を使用したトラブルシューティング

リソース マネージャーの REST API は、デプロイに関する情報、デプロイの操作、特定の操作の詳細に関する情報を取得するための URI を提供します。これらのコマンドの完全な説明については、次を参照してください。

- [テンプレートのデプロイに関する情報の取得](https://msdn.microsoft.com/library/azure/dn790565.aspx)
- [すべてのテンプレート デプロイ操作の一覧を取得する](https://msdn.microsoft.com/library/azure/dn790518.aspx)
- [テンプレート デプロイ操作に関する情報の取得](https://msdn.microsoft.com/library/azure/dn790519.aspx)


## 期限切れの資格情報の更新

Azure の資格情報が期限切れの場合や Azure アカウントにサインインしていない場合、デプロイは失敗します。セッションが長時間開かれている場合、資格情報の期限が切れる場合があります。資格情報は、次のオプションを使用して更新できます。

- PowerShell では、**Login-AzureRmAccount** コマンドレットを使用します (PowerShell 1.0 プレビューよりも前のバージョンでは、**Add-AzureAccount** を使用します)。発行設定ファイルの資格情報は、AzureResourceManager モジュールのコマンドレットには十分ではありません。
- Azure CLI では、**azure login** を使用します。認証エラーのヘルプを表示する場合は、[Azure CLI が正しく構成されている](../xplat-cli-connect.md)ことを確認してください。

## テンプレートおよびパラメーターの形式のチェック

テンプレートまたはパラメーター ファイルの形式が不正な場合、デプロイは失敗します。デプロイを実行する前に、テンプレートとパラメーターの有効性をテストできます。

### PowerShell

PowerShell では、**Test-AzureRmResourceGroupDeployment** を使用します (PowerShell 1.0 プレビューよりも前のバージョンでは、**Test-AzureResourceGroupTemplate** を使用します)。

    PS C:\> Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile c:\Azure\Templates\azuredeploy.json -TemplateParameterFile c:\Azure\Templates\azuredeploy.parameters.json
    VERBOSE: 12:55:32 PM - Template is valid.

### Azure CLI

Azure CLI では、**azure group template validate <resource group>** を使用します。

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

### REST API

REST API の場合は、「[テンプレートのデプロイの検証](https://msdn.microsoft.com/library/azure/dn790547.aspx)」をご覧ください。

## リソースをサポートする場所の確認

リソースの場所を指定するには、リソースをサポートする場所のいずれかを使用する必要があります。リソースの場所を入力する前に、次のいずれかのコマンドを使用して、その場所がリソースの種類をサポートしていることを確認します。

### PowerShell

PowerShell 1.0 プレビューよりも前のバージョンの場合、**Get-AzureLocation** コマンドを使用すると、すべてのリソースおよび場所が一覧表示されます。

    PS C:\> Get-AzureLocation

    Name                                    Locations                               LocationsString
    ----                                    ---------                               ---------------
    ResourceGroup                           {East Asia, South East Asia, East US... East Asia, South East Asia, East US,...
    Microsoft.ApiManagement/service         {Central US, East US, East US 2, Nor... Central US, East US, East US 2, Nort...
    Microsoft.AppService/apiapps            {East US, West US, South Central US,... East US, West US, South Central US, ...
    ...

リソースの特定の種類を指定するには、次を使用します。

    PS C:\> Get-AzureLocation | Where-Object Name -eq "Microsoft.Compute/virtualMachines" | Format-Table Name, LocationsString -Wrap

    Name                                                        LocationsString
    ----                                                        ---------------
    Microsoft.Compute/virtualMachines                           East US, East US 2, West US, Central US, South Central US,
                                                                North Europe, West Europe, East Asia, Southeast Asia,
                                                                Japan East, Japan West

PowerShell 1.0 プレビューでは、**Get-AzureRmResourceProvider** を使用してサポートされている場所を取得します。

    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web

    ProviderNamespace RegistrationState ResourceTypes               Locations
    ----------------- ----------------- -------------               ---------
    Microsoft.Web     Registered        {sites/extensions}          {Brazil South, ...
    Microsoft.Web     Registered        {sites/slots/extensions}    {Brazil South, ...
    Microsoft.Web     Registered        {sites/instances}           {Brazil South, ...
    ...

リソースの特定の種類を指定するには、次を使用します。

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia

### Azure CLI

Azure CLI の場合は、**azure location list** を使用します。場所の一覧は長くなることがあり、多数のプロバイダーがあるため、ツールを使用して、まだ使用可能でない場所を使用する前に、プロバイダーと場所を確認します。次のスクリプトでは、**jq** を使用して、Azure Virtual Machines のリソース プロバイダーが使用可能な場所を探索します。

    azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
    {
      "name": "Microsoft.Compute/virtualMachines",
      "location": "East US,East US 2,West US,Central US,South Central US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West"
    }

### REST API

REST API の場合、「[リソース プロバイダーの情報の取得](https://msdn.microsoft.com/library/azure/dn790534.aspx)」をご覧ください。

## 一意のリソース名の作成

一部のリソース (特にストレージ アカウント、データベース サーバー、Web サイト) には、Azure 全体で一意となるリソース名を指定する必要があります。現時点では、名前が一意であることをテストする方法はありません。他の組織が使用する可能性がない名前付け規則を使用することをお勧めします。

## 認証、サブスクリプション、ロール、クォータの問題

認証と承認および Azure Active Directory に関連する、デプロイの成功を妨げる 1 つ以上の問題があることがあります。Azure リソース グループを管理する方法に関係なく、アカウントにサインインするために使用する ID は Azure Active Directory オブジェクトである必要があります。この ID は作成した、あるいは割り当てられた会社または学校アカウントにすることができます。または、アプリケーション用のサービス プリンシパルを作成できます。

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

> [AZURE.NOTE]リソース グループの場合、クォータはサブスクリプション全体ではなく個々 のリージョンに対するものであることに注意してください。米国西部に 30 のコアをデプロイする必要がある場合は、米国西部に 30 のリソース マネージャーのコアを要求する必要があります。アクセスできるリージョンのいずれかで 30 のコアをデプロイする必要がある場合は、すべてのリージョンで 30 のリソース　マネージャー コアを要求する必要があります。<!-- --> コアについて具体的に把握するには、たとえば、次のコマンドを使用して適切なクォータ量を要求すべきリージョンを確認できます。このコマンドは、**jq** にパイプ出力して json 解析を行います。 <!-- --> azure provider show Microsoft.Compute --json | jq '.resourceTypes | select(.name == "virtualMachines") | { name,apiVersions, locations}' { "name": "virtualMachines", "apiVersions": [ "2015-05-01-preview", "2014-12-01-preview" ], "locations": [ "East US", "West US", "West Europe", "East Asia", "Southeast Asia" ] }


## リソース プロバイダーの登録の確認

リソースは、リソース プロバイダーによって管理され、特定のプロバイダーを使用できるようアカウントやサブスクリプションが有効になっている場合があります。プロバイダーを使用できるようになったら、使用登録もする必要があります。ほとんどのプロバイダーは、Azure プレビュー ポータルまたはご使用のコマンド ライン インターフェイスによって自動的に登録されますが、登録されない場合もあります。

### PowerShell

PowerShell 1.0 プレビューよりも前のバージョンの場合、リソース プロバイダーと登録状態の一覧を取得するには、**Get-AzureProvider** を使用します。

    PS C:\> Get-AzureProvider

    ProviderNamespace                       RegistrationState                       ResourceTypes
    -----------------                       -----------------                       -------------
    Microsoft.AppService                    Registered                              {apiapps, appIdentities, gateways, d...
    Microsoft.Batch                         Registered                              {batchAccounts}
    microsoft.cache                         Registered                              {Redis, checkNameAvailability, opera...
    ...

プロバイダーを登録するには、**Register-AzureProvider** を使用します。

Powershell 1.0 プレビューでは、**Get-AzureRmResourceProvider** を使用します。

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}

プロバイダーを登録するには、**Register-AzureRmResourceProvider** を使用します。

### Azure CLI

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

### REST API

登録の状態を取得するには、「[リソース プロバイダーの情報の取得](https://msdn.microsoft.com/library/azure/dn790534.aspx)」を参照してください。

プロバイダーを登録するには、「[リソース プロバイダーへのサブスクリプションの登録](https://msdn.microsoft.com/library/azure/dn790548.aspx)」を参照してください。


## カスタム テンプレートでいつデプロイが成功したかを確認する

自分で作成したテンプレートを使用している場合は、すべてのプロバイダーがデプロイから正常に返されたときに、Azure リソース マネージャー システムがデプロイの成功を報告することを理解しておくことが重要です。これは、すべてのテンプレート項目が使用法に応じてデプロイされたことを意味します。

ただし、これは必ずしもリソース グループが「アクティブで、ユーザーが使用できる状態」であるとは限りません。たとえば、ほとんどのデプロイメントでは、アップグレードのダウンロード、他の非テンプレート リソースの待機、または複雑なスクリプトや、Azure が認識していない他の実行可能なアクティビティのインストールをデプロイメントに要求します。これは、アクティビティが、プロバイダーが追跡しているアクティビティではないためです。このような場合、リソースが実際に使用できる状態になるまで時間がかかります。その結果、デプロイが使用できるまでのある時にデプロイが成功の状態になることを予期する必要があります。

ただし、(たとえば [CustomScriptExtension](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/) を使用して) カスタム テンプレートにカスタム スクリプトを作成することで、Azure がデプロイの成功を報告できないようにすることができます。CustomScriptExtension は、デプロイメント全体がシステム規模で準備ができていることを監視し、ユーザーがデプロイ全体と対話できる場合のみ「成功」を返す方法を認識しています。拡張機能が最後に実行されるようにしたい場合は、テンプレートで **dependsOn** プロパティを使用します。例は[こちら](https://msdn.microsoft.com/library/azure/dn790564.aspx)で確認できます。

## Azure とやり取りする便利なツール
コマンドラインから Azure リソースを使用する場合に、作業に役立つツールがあります。Azure リソース グループのテンプレートは JSON ドキュメントで、Azure リソース マネージャー API は JSON を受信して返します。このため、JSON 解析ツールは、リソースに関する情報を参照したり、テンプレートとテンプレートのパラメーター ファイルを設計し、操作する際に最初に使用するツールの 1 つになります。

### Mac、Linux、Windows の各ツール
Mac、Linux、Windows 用の Azure CLI を使用する場合、標準のダウンロード ツール (**[curl](http://curl.haxx.se/)** と **[wget](https://www.gnu.org/software/wget/)** または **[Resty](https://github.com/beders/Resty)** など)、JSON ユーティリティ (**[jq](http://stedolan.github.io/jq/download/)**、**[jsawk](https://github.com/micha/jsawk)** など)や、JSON を適切に処理する言語ライブラリを既に使い慣れていることと思います (これらのツールの多くには、[wget](http://gnuwin32.sourceforge.net/packages/wget.htm) などの Windows 用のポートもあります。実際、Linux とその他のオープン ソース ソフトウェア ツールを Windows でも実行させるいくつかの方法があります)。

このトピックには、**jq** とともに使用して、正確な情報をより効率的に取得する Azure CLI コマンドが含まれています。Azure リソースの使用状況を理解するには、使い慣れているツールセットを選択する必要があります。

### PowerShell

PowerShell には、同じ手順を実行するいくつかの基本的なコマンドがあります。

- **[Invoke-WebRequest](https://technet.microsoft.com/library/hh849901%28v=wps.640%29)** コマンドレットを使用すると、リソース グループ テンプレートやパラメーターの JSON ファイルなどのファイルをダウンロードできます。
- **[ConvertFrom-Json](https://technet.microsoft.com/library/hh849898%28v=wps.640%29.aspx)** コマンドレットを使用すると、JSON 文字列を、JSON 文字列の各フィールドにプロパティを持つカスタム オブジェクト ([PSCustomObject](https://msdn.microsoft.com/library/windows/desktop/system.management.automation.pscustomobject%28v=vs.85%29.aspx)) に変換できます。


## 次のステップ

テンプレートの作成をマスターするには、「[Azure リソース マネージャーのテンプレートの作成](../resource-group-authoring-templates.md)」をお読みください。さらに、デプロイできる使用例については [Azure のクイック スタートテンプレート リポジトリ](https://github.com/Azure/azure-quickstart-templates)をご覧ください。**dependsOn** プロパティの例については、[アクセス可能な複数の NIC や RDP を使用した VM の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-1-vm-loadbalancer-2-nics)に関するページを参照してください。

<!--Image references-->

<!--Reference style links - using these makes the source content way more readable than using inline links-->

<!---HONumber=AcomDC_1125_2015-->