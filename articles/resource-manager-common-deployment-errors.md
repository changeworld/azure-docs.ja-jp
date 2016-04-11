<properties
   pageTitle="一般的な Azure デプロイ エラー | Microsoft Azure"
   description="Azure Resource Manager でデプロイ時に発生する一般的なエラーを解決する方法について説明します。"
   services="azure-resource-manager"
   documentationCenter=""
   tags=""
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/24/2016"
   ms.author="tomfitz"/>

# Azure Resource Manager を使用してリソースを Azure にデプロイするときに発生する一般的なエラーを解決する

このトピックでは、リソースを Azure にデプロイするときに発生する可能性がある一般的なエラーの解決方法について説明します。デプロイのトラブルシューティングの詳細については、[リソース グループのデプロイのトラブルシューティング](resource-manager-troubleshoot-deployments-portal.md)に関するページを参照してください。

一部のエラーは、デプロイ前にテンプレートとパラメーターを確認することで回避できます。テンプレートの確認例については、「[Azure リソース マネージャーのテンプレートを使用したリソース グループのデプロイ](resource-group-template-deploy.md)」を参照してください。

## リソース名が既に存在する

一部のリソース (特にストレージ アカウント、データベース サーバー、Web サイト) には、Azure 全体で一意となるリソース名を指定する必要があります。一意の名前を作成するには、使用している命名規則に、[uniqueString](./resource-group-template-functions/#uniquestring) 関数の結果を連結します。
 
    "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 

## デプロイ時にリソースが見つからない

Resource Manager は、可能であれば複数のリソースを並列して作成することで、デプロイを最適化しています。リソースを連続してデプロイする必要がある場合は、テンプレートに **dependsOn** 要素を使用して、他のリソースへの依存関係を作成します。たとえば、Web アプリをデプロイする場合は、App Service プランが存在する必要があります。Web アプリが App Service プランに依存していることを指定しなかった場合、Resource Manager では同時に両方のリソースが作成されます。また、App Service プランのリソースが見つからないというエラーが発生します。これは、Web アプリにプロパティを設定しようとしたときに、そのリソースがまだ存在しないためです。このエラーは、Web アプリに依存関係を設定することで回避できます。

    {
      "apiVersion": "2015-08-01",
      "type": "Microsoft.Web/sites",
      ...
      "dependsOn": [
        "[variables('hostingPlanName')]"
      ],
      ...
    }

## リソースに場所を使用できない

リソースの場所を指定するには、リソースをサポートする場所のいずれかを使用する必要があります。リソースの場所を入力する前に、次のいずれかのコマンドを使用して、その場所がリソースの種類をサポートしていることを確認します。

### PowerShell

特定のリソース プロバイダーでサポートされる種類と場所を取得するには、**Get-AzureRmResourceProvider** を使用します。

    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web

そのリソース プロバイダーに対応するリソースの種類の一覧が取得されます。

    ProviderNamespace RegistrationState ResourceTypes               Locations
    ----------------- ----------------- -------------               ---------
    Microsoft.Web     Registered        {sites/extensions}          {Brazil South, ...
    Microsoft.Web     Registered        {sites/slots/extensions}    {Brazil South, ...
    Microsoft.Web     Registered        {sites/instances}           {Brazil South, ...
    ...

次のコマンドを使用して、指定したリソースの種類に対応する場所に集中することができます。

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

このコマンドは、次のサポートされる場所を返します。

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
    
このコマンドは、次のサポートされる場所を返します。
    
    {
      "name": "Microsoft.Compute/virtualMachines",
      "location": "East US,East US 2,West US,Central US,South Central US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West"
    }

### REST API

REST API の場合、「[リソース プロバイダーの情報の取得](https://msdn.microsoft.com/library/azure/dn790534.aspx)」をご覧ください。

## クォータを超過した

デプロイがクォータを超過すると、問題が発生する場合があります。クォータは、リソース グループごと、サブスクリプションごと、アカウントごと、その他のスコープごとに指定されている可能性があります。たとえば、リージョンのコア数を制限するようにサブスクリプションが構成されていることがあります。上限を超えたコア数の仮想マシンをデプロイしようとすると、クォータを超過したというエラーが発生します。クォータに関する完全な情報については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](./azure-subscription-service-limits.md)」をご覧ください。

コアのサブスクリプションのクォータを確認するには、Azure CLI で `azure vm list-usage` コマンドを実行します。次に、無料試用版アカウントのコア クォータが 4 である例を示します。

    azure vm list-usage
    
次のような結果が返されます。
    
    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

上記のサブスクリプションで、(ポータルでまたはデプロイメント ログを調査して) 米国西部リージョンに 4 つ以上のコアを作成するテンプレートをデプロイしようとした場合、次のようなデプロイメント エラーになります。

    statusCode:Conflict
    serviceRequestId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    statusMessage:{"error":{"code":"OperationNotAllowed","message":"Operation results in exceeding quota limits of Core. Maximum allowed: 4, Current in use: 4, Additional requested: 2."}}

また、PowerShell では、**Get-AzureRmVMUsage** コマンドレットを使用できます。

    Get-AzureRmVMUsage
    
次のような結果が返されます。

    ...
    CurrentValue : 0
    Limit        : 4
    Name         : {
                     "value": "cores",
                     "localizedValue": "Total Regional Cores"
                   }
    Unit         : null
    ...

このような場合はポータルに移動し、ファイルをデプロイするリージョンのクォータを増加させるように、サポートに問題を報告してください。

> [AZURE.NOTE] リソース グループの場合、クォータはサブスクリプション全体ではなく個々 のリージョンに対するものであることに注意してください。米国西部に 30 のコアをデプロイする必要がある場合は、米国西部に 30 のリソース マネージャーのコアを要求する必要があります。アクセスできるリージョンのいずれかで 30 のコアをデプロイする必要がある場合は、すべてのリージョンで 30 のリソース　マネージャー コアを要求する必要があります。


## 承認に失敗する

リソースをデプロイしようとしたアカウントまたはサービス プリンシパルに、そのアクションを実行するアクセス権がなかった場合、デプロイ時にエラーが発生することがあります。Azure Active Directory では、どの ID がどのリソースにアクセスできるかをユーザーまたは管理者が高い精度で制御することができます。たとえば、アカウントにリーダー ロールが割り当てられている場合、そのアカウントで新しいリソースを作成することはできません。作成しようとすると、承認が失敗したというエラー メッセージが表示されます。

ロールベースのアクセス制御の詳細については、「[Azure のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」を参照してください。

デプロイ アクションは、ロールベースのアクセス制御だけでなく、サブスクリプションのポリシーによって制限されることがあります。管理者は、ポリシーを使用して、サブスクリプションにデプロイされるすべてのリソースに規約を課すことができます。たとえば、管理者は、あるリソースの種類について、特定のタグ値を必須にすることができます。そのポリシーの要件を満たしていないと、デプロイ時にエラーが発生します。ポリシーの詳細については、「[ポリシーを使用したリソース管理とアクセス制御](./resource-manager-policy.md)」を参照してください。

## リソース プロバイダーの登録を確認する

リソースはリソース プロバイダーによって管理されているので、特定のプロバイダーを使用するようにアカウントやサブスクリプションを登録する必要があります。ほとんどのプロバイダーは、Azure ポータルまたはご使用のコマンド ライン インターフェイスによって自動的に登録されますが、登録されない場合もあります。

### PowerShell

登録の状態を確認するには、**Get-AzureRmResourceProvider** を使用します。

    Get-AzureRmResourceProvider -ListAvailable

このコマンドは、すべての使用できるリソース プロバイダーと登録の状態が返されます。

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}

プロバイダーを登録するには、**Register-AzureRmResourceProvider** を使用し、登録リソース プロバイダーの名前を指定します。

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

登録の確認が求められ、状態が返されます。

    Confirm
    Are you sure you want to register the provider 'Microsoft.Cdn'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

    ProviderNamespace RegistrationState ResourceTypes
    ----------------- ----------------- -------------
    Microsoft.Cdn     Registering       {profiles, profiles/endpoints,...

### Azure CLI

Azure CLI を使用してプロバイダーが登録されているかどうかを確認するには、`azure provider list` コマンドを使用します (切り捨てられた出力の例を次に示します)。

    azure provider list
        
このコマンドは、すべての使用できるリソース プロバイダーと登録の状態が返されます。
        
    info:    Executing command provider list
    + Getting ARM registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.Compute                Registered
    data:    Microsoft.Network                Registered  
    data:    Microsoft.Storage                Registered
    data:    microsoft.visualstudio           Registered
    ...
    info:    provider list command OK

リソース プロバイダーを登録するには、`azure provider register` コマンドを使用し、登録する *namespace* を指定します。

    azure provider register Microsoft.Cdn

### REST API

登録の状態を取得するには、「[リソース プロバイダーの情報の取得](https://msdn.microsoft.com/library/azure/dn790534.aspx)」を参照してください。

プロバイダーを登録するには、「[リソース プロバイダーへのサブスクリプションの登録](https://msdn.microsoft.com/library/azure/dn790548.aspx)」を参照してください。

## カスタム スクリプト拡張機能のエラー

カスタム スクリプト拡張機能で仮想マシンのデプロイ時にエラーが発生した場合は、[Azure Windows VM 拡張機能のエラーのトラブルシューティング](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md)、または [Azure Linux VM 拡張機能のエラーのトラブルシューティング](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md)に関するページを参照してください。

## デプロイの準備が完了したタイミングを把握する 

すべてのプロバイダーがデプロイから正常に戻ると、Azure Resource Manager からデプロイの成功がレポートされます。ただし、レポートだけでは、リソース グループが "アクティブで、ユーザーが使用できる状態" であるとは判断できません。たとえば、アップグレードのダウンロード、非テンプレート リソースの待機、複雑なスクリプトのインストール、プロバイダーが追跡しているアクティビティではないため Azure が認識していない他の実行可能なアクティビティのインストールなどがデプロイに必要なことがあります。このような場合、リソースが実際に使用できる状態になるまで時間がかかります。その結果、デプロイメントが使用できるまでのある時にデプロイメントが成功の状態になることを予期する必要があります。

ただし、(たとえば [CustomScriptExtension](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/) を使用して) カスタム テンプレートにカスタム スクリプトを作成することで、Azure がデプロイの成功を報告できないようにすることができます。CustomScriptExtension は、デプロイメント全体がシステム規模で準備ができていることを監視し、ユーザーがデプロイ全体と対話できる場合のみ「成功」を返す方法を認識しています。拡張機能が最後に実行されるようにしたい場合は、テンプレートで **dependsOn** プロパティを使用します。

## 次のステップ

- 監査アクションについては、「[リソース マネージャーの監査操作](./resource-group-audit.md)」を参照してください。
- デプロイ時に発生するエラーを判断するアクションについては、[リソース グループのデプロイのトラブルシューティング](./resource-manager-troubleshoot-deployments-portal.md)に関するページを参照してください。

<!---HONumber=AcomDC_0330_2016------>