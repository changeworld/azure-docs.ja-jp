---
title: Azure パートナーと顧客の使用状況の属性
description: Azure Marketplace ソリューションの顧客による使用状況を追跡する方法の概要
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/26/2018
ms.author: ellacroi
ms.openlocfilehash: 46cd5dbc044cbd0b7e38e5f0d0c8aa1916387a2d
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038477"
---
# <a name="azure-partner-customer-usage-attribution"></a>Azure パートナーの顧客の使用状況の属性

Azure のソフトウェア パートナーのソリューションは、Azure コンポーネントを必要とするか、Azure インフラストラクチャ上に直接デプロイされます。  現在、顧客がパートナー ソリューションをデプロイして独自の Azure リソースをプロビジョニングした後では、パートナーがこれらのデプロイの状態や、Azure の拡大への影響を把握することは困難です。 これらの状況がわかるようにすることは、パートナーが Microsoft の営業チームと連携し、Microsoft パートナー プログラムのクレジットを獲得するのに役立ちます。   

Microsoft では、顧客が Azure にソフトウェアをデプロイした結果を示す Azure 使用状況をより的確に追跡するための新しい方法を開発中です。 この新しい方法の基本は、Azure Resource Manager を使用して Azure サービスのデプロイを調整することです。

Microsoft パートナーは、顧客に代わって、プロビジョニングする Azure リソースに Azure の使用状況を関連付けることができます。  この関連付けは、Marketplace、クイック スタート リポジトリ、プライベート GitHub リポジトリのほか、1 対 1 の顧客エンゲージメントを通じて実行できます。  追跡を有効にするには、2 つの方法が使用可能です。

- Azure Resource Manager テンプレート: Azure Resource Manager テンプレートまたはソリューション テンプレート。パートナーのソフトウェアを実行する Azure サービスをデプロイします。 パートナーは、Azure ソリューションのインフラストラクチャと構成を定義する Azure Resource Manager テンプレートを作成できます。 Azure Resource Manager テンプレートを作成すると、パートナーとパートナーの顧客は、ソリューションをそのライフサイクル全体でデプロイでき、常にリソースが一貫した状態でデプロイされることを確信できます。 

- Azure Resource Manager API: パートナーは Azure Resource Manager API を直接呼び出して、Azure Resource Manager テンプレートをデプロイするか、API 呼び出しを生成して Azure サービスを直接プロビジョニングすることができます。 

## <a name="method-1-azure-resource-manager-templates"></a>方法 1: Azure Resource Manager のテンプレート 

現在、多くのパートナー ソリューションは、Azure Resource Manager テンプレートを使用して顧客のサブスクリプションに対してデプロイされます。  Marketplace や GitHub に、またはクイック スタートとして使用できる Azure Resource Manager テンプレートが既にある場合、この新しい追跡方法を有効にするためにテンプレートを変更するプロセスは簡単です。  現時点で Azure Resource Manager テンプレートを使用していない場合、Azure Resource Manager テンプレートについての理解を深め、作成方法を知るために役立つリンクがいくつかあります。 

*   [初めての Azure Resource Manager テンプレートを作成およびデプロイする](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
*   [Azure Marketplace 向けソリューション テンプレートの作成ガイド](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-solution-template-creation)

## <a name="instructions-add-a-guid-to-your-existing-azure-resource-manager-template"></a>手順: 既存の Azure Resource Manager テンプレートに GUID を追加する

GUID の追加は、メイン テンプレート ファイルの 1 つの変更点です。
 1. GUID を作成します。たとえば、生成された値を eb7927c8-dd66-43e1-b0cf-c346a422063 とします。
 2. Azure Resource Manager テンプレートを開きます。
 3. メイン テンプレート ファイルに新しいリソースを追加します。 リソースは、mainTemplate.json または azuredeploy.json のみにあればよく、入れ子またはリンクされたテンプレート内には必要ありません。
 4. 上記のように、"pid-" の後に GUID を入力します。

   この例のようになります。`pid-eb7927c8-dd66-43e1-b0cf-c346a422063`

 5. テンプレートのエラーをチェックします。
 6. 適切なリポジトリ内のテンプレートを再公開します。

## <a name="sample-template-code"></a>サンプル テンプレートのコード

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/tracking-sample-code-for-lu-1.PNG?token=Ak8ZDB0JzsBdUGlKEIeHNJRS7b0BWn4Gks5bbMwwwA%3D%3D)


## <a name="method-2-azure-resource-manager-apis"></a>方法 2: Azure Resource Manager API

