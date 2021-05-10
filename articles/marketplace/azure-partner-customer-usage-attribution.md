---
title: Azure 顧客の使用状況の属性
description: コマーシャル マーケットプレースや、パートナーによって開発されたその他のデプロイ可能な IP での Azure アプリケーションに関する顧客の使用状況の追跡の概要について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: cpercy737
ms.author: camper
ms.date: 04/12/2021
ms.custom: devx-track-terraform
ms.openlocfilehash: 7f09e744dfdd7be73c62c70bcfa91804f14aaa68
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313676"
---
# <a name="azure-customer-usage-attribution"></a>Azure 顧客の使用状況の属性

顧客の使用状況の属性は、パートナーの IP をデプロイしているときに作成された顧客のサブスクリプションでの Azure リソースの使用状況をパートナーに関連付けます。 これらの関連付けを Microsoft システム内に形成することにより、パートナーのソフトウェアを実行している Azure の占有領域の可視性が向上します。 [コマーシャル マーケットプレースの Azure アプリケーション プラン](#commercial-marketplace-azure-apps)の場合、この追跡機能は、Microsoft セールス チームと連携し、Microsoft パートナー プログラムでの信頼を得るために役立ちます。 顧客の利用状況属性は [コマーシャル マーケットプレースの Azure 仮想マシン オファー](./azure-vm-create.md)には適用されません。 エンドカスタマー サブスクリプションで Azure の使用量が追跡されるようにするために、マーケットプレースのパブリッシャーが仮想マシン オファーに対して行う必要があることは何もありません。

顧客の使用状況の属性では、以下の 3 つのデプロイ オプションがサポートされます。

1. Azure Resource Manager テンプレート (Azure アプリの共通の基盤、コマーシャル マーケットプレースでは "ソリューション テンプレート" または "マネージド アプリ" とも呼ばれます): パートナーは、インフラストラクチャとその Azure ソリューションの構成を定義するために Resource Manager テンプレートを作成します。 Resource Manager テンプレートを使用すると、顧客は、パートナーのソリューションのリソースを一貫性のある反復可能な状態でデプロイできます。
1. Azure Resource Manager API: パートナーは Resource Manager API を呼び出して、Resource Manager テンプレートをデプロイするか、または Azure サービスを直接プロビジョニングできます。
1. Terraform: パートナーは Terraform を使用して、Resource Manager テンプレートをデプロイするか、または Azure サービスを直接デプロイできます。

顧客の使用状況の属性に関する二次的なユース ケースがコマーシャル マーケットプレースの外部にあり、[この記事の後の方で](#other-use-cases)で説明されています。

>[!IMPORTANT]
>- 顧客の使用状況の属性は、システム インテグレーター、マネージド サービス プロバイダー、または主に Azure リソースをデプロイおよび管理するように設計されたツールの作業の追跡を目的にしているわけではありません。
>- 顧客の使用状況の属性は、新しいデプロイのためのものであり、既にデプロイされているリソースの追跡をサポートしていません。
>- すべての Azure サービスが、顧客の使用状況の属性との間に互換性があるわけではありません。 Azure Kubernetes Services (AKS)、VM Scale Sets、Microsoft Azure Batch には、使用状況を実際よりも低く報告する既知の問題があります。

## <a name="commercial-marketplace-azure-apps"></a>コマーシャル マーケットプレースの Azure アプリ

コマーシャル マーケットプレースに公開されている Azure アプリの Azure の使用状況の追跡は、ほぼ自動的に行われます。 [マーケットプレースの Azure アプリのプランの技術的な構成](./create-new-azure-apps-offer-solution.md#define-the-technical-configuration)の一部として Resource Manager テンプレートをアップロードすると、パートナー センターでは、Azure Resource Manager で読み取り可能な追跡 ID を追加します。

Azure Resource Manager API を使用する場合は、コードでリソースをデプロイするときに Azure Resource Manager に渡すために、[以下の手順](#use-resource-manager-apis)に従って追跡 ID を追加する必要があります。 この ID は、パートナー センターのそのプランの [技術的な構成] ページに表示されます。 

> [!NOTE]
> 既存の Azure アプリの場合は、各プランの技術的な構成にある追跡 ID を更新するために、2021 年 3 月に 1 回限りの移行が開始されました。 これらのプランの過去のデプロイの使用状況は、Microsoft システムで引き続き追跡されます。
>
>プランを更新すると、メイン テンプレート ファイルに、**Microsoft.Resources またはデプロイ** リソースの種類を追加する必要がなくなります。

## <a name="other-use-cases"></a>その他のユース ケース 

顧客の使用状況の属性を使用すると、コマーシャル マーケットプレースでは入手できないソリューションの Azure の使用状況を追跡できます。 これらのソリューションは通常、クイック スタート リポジトリやプライベート GitHub リポジトリに存在するか、または持続的な IP を作成する 1:1 の顧客エンゲージメントから生まれます (デプロイ可能で、かつスケーラブルなアプリなど)。

次のいくつかの手動の手順が必要になります。

1. 追跡 ID として使用する 1 つ以上の GUID を作成します。
1. これらの GUID をパートナー センターで登録します。
1. 登録された GUID を Azure アプリまたはユーザー エージェント文字列、あるいはその両方に追加します。

### <a name="create-guids"></a>GUID の作成

コマーシャル マーケットプレースの Azure アプリのためにパートナー センターによって自動的に作成される追跡 ID とは異なり、CUA のその他の使用では、追跡 ID として使用する GUID を作成する必要があります。 GUID は、32 桁の 16 進数を含む一意の参照識別子です。 追跡のための GUID を作成するには、GUID ジェネレーターを使用する必要があります。たとえば、PowerShell を使用して、次のように入力します。

```powershell
[guid]::NewGuid()
```

製品と配布チャネルごとに一意の GUID を作成する必要があります。 レポート作成が分割されないようにしたい場合は、製品の複数の配布チャネルに対して 1 つの GUID を使用できます。 レポート作成は、Microsoft Partner Network ID と GUID によって実行されます。

### <a name="register-guids"></a>GUID を登録する

次に、GUID をパートナー センターで登録して、パートナーに関連付けることができるようにする必要があります。

1. [パートナー センター](https://partner.microsoft.com/dashboard)にサインインします。

1. [商業マーケットプレース パブリッシャー](https://aka.ms/JoinMarketplace)としてサインアップします。

1. 右上隅の **[設定]** (歯車アイコン) を選択してから、 **[アカウント設定]** を選択します。

1. **[組織プロファイル]**  >  **[識別子]**  >  **[追跡 GUID の追加]** の順に選択します。

1. **[GUID]** ボックスに、追跡用 GUID を入力します。 `pid-` プレフィックスは付けずに GUID だけを入力してください。 **[説明]** ボックスに、ソリューションの名前または説明を入力します。

1. 複数の GUID を登録するには、もう一度 **[Add Tracking GUID]\(トラッキング GUID の追加\)** を選択します。 追加のボックスがページに表示されます。

1. **[保存]** を選択します。

### <a name="add-a-guid-to-a-resource-manager-template"></a>GUID を Resource Manager テンプレートに追加する

登録された GUID を Resource Manager テンプレートに追加するには、メイン テンプレート ファイルに 1 つの変更を加えます。

1. Resource Manager テンプレートを開きます。

1. メイン テンプレート ファイルで [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments) 型の新しいリソースを追加します。 このリソースは、**mainTemplate.json** または **azuredeploy.json** ファイルにしか存在する必要がなく、入れ子になったテンプレートやリンク済みテンプレートには必要ありません。

1. `pid-` プレフィックスの後にリソースの名前として GUID 値を入力します。 たとえば、GUID が eb7927c8-dd66-43e1-b0cf-c346a422063 の場合、リソース名は **pid-eb7927c8-dd66-43e1-b0cf-c346a422063** になります。 例:
 
```json
{ // add this resource to the resources section in the mainTemplate.json
    "apiVersion": "2020-06-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```
4. テンプレートにエラーないかどうか確認します。

1. 適切なリポジトリにテンプレートを再発行します。

1. [テンプレートのデプロイで GUID の成功を確認します](#verify-deployments-tracked-with-a-guid)。

> [!TIP]
> Resource Manager テンプレートの作成と公開の詳細については、[初めての Resource Manager テンプレートの作成とデプロイ](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)に関するページを参照してください。

### <a name="verify-deployments-tracked-with-a-guid"></a>GUID で追跡されるデプロイを検証する

テンプレートを変更し、テスト デプロイを実行したら、次の PowerShell スクリプトを使用して、デプロイしてタグを付けたリソースを取得します。

このスクリプトを使用すると、GUID が Resource Manager テンプレートに正常に追加されたことを確認できます。 このスクリプトは、Resource Manager API または Terraform のデプロイには適用されません。

Azure にサインインします。 スクリプトを実行する前に、検証するデプロイを含むサブスクリプションを選択します。 スクリプトはデプロイのサブスクリプション コンテキスト内で実行する必要があります。

デプロイの **GUID** (下では "deploymentName") と **resourceGroupName** の名前は必須のパラメーターです。

[元のスクリプト](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1)は GitHub から入手できます。

```powershell
Param(
    [string][Parameter(Mandatory=$true)]$deploymentName, # the full name of the deployment, e.g. pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the named deployment
$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name "$deploymentName").correlationId

# Find all deployments with that correlationId
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in all deployments with that correlationId as PowerShell doesn't surface outputResources on the deployment or correlationId on the deploymentOperation

foreach ($deployment in $deployments){
    # Get deploymentOperations by deploymentName
    # then the resourceIds for each resource
    ($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.targetResource -notlike "*Microsoft.Resources/deployments*"}).TargetResource
}
```

### <a name="notify-your-customers"></a>顧客に通知する

パートナーはその顧客に対し、顧客の使用状況の属性を使ったデプロイについて伝える必要があります。 Microsoft は、これらのデプロイに関連付けられた Azure の使用状況をパートナーに報告します。 以降の例では、そうしたデプロイについて顧客に通知する際にご利用いただける内容を取り上げています。 それぞれの例にある \<PARTNER> は貴社の名前に置き換えてください。 パートナーは、この通知が自社のデータ プライバシーおよび収集ポリシー (顧客が追跡から除外されるオプションを含む) に確実に整合するようにする必要があります。

#### <a name="notification-for-resource-manager-template-deployments"></a>Resource Manager テンプレートでデプロイする場合の通知

このテンプレートをデプロイすると、Microsoft は \<PARTNER> ソフトウェアのインストールを、デプロイされた Azure リソースと共に識別できます。 Microsoft は、このソフトウェアをサポートするために使用されるこれらのリソースを関連付けることができます。 Microsoft はこの情報を収集し、パートナーの製品とビジネスの運用に最適なエクスペリエンスを提供します。 これらのデータは、Microsoft のプライバシー ポリシー ([https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) にあります) によって収集および管理されます。

#### <a name="notification-for-sdk-or-api-deployments"></a>SDK または API でデプロイする場合の通知

\<PARTNER> ソフトウェアをデプロイすると、Microsoft は \<PARTNER> ソフトウェアのインストールを、デプロイされた Azure リソースと共に識別できます。 Microsoft は、このソフトウェアをサポートするために使用されるこれらのリソースを関連付けることができます。 Microsoft はこの情報を収集し、パートナーの製品とビジネスの運用に最適なエクスペリエンスを提供します。 これらのデータは、Microsoft のプライバシー ポリシー ([https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) にあります) によって収集および管理されます。

## <a name="use-resource-manager-apis"></a>Resource Manager API を使用する

場合によっては、Azure サービスをデプロイするために Resource Manager REST API を直接呼び出すことができます。 こうした呼び出しに対応するため、[Azure は複数の SDK をサポート](../index.yml?pivot=sdkstools)しています。 いずれかの SDK を使用するか、または REST API を直接呼び出してリソースをデプロイできます。

顧客の使用状況の属性を有効にするには、API 呼び出しを設計するときに、要求内のユーザー エージェント ヘッダーに追跡 ID を含めます。 `pid-` プレフィックスを使用して文字列を書式設定します。 例 :

```xml
//Commercial Marketplace Azure app
pid-contoso-myoffer-partnercenter //copy the tracking ID exactly as it appears in Partner Center

//Other use cases
pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4 //enter your GUID after "pid-"
```
> [!IMPORTANT]
> コマーシャル マーケットプレースの Azure アプリで Resource Manager API を使用している場合は、パートナー センターで提供された追跡 ID を使用します。 GUID を使用しないでください。

各種の SDK ではさまざまな方法で Resource Manager API を操作するため、コードにいくつかの違いが必要になります。 以下の例は、GUID を使用したコマーシャル マーケットプレース以外のアプローチを示し、より一般的なさまざまな Azure SDK について説明しています。

#### <a name="example-python-sdk"></a>例: Python SDK

Python では、**config** 属性を使用します。 この属性は UserAgent に対してのみ追加できます。 例:

```python
client = azure.mgmt.servicebus.ServiceBusManagementClient(**parameters)
client.config.add_user_agent("pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4")
```
> [!IMPORTANT]
> 属性はクライアントごとに追加します。 グローバルな静的構成はありません。 すべてのクライアントで確実に追跡が行われるように、クライアント ファクトリにタグを付けることもできます。 詳細については、[GitHub のクライアント ファクトリ サンプル](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)を参照してください。

#### <a name="example-net-sdk"></a>例: .NET SDK

.NET の場合、必ずユーザー エージェントを設定します。 [Microsoft.Azure.Management.Fluent](/dotnet/api/microsoft.azure.management.fluent) ライブラリを使用して、次のコード (C# での例) でユーザー エージェントを設定します。

```csharp
var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="example-azure-powershell"></a>例: Azure PowerShell

Azure PowerShell 経由でリソースをデプロイする場合は、次のメソッドを使用して GUID を追加します。

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="example-azure-cli"></a>例: Azure CLI

Azure CLI を使用して GUID を追加する場合は、スクリプトのスコープ内で **AZURE_HTTP_USER_AGENT** 環境変数を設定します。 シェル スコープを対象として、この変数をグローバルに設定することもできます。

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

詳細については、[Azure SDK for Go](/azure/developer/go/) に関するページを参照してください。

## <a name="use-terraform"></a>Terraform を使用する

Terraform に対するサポートは、Azure プロバイダーの 1.21.0 リリースを通して使用できます ([https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019))。 これは、Terraform 経由でソリューションをデプロイするすべてのパートナーと、Azure プロバイダー (バージョン 1.21.0 以降) によってデプロイおよび測定されるすべてのリソースに適用されます。

Terraform 用の Azure プロバイダーでは、ソリューションに使用される追跡 GUID を指定するための [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) という名前の新しい省略可能なフィールドが追加されました。 このフィールドの値は、*ARM_PARTNER_ID* 環境変数から提供することもできます。

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
*partner_id* の値を登録された GUID に設定します。 GUID の前に "pid-" を付加しないでください。単純に、実際の GUID に設定します。

> [!IMPORTANT]
> コマーシャル マーケットプレースの Azure アプリで Terraform を使用している場合は、パートナー センターで提供された追跡 ID 全体を使用します。 GUID を使用しないでください。

## <a name="get-support"></a>サポートを受ける

コマーシャル マーケットプレースのサポート オプションについては、「[パートナー センターでのコマーシャル マーケットプレース プログラムのサポート](support.md)」を参照してください。

### <a name="how-to-submit-a-technical-consultation-request"></a>技術コンサルティングの要求を送信する方法

1. [パートナー テクニカル サービス](https://aka.ms/TechnicalJourney)を参照してください。
1. **[クラウド インフラストラクチャおよび管理]** を選択して、技術的な説明を表示します。
1. **[デプロイ サービス]**  >  **[要求の送信]** の順に選択します。
1. MSA (MPN アカウント) または AAD (パートナー ダッシュボード アカウント) を使用してサインインします。
1. 開いたフォームで、連絡先情報を入力または確認します。 コンサルティングの詳細が事前に入力されているか、またはドロップダウン オプションが表示される可能性があります。
1. 問題のタイトルと詳細な説明を入力します。
1. **[Submit]\(送信\)** をクリックします。

スクリーンショット付きの詳細な手順については、[テクニカル プリセールスおよびデプロイ サービスを使用する](https://aka.ms/TechConsultInstructions)方法に関する記事を参照してください。

お客様のニーズを調べる電話の準備のために、Microsoft パートナー技術コンサルタントから連絡があります。

## <a name="report"></a>レポート
現在 ISV パートナーは、顧客の利用状況属性によって記録された Azure 使用状況のレポートを利用することはできません。 顧客の利用状況属性を対象としたレポートがパートナー センターのコマーシャル マーケットプレース プログラムに追加されるのは、2021 年下半期の予定です。

## <a name="faq"></a>よく寄せられる質問

#### <a name="after-a-tracking-id-is-added-can-it-be-changed"></a>追跡 ID が追加された後、それを変更することはできますか?

コマーシャル マーケットプレースの Azure アプリの追跡 ID は、パートナー センターによって自動的に管理されます。 ただし、顧客はテンプレートをダウンロードして、追跡 ID を変更または削除できます。 パートナーは、これらの削除や編集を防止するために、追跡 ID の役割を顧客に事前に説明しておく必要があります。 追跡 ID の変更は、新しいデプロイとリソースにのみ影響を与え、既存のデプロイやリソースは影響を受けません。

#### <a name="can-i-track-templates-deployed-from-a-non-microsoft-repository-like-github"></a>GitHub のような Microsoft 以外のリポジトリからデプロイしたテンプレートを追跡できますか?

はい。テンプレートがデプロイされたときに追跡 ID が存在している限り、使用状況が追跡されます。 公開元であるパートナーと、Microsoft 以外のリポジトリからデプロイされたテンプレートの間の関連付けを維持するには、まず、Azure portal でそのプランのコマーシャル マーケットプレースの一覧から (追跡 ID が含まれている) 公開されたテンプレートのコピーをダウンロードします。 そのバージョンを GitHub または Microsoft 以外の別のリポジトリに公開します。

テンプレートがコマーシャル マーケットプレースの一覧には表示されないが、登録された GUID を含んでいる場合は、GitHub または Microsoft 以外の別のリポジトリに公開するバージョンに GUID が存在することを確認してください。

#### <a name="does-the-customer-receive-reporting-as-well"></a>顧客もレポートを受け取るのですか?

いいえ。 顧客は、Azure portal 内のすべてのリソースまたはリソース グループの自分の使用状況を追跡できます。 CUA の追跡 ID で分類された使用状況は顧客に表示されません。

#### <a name="is-customer-usage-attribution-similar-to-the-digital-partner-of-record-dpor-or-partner-admin-link-pal"></a>顧客の使用状況の属性は、Digital Partner of Record (DPOR) またはパートナー管理リンク (PAL) に似ていますか?

顧客の使用状況の属性は、Azure の使用状況をパートナーの反復可能で、デプロイ可能な IP に関連付ける (デプロイ時に関連付けを形成する) ためのメカニズムです。 DPOR と PAL は、コンサルティング (システム インテグレーター) または管理 (マネージド サービス プロバイダー) パートナーを、そのパートナーが顧客と連携している間のその顧客の該当する Azure の占有領域に関連付けることを目的にしています。