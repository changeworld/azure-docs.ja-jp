<properties
   pageTitle="Azure へのデプロイで発生する一般的なエラーのトラブルシューティング | Microsoft Azure"
   description="Azure Resource Manager を使用した Azure へのリソースのデプロイ時に発生する一般的なエラーの解決方法について説明します。"
   services="azure-resource-manager"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"
   keywords="デプロイのエラー、Azure へのデプロイ、Azure へのデプロイ"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/14/2016"
   ms.author="tomfitz"/>

# Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング

このトピックでは、Azure へのデプロイで発生する可能がある一般的なエラーの解決方法について説明します。デプロイのエラーについて詳しい情報が必要な場合は、まず[デプロイ操作の表示](resource-manager-troubleshoot-deployments-portal.md)に関する記事をお読みください。その後、再度こちらの記事を参照し、エラーの解決に役立ててください。

## 無効なテンプレートまたはリソース

テンプレートをデプロイするときに、次のメッセージが表示される場合があります。

    Code=InvalidTemplate 
    Message=Deployment template validation failed

テンプレートとリソースのプロパティのいずれかが無効であることを示すエラーが発生した場合は、テンプレートに構文エラーが存在する可能性があります。テンプレートの式は複雑になることもあり、このエラーが発生することは珍しくありません。たとえば、ストレージ アカウントの次の名前の割り当てには、1 組の角かっこ、3 つの関数、3 組のかっこ、1 組の一重引用符、および 1 つのプロパティが含まれています。

    "name": "[concat('storage', uniqueString(resourceGroup().id))]",

一致する構文全体を指定しないと、テンプレートによって、意図したものとは全然違う値が生成されます。

この種類のエラーが発生したら、式の構文を慎重に確認してください。[Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) や [Visual Studio Code](resource-manager-vs-code.md) など、構文エラーについて指摘を受けることのできる JSON エディターの使用を検討してください。

## セグメントの長さが不適切

テンプレートが無効であるために発生するエラーは他にもあります。たとえばリソース名が正しい形式になっていないとエラーが発生します。

    Code=InvalidTemplate
    Message=Deployment template validation failed: 'The template resource {resource-name}' 
    for type {resource-type} has incorrect segment lengths.

ルート レベルのリソースは、名前に含まれるセグメントの数がリソース タイプのセグメントの数よりも 1 つ少なくなっている必要があります。各セグメントは、スラッシュで区別されます。次の例は、type のセグメント数が 2 つで、name のセグメント数が 1 つなので、**有効な名前**です。

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "myHostingPlanName",

しかし次の例は、name と type のセグメント数が同じであるため、**有効な名前ではありません**。

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "appPlan/myHostingPlanName",