場合によっては、パートナーは Azure サービスをデプロイするために、Azure Resource Manager REST API に対して直接呼び出しを行うことができます。 これを可能にするため、[Azure は複数の SDK をサポート](https://docs.microsoft.com/azure/#pivot=sdkstools)しています。  いずれか 1 つの SDK を使用するか、REST API を直接呼び出して、リソースをデプロイできます。

Azure Resource Manager テンプレートを使用している場合は、上記の手順を使用してソリューションにタグ付けする必要があります。  Azure Resource Manager テンプレートを使用しておらず、API 呼び出しを直接行わない場合でも、Azure リソースの使用量を関連付けるために、デプロイにタグ付けすることができます。 

**Azure Resource Manager API を使用してデプロイにタグ付けする方法:** この方法では、API 呼び出しを設計するときに、要求のユーザー エージェント ヘッダーに GUID を含めます。 各プランまたは各 SKU の GUID を追加する必要があります。  文字列は、pid というプレフィックスを使用し、その後にパートナーが生成した GUID を含めた形式にします。   

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG?token=Ak8ZDDiokRcj4PJj0aMkZmfF8BdOuOTzks5bbM35wA%3D%3D)

>[!Note] 
>ユーザー エージェントに挿入する場合の GUID 形式: pid-eb7927c8-dd66-43e1-b0cf-c346a422063     // "pid" の後に GUID を入力

文字列の形式は重要です。 プレフィックス "pid-" が含まれていないとデータを照会できません。 これは SDK ごとに異なります。  この方法を実装するには、使用する Azure SDK について、サポートとアプローチを確認する必要があります。 

**Python SDK の使用例:** Python では、"config" 属性を使用する必要があります。 UserAgent に対してのみ追加できます。 たとえば次のようになります。

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/python-for-lu.PNG?token=Ak8ZDK5Um4J6oY-7x25tuBpa168BEiYMks5bbMuUwA%3D%3D)

>これはクライアントごとに行う必要があり、グローバルな静的構成は存在しません。すべてのクライアントに行っていることを確認するために、クライアント ファクトリを作成することもできます。 
>[追加の参照情報](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)

Azure PowerShell または Azure CLI を使用してデプロイにタグ付けする方法: AzurePowerShell 経由でリソースをデプロイする場合は、次の方法を使用して GUID を追加できます。

```

[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")


```

Azure CLI を使用する場合、GUID を追加するには環境変数 AZURE_HTTP_USER_AGENT を設定します。  この変数はスクリプトのスコープ内に設定できます。または、シェルのスコープに対してグローバルに設定するには、次のようにします。

```

export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'


```

## <a name="registering-guidsoffers"></a>GUID/オファーを登録する

GUID を追跡の対象に含めるには、登録する必要があります。  

テンプレート GUID の登録はすべて、Azure Marketplace Cloud パートナー ポータル (CPP) を経由して行われます。 

1. 今すぐ [Azure Marketplace](http://aka.ms/listonazuremarketplace) に申し込み、Cloud パートナー ポータルへのアクセス権を取得してください。

 *  パートナーは、[CPP にプロファイルを所有している](https://docs.microsoft.com/azure/marketplace/become-publisher)必要があり、Marketplace または AppSource にオファーのリストを掲載することが推奨されます。 
 *  パートナーは複数の GUID を登録できます。 
 *  パートナーは、Marketplace 以外のソリューション テンプレートまたはオファーの GUID を登録することもできます。
 
2. [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインする
3. ポータルの右上隅で、アカウント アイコンをクリックし、**[Publisher profile]\(発行プロファイル\)** をクリックします。

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-image-for-lu.png)

4. [プロファイル] ページで、**[Add Tracking GUID]\(トラッキング GUID の追加\)** をクリックします。

![](https://github.com/ellacroi/azure-docs-pr/blob/last-lu-images-i-hope/articles/marketplace/media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

5. 展開されたフィールドで、**[Tracking GUID]\(トラッキング GUID\)** フィールドに GUID (GUID のみ、"pid-" のプレフィックスは省略) を入力し、**[Custom Description]\(ユーザー設定の説明\)** フィールドにオファー名または説明を入力します。

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-login.png)

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-example.png)

6. 1 つ以上の GUID を登録するには、もう一度 **[Add Tracking GUID]\(トラッキング GUID の追加\)** をクリックします。 これにより、もう 1 つのフィールドが展開されます。 

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-example-add.png)

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-example-description.png)

7. 編集が終了したら、**[保存]** をクリックして変更を保存します。 

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-save.png)



## <a name="verification-of-guid-deployment"></a>GUID デプロイの検証 

テンプレートを変更してテスト配置を実行した後、次の PowerShell スクリプトを使用して、デプロイしてタグ付けしたリソースを取得できます。 

そのスクリプトを使用すると、GUID が Azure Resource Manager テンプレートに正常に追加されたかどうかを確認できます。 これは、Azure Resource Manager API のデプロイには適用されません。

Azure にログインしたら、スクリプトを実行する前に、検証対象のデプロイが含まれるサブスクリプションを選択します。 スクリプトはデプロイのサブスクリプション コンテキスト内で実行する必要があります。

デプロイの GUID と resourceGroup 名は、必須パラメーターです。

元のスクリプトは、[こちら](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1)にあります。

```

Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName'
)

#get the correlationId of the pid deployment

$correlationId = (Get-AzureRmResourceGroupDeployment -ResourceGroupName 
$resourceGroupName -Name "pid-$guid").correlationId

#find all deployments with that correlationId

$deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

#find all deploymentOperations in a deployment by name (since PowerShell does not surface outputResources on the deployment or correlationId on the deploymentOperation)

foreach ($deployment in $deployments){

#get deploymentOperations by deploymentName and then the resourceId for any create operation

($deployment | Get-AzureRmResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}


```

## <a name="guidance-on-creating-guids"></a>GUID の作成に関するガイダンス

GUID (グローバル一意識別子) は、32 桁の 16 進数字からなる一意の参照番号です。 GUID を作成するには、GUID ジェネレーターを使用して、追跡の対象となる GUID を作成する必要があります。  使用可能な複数の[オンライン GUID ジェネレーター](https://www.bing.com/search?q=guid%20generator&qs=n&form=QBRE&sp=-1&ghc=2&pq=guid%20g&sc=8-6&sk=&cvid=0BAFAFCD70B34E4296BB97FBFA3E1B4E)があります。

オファーと配布チャネルごとに一意の GUID を作成することが推奨されます。  たとえば、ソリューションが 2 つあり、両方ともテンプレートを使用してデプロイされていて、Marketplace と GitHub の両方で使用できるとします。  4 つの GUID を作成します。

*   A を Marketplace に提供 
*   A を GitHub に提供
*   B を Marketplace に提供 
*   B を GitHub に提供

レポートは、パートナー (Microsoft パートナー ID) と GUID によって行われます。 

より詳細なレベルの SKU (SKU はオファーのバリアント) で追跡することもできます。

## <a name="guidance-on-privacy-and-data-collection"></a>プライバシーとデータ収集に関するガイダンス

パートナーは、次のことを顧客に伝えるメッセージを提供する必要があります。すなわち、Azure Resource Manager の GUID 追跡を含むデプロイの場合、Microsoft は、デプロイに関連付けられた Azure 使用状況をパートナーに報告することができます。  いくつかの例文を次に示します。 "PARTNER" となっているところには、会社名を入力する必要があります。 さらに、パートナーは、言語が自社独自のデータ プライバシー ポリシーおよびデータ収集ポリシーに矛盾しないことを確認する必要があります。この確認は、顧客を追跡の対象から除外するオプションについても行う必要があります。 

**Azure Resource Manager テンプレートでのデプロイの場合**

このテンプレートをデプロイすると、Microsoft は PARTNER ソフトウェアのインストールを、デプロイされた Azure リソースと共に識別できるようになります。  Microsoft は、ソフトウェアをサポートするために使用される Azure リソースを関連付けることができます。  Microsoft はこの情報を収集し、パートナーの製品とビジネスの運用に最適なエクスペリエンスを提供します。 このデータは、Microsoft のプライバシー ポリシー (https://www.microsoft.com/trustcenter で確認できます) に沿って収集され、管理されます。 

**SDK または API でのデプロイの場合**

PARTNER ソフトウェアをデプロイすると、Microsoft は PARTNER ソフトウェアを、デプロイされた Azure リソースと共に識別できるようになります。  Microsoft は、ソフトウェアをサポートするために使用される Azure リソースを関連付けることができます。  Microsoft はこの情報を収集し、パートナーの製品とビジネスの運用に最適なエクスペリエンスを提供します。 このデータは、Microsoft のプライバシー ポリシー (https://www.microsoft.com/trustcenter で確認できます) に沿って収集され、管理されます。

## <a name="support"></a>サポート

サポートを依頼するには、次の手順に従ってください。
 1. サポート ページ ([go.microsoft.com/fwlink/?linkid=844975](https://go.microsoft.com/fwlink/?linkid=844975)) にアクセスします。
 2. 使用状況の関連付けに関する問題の場合 - 問題の種類として **[Marketplace Onboarding]\(Marketplace オンボード\)**、カテゴリとして **[その他]** を選択し、**[Start Request]\(要求の開始\)** をクリックします。 

Azure Marketplace Cloud パートナー ポータルへのアクセスに関する問題の場合 - 問題の種類として **[Marketplace Onboarding]\(Marketplace オンボーディング\)**、カテゴリとして **[Access Problem]\(アクセスの問題\)** を選択し、**[Start Request]\(要求の開始\)** をクリックします。

 ![](https://github.com/ellacroi/azure-docs-pr/blob/last-lu-images-i-hope/articles/marketplace/media/marketplace-publishers-guide/lu-article-incident.png)
 

 3. 次のページで必須のフィールドに入力し、**[続行]** をクリックします。
 4. 次のページで自由記載フィールドに入力します。 **重要**: インシデントのタイトルに「**ISV Usage Tracking**」と入力し、後ろの広いテキスト自由記載欄に問題を詳しく記述します。  フォームの残りの欄に入力して **[送信]** をクリックします。 
 
  ![](https://github.com/qianw211/azure-docs-pr/blob/MyImgAdded-2/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-help.png)

 

## <a name="faqs"></a>FAQ

**GUID をテンプレートに追加するメリットは何ですか?**

Microsoft はパートナーに、テンプレートの顧客デプロイを把握するためのビューと、影響を受ける使用状況の分析情報を提供します。  また、Microsoft とパートナーの双方が、この情報を使用して営業チーム間のいっそう緊密な連携を推進することもできます。 また、Microsoft とパートナーはどちらも、それを使用して、Azure の拡大に対する個々のパートナーの影響に関する一貫した認識を取得できます。 

**だれがテンプレートに GUID を追加できますか?**

リソース追跡の目的は、パートナーのソリューションを顧客の Azure 使用状況に結び付けることです。  使用状況データがパートナーの Microsoft Partner Network ID (MPN ID) に関連付けられ、Cloud パートナー ポータル (CPP) でレポートがパートナーに提供されます。  

**追加し終わった GUID は変更できますか?**
 
はい。顧客または実装パートナーはテンプレートをカスタマイズし、GUID を変更したり、削除したりできます。 パートナーには、顧客に対してリソースと GUID の役割を事前に説明すること、追跡 GUID を削除または編集しないことをお勧めします。  GUID の変更は、既存ではない新規のデプロイとリソースのみに反映されます。

**レポートはいつ利用できるのでしょうか?**

レポート機能のベータ版が間もなく利用可能になります。  レポートは、Cloud パートナー ポータル (CPP) に統合されます。

**GitHub のような Microsoft 以外のリポジトリからデプロイしたテンプレートを追跡できますか?**

はい。テンプレートがデプロイされた時点で GUID が存在している場合は、使用状況が追跡されます。  
パートナーが Marketplace の外部で公開されている関連テンプレートを登録するには、Cloud パートナー ポータルにプロファイルを持っている必要があります。 

**テンプレートを Marketplace からデプロイした場合と、GitHub のようなその他のリポジトリからデプロイした場合とで、違いはありますか?**

はい。Marketplace にオファーを公開したパートナーは、Marketplace からデプロイに関するより詳細なデータを受け取ることができます。  パートナーにとって、Marketplace ポータルと Azure 管理ポータル上の顧客にオファーを公開することは、メリットがあります。 Marketplace にあるオファーからも、パートナーのリードが創出されます。

**個々の顧客エンゲージメント用にカスタム テンプレートを作成すると、どうなりますか?**

その場合も、GUID をテンプレートに追加できます。  登録されている既存の GUID を使用すると、GUID がレポートに挿入されます。  新しい GUID を作成した場合は、追跡の対象になるように、新しい GUID を登録する必要があります。

**顧客もレポートを受け取るのですか?**

現在のところ顧客は、個々のリソースの使用状況、または Azure 管理ポータル内のユーザー定義リソース グループの使用状況を追跡できます。   

**この追跡方法は Digital Partner of Record (DPOR) に似ていますか?**

デプロイと使用状況をパートナーのソリューションに結び付けるこの新しい方法は、パートナーのソリューションを Azure の使用状況にリンクするメカニズムを提供することを目標としています。  DPOR が目的とするのは、コンサルティング (システム インテグレーター) または管理 (マネージド サービス プロバイダー) パートナーを顧客の Azure サブスクリプションに関連付けることです。   