子のリソースに関しては、type と name のセグメント数が一致している必要があります。一見矛盾しているように見えますが、そんなことはありません。子の完全な name と type には親の name と type が含まれるので、完全な name のセグメント数としては、やはり完全な type よりも 1 つ少なくなります。

    "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "contosokeyvault",
            ...
            "resources": [
                {
                    "type": "secrets",
                    "name": "appPassword",

複数のリソース プロバイダーに対して適用される Resource Manager タイプでは特に、セグメントを正しく指定するために注意が必要となります。たとえば、リソース ロックを Web サイトに適用するためには、type が 4 つのセグメントで構成されている必要があります。したがって name のセグメント数は 3 つです。

    {
        "type": "Microsoft.Web/sites/providers/locks",
        "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",

## リソース名が既に存在する、または既に別のリソースによって使用されている

一部のリソース (特にストレージ アカウント、データベース サーバー、Web サイト) には、Azure 全体で一意となるリソース名を指定する必要があります。一意の名前を指定しないと、次のようなエラーが発生します。

    Code=StorageAccountAlreadyTaken 
    Message=The storage account named mystorage is already taken.

一意の名前を作成するには、使用している命名規則に、[uniqueString](resource-group-template-functions.md#uniquestring) 関数の結果を連結します。

    "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]",
    "type": "Microsoft.Storage/storageAccounts",

## デプロイ時にリソースが見つからない

Resource Manager は、可能であれば複数のリソースを並列して作成することで、デプロイを最適化しています。リソースを順番にデプロイする必要がある場合は、テンプレートで **dependsOn** 要素を使用して、他のリソースへの依存関係を作成してください。たとえば、Web アプリをデプロイする場合は、App Service プランが存在する必要があります。Web アプリが App Service プランに依存していることを指定しなかった場合、Resource Manager では同時に両方のリソースが作成されます。また、App Service プランのリソースが見つからないというエラーが発生します。これは、Web アプリにプロパティを設定しようとしたときに、そのリソースがまだ存在しないためです。このエラーは、Web アプリに依存関係を設定することで回避できます。

    {
      "apiVersion": "2015-08-01",
      "type": "Microsoft.Web/sites",
      ...
      "dependsOn": [
        "[variables('hostingPlanName')]"
      ],
      ...
    }

## Could not find member 'copy' on object (オブジェクトにメンバー 'copy' が見つかりません)

このエラーは、**copy** 要素がサポートされていないテンプレート部分にこの要素を適用すると発生します。copy 要素を適用できるのは、リソース タイプだけです。リソース タイプ内のプロパティに copy を適用することはできません。たとえば、copy を仮想マシンに適用することはできますが、仮想マシンの OS ディスクに適用することはできません。場合によっては、子リソースを親リソースに変換して、copy のループを作成することができます。copy の使用について詳しくは、「[Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」をご覧ください。

## SKU not available (SKU が利用できません)

リソース (通常は仮想マシン) をデプロイするときに、次のエラー コードとエラー メッセージが表示される場合があります。

    Code: SkuNotAvailable
    Message: The requested tier for resource '<resource>' is currently not available in location '<location>' for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.

このエラーは、選択したリソースの SKU (VM サイズなど) が、選択した地域で利用できない場合に発生します。この問題を解決するには、次の 2 とおりの方法があります。

1.	ポータルにログインし、UI から新しいリソースを追加します。値を設定するときに、そのリソースで利用可能な SKU が表示されます。

    ![available skus](./media/resource-manager-common-deployment-errors/view-sku.png)

2.	UI に表示されたリージョン (またはビジネス ニーズを満たすその他のリージョン) に適切な SKU が見つからない場合は、[Azure サポート](https://portal.azure.com/#create/Microsoft.Support)にお問い合わせください。


## No registered provider found (登録済みのプロバイダーが見つかりません)

リソースをデプロイするときに、次のエラー コードとメッセージが表示される場合があります。

    Code: NoRegisteredProviderFound
    Message: No registered resource provider found for location '<location>' and API version '<api-version>' for type '<resource-type>'.

このエラーの原因として、次の 3 つの理由のいずれかが考えられます。

1. リソース タイプでサポートされた場所に該当しない
2. リソース タイプでサポートされた API バージョンに該当しない
3. サブスクリプションに対してリソース プロバイダーが登録されていない

サポートされる場所や API バージョンがエラー メッセージに提示されます。提示されたいずれかの値を使用するようにテンプレートを変更してください。ほとんどのプロバイダーは、Azure ポータルまたはご使用のコマンド ライン インターフェイスによって自動的に登録されますが、登録されない場合もあります。まだ使ったことがないリソース プロバイダーについては、手動で登録しなければならない場合があります。リソース プロバイダーについてのさらに詳しい情報は、以下に示したコマンドで検出できます。

### PowerShell

登録の状態を確認するには、**Get-AzureRmResourceProvider** を使用します。

    Get-AzureRmResourceProvider -ListAvailable

プロバイダーを登録するには、**Register-AzureRmResourceProvider** を使用し、登録するリソース プロバイダーの名前を指定します。

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

特定のタイプのリソースでサポートされている場所を取得するには、次のコマンドを使用します。

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

特定のタイプのリソースでサポートされている API バージョンを取得するには、次のコマンドを使用します。

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### Azure CLI

プロバイダーが登録されているかどうかを確認するには、`azure provider list` コマンドを使用します。

    azure provider list

リソース プロバイダーを登録するには、`azure provider register` コマンドを使用し、登録する*名前空間*を指定します。

    azure provider register Microsoft.Cdn

特定のリソース プロバイダーでサポートされている場所と API バージョンを確認するには、次のコマンドを使用します。

    azure provider show -n Microsoft.Compute --json > compute.json

## クォータを超過した

デプロイがクォータを超過すると、問題が発生する場合があります。クォータは、リソース グループごと、サブスクリプションごと、アカウントごと、その他のスコープごとに指定されている可能性があります。たとえば、リージョンのコア数を制限するようにサブスクリプションが構成されていることがあります。上限を超えたコア数の仮想マシンをデプロイしようとすると、クォータを超過したというエラーが発生します。クォータに関する完全な情報については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](azure-subscription-service-limits.md)」をご覧ください。

コアのサブスクリプションのクォータを確認するには、Azure CLI で `azure vm list-usage` コマンドを使用します。次に、無料試用版アカウントのコア クォータが 4 である例を示します。

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

デプロイ アクションは、ロールベースのアクセス制御だけでなく、サブスクリプションのポリシーによって制限されることがあります。管理者は、ポリシーを使用して、サブスクリプションにデプロイされるすべてのリソースに規約を課すことができます。たとえば、管理者は、あるリソースの種類について、特定のタグ値を必須にすることができます。そのポリシーの要件を満たしていないと、デプロイ時にエラーが発生します。ポリシーについては、「[ポリシーを使用したリソース管理とアクセス制御](resource-manager-policy.md)」をご覧ください。

## 仮想マシンのトラブルシューティング

| エラー | 記事 |
| -------- | ----------- |
| カスタム スクリプト拡張機能のエラー | [Windows VM 拡張機能のエラー](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md)<br />または<br />[Linux VM 拡張機能のエラー](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md) |
| OS イメージのプロビジョニング エラー | [新しい Windows VM の作成時のエラー](./virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md)<br />または<br />[新しい Linux VM の作成時のエラー](./virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md) |
| 割り当ての失敗 | [Windows VM の割り当てエラー](./virtual-machines/virtual-machines-windows-allocation-failure.md)<br />または<br />[Linux VM の割り当てエラー](./virtual-machines/virtual-machines-linux-allocation-failure.md) |
| 接続を試行するときの Secure Shell (SSH) エラー | [Linux VM への Secure Shell 接続](./virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md) |
| VM で実行されているアプリケーションへの接続時に発生するエラー | [Windows VM で実行中のアプリケーション](./virtual-machines/virtual-machines-windows-troubleshoot-app-connection.md)<br />または<br />[Linux VM で実行中のアプリケーション](./virtual-machines/virtual-machines-linux-troubleshoot-app-connection.md) |
| リモート デスクトップ接続のエラー | [Windows VM へのリモート デスクトップ接続](./virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md) |
| 再デプロイによって解決する接続エラー | [新しい Azure ノードへの仮想マシンの再デプロイ](./virtual-machines/virtual-machines-windows-redeploy-to-new-node.md) |
| クラウド サービスのエラー | [クラウド サービスのデプロイの問題](./cloud-services/cloud-services-troubleshoot-deployment-problems.md) |

## その他のサービスのトラブルシューティング

以下の表は、Azure のトラブルシューティング トピックを網羅したものではありません。リソースのデプロイまたは構成に関連した問題を中心に掲載しています。リソースに関して実行時に発生する問題のトラブルシューティングについてご不明な点がある場合は、対応する Azure サービスのドキュメントを参照してください。

| サービス | 記事 |
| -------- | -------- |
| Automation | [Azure Automation の共通エラーのトラブルシューティングのヒント](./automation/automation-troubleshooting-automation-errors.md) |
| Azure Stack | [Microsoft Azure Stack のトラブルシューティング](./azure-stack/azure-stack-troubleshooting.md) |
| Azure Stack | [Web Apps と Azure Stack](./azure-stack/azure-stack-webapps-troubleshoot-known-issues.md) |
| Data Factory | [Data Factory のトラブルシューティング](./data-factory/data-factory-troubleshoot.md) |
| Service Fabric | [Azure Service Fabric でサービスをデプロイするときの一般的な問題のトラブルシューティング](./service-fabric/service-fabric-diagnostics-troubleshoot-common-scenarios.md) |
| Site Recovery | [仮想マシンおよび物理サーバーの保護の監視とトラブルシューティング](./site-recovery/site-recovery-monitoring-and-troubleshooting.md) |
| Storage | [Microsoft Azure Storage の監視、診断、およびトラブルシューティング](./storage/storage-monitoring-diagnosing-troubleshooting.md) |
| StorSimple | [StorSimple デバイスのデプロイメントのトラブルシューティング](./storsimple/storsimple-troubleshoot-deployment.md) |
| SQL Database | [Azure SQL Database との接続に関する一般的な問題のトラブルシューティング](./sql-database/sql-database-troubleshoot-common-connection-issues.md) |
| SQL Data Warehouse | [Azure SQL Data Warehouse のトラブルシューティング](./sql-data-warehouse/sql-data-warehouse-troubleshoot.md) |

## デプロイの準備が完了したタイミングを把握する

すべてのプロバイダーがデプロイから正常に戻ると、Azure Resource Manager からデプロイの成功がレポートされます。ただし、レポートだけでは、リソース グループが "アクティブで、ユーザーが使用できる状態" であるとは判断できません。たとえば、アップグレードのダウンロード、非テンプレート リソースの待機、複雑なスクリプトのインストール、プロバイダーが追跡しているアクティビティではないため Azure が認識していない他の実行可能なアクティビティのインストールなどがデプロイに必要なことがあります。このような場合、リソースが実際に使用できる状態になるまで時間がかかります。その結果、デプロイメントが使用できるまでのある時にデプロイメントが成功の状態になることを予期する必要があります。

ただし、(たとえば [CustomScriptExtension](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/) を使用して) カスタム テンプレートにカスタム スクリプトを作成することで、Azure がデプロイの成功を報告できないようにすることができます。CustomScriptExtension は、デプロイメント全体がシステム規模で準備ができていることを監視し、ユーザーがデプロイ全体と対話できる場合のみ「成功」を返す方法を認識しています。拡張機能が最後に実行されるようにしたい場合は、テンプレートで **dependsOn** プロパティを使用します。

## 次のステップ

- 監査アクションについては、「[リソース マネージャーの監査操作](resource-group-audit.md)」をご覧ください。
- デプロイ時にエラーが発生した場合の対応については、[デプロイ操作の確認](resource-manager-troubleshoot-deployments-portal.md)に関するページを参照してください。

<!---HONumber=AcomDC_0720_2016-->