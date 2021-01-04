---
title: プライベート エンドポイントでの Speech Services の使用
titleSuffix: Azure Cognitive Services
description: Azure Private Link で提供されるプライベート エンドポイントでの Speech Services の使用方法
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: alexeyo
ms.openlocfilehash: 01a0171ed2b660fbabebf4276a74f8a3ea631bde
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516532"
---
# <a name="using-speech-services-with-private-endpoints-provided-by-azure-private-link"></a>Azure Private Link で提供されるプライベート エンドポイントでの Speech Services の使用

[Azure Private Link](../../private-link/private-link-overview.md) を使用すると、[プライベート エンドポイント](../../private-link/private-endpoint-overview.md)を介して Azure 内のさまざまな PaaS サービスに接続できます。 プライベート エンドポイントは、特定の[仮想ネットワーク](../../virtual-network/virtual-networks-overview.md)およびサブネット内のプライベート IP アドレスです。

この記事では、Azure Cognitive Speech Services で Private Link とプライベート エンドポイントを設定して使用する方法について説明します。 

> [!NOTE]
> この記事では、Azure Cognitive Speech Services での Private Link の設定および使用の詳細について説明します。 先に進む前に、[Cognitive Services での仮想ネットワークの使用](../cognitive-services-virtual-networks.md)に関する一般的な記事をご覧ください。

プライベート エンドポイントのシナリオで Speech リソースを有効にするには、次のタスクを実行する必要があります。
- [Speech リソースのカスタム ドメイン名を作成する](#create-custom-domain-name)
- [プライベート エンドポイントを作成し構成する](#enabling-private-endpoints)
- [既存のアプリケーションとソリューションを調整する](#using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled)

後からすべてのプライベート エンドポイントを削除するが、リソースを使用し続けることにした場合、必要な操作は[このセクション](#using-speech-resource-with-custom-domain-name-without-private-endpoints)で説明しています。

## <a name="create-custom-domain-name"></a>カスタム ドメイン名を作成する

プライベート エンドポイントでは、[Cognitive Services カスタム サブドメイン名](../cognitive-services-custom-subdomains.md)を使用する必要があります。 次の指示を使用して、Speech リソース用に 1 つを作成します。

> [!WARNING]
> カスタム ドメイン名が有効になっている Speech リソースでは、Speech Services とのやり取りに別の方法を使用します。 多くの場合、[プライベート エンドポイントが有効になっている](#using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled)シナリオと、[プライベート エンドポイントが有効になって **いない**](#using-speech-resource-with-custom-domain-name-without-private-endpoints)シナリオの両方に対して、アプリケーション コードを調整する必要があります。
>
> カスタム ドメイン名を有効にする操作は、[**元に戻すことはできません**](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name)。 [リージョン名](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)に戻る唯一の方法は、新しい Speech リソースを作成することです。 
>
> 特に、[Speech Studio](https://speech.microsoft.com/) を使用して作成された多数のカスタム モデルおよびプロジェクトが Speech リソースに関連付けられている場合は、運用環境で使用されているものを変更する前に、必ずテスト リソースで構成を試行することを **強く** お勧めします。

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

- [Azure portal](https://portal.azure.com/) に移動して、ご利用の Azure アカウントにサインインします
- 必要な Speech リソースを選択します
- *[ネットワーク]* (*リソース管理* グループ) を選択します 
- *[ファイアウォールと仮想ネットワーク]* タブ (既定) で、 **[カスタム ドメイン名を生成します]** ボタンをクリックします
- 新しいパネルが開き、リソースの一意のカスタム サブドメインを作成する手順が示されます
> [!WARNING]
> カスタム ドメインの名前は、作成後に変更することは **できません**。 詳細については、上記の警告を参照してください。
- 操作が完了したら、 *[キーとエンドポイント]* (*リソース管理* グループ) を選択し、次の形式でリソースの新しいエンドポイント名を確認することができます <p />`{your custom name}.cognitiveservices.azure.com`

# <a name="powershell"></a>[PowerShell](#tab/powershell)

このセクションでは、Azure PowerShell モジュール バージョン 5.1.0 以降を使用して、ローカルで PowerShell バージョン 7.x 以降を実行する必要があります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。

先に進む前に、`Connect-AzAccount` を実行して、Azure との接続を作成します。

## <a name="verify-custom-domain-name-availability"></a>カスタム ドメイン名の可用性を確認する

使用しようと考えているカスタム ドメインが空いているかどうかを確認する必要があります。 Cognitive Services REST API の[ドメインの可用性の確認方法](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability)を使用します。 下のコード ブロックに記された、手順を説明したコメントを参照してください。

> [!TIP]
> コードは Azure Cloud Shell では機能 **しません**。

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription containing Speech resource
# If your Azure account has only one active subscription
# you can skip this step
Set-AzContext -SubscriptionId $subId

# Preparing OAuth token which is used in request
# to Cognitive Services REST API
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Preparing and executing the request to Cognitive Services REST API
$uri = "https://management.azure.com/subscriptions/" + $subId + `
    "/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18"
$body = @{
subdomainName = $subdomainName
type = "Microsoft.CognitiveServices/accounts"
}
$jsonBody = $body | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri $uri -ContentType "application/json" -Authentication Bearer `
    -Token $token -Body $jsonBody | Format-List
```
目的の名前が利用可能な場合は、次のような応答が返されます。
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
その名前が既に取得されている場合は、次の応答が返されます。
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="enabling-custom-domain-name"></a>カスタム ドメイン名の有効化

選択した Speech リソースに対してカスタム ドメイン名を有効にするには、[Set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) コマンドレットを使用します。 下のコード ブロックに記された、手順を説明したコメントを参照してください。

> [!WARNING]
> 以下のコードを正常に実行した後で、Speech リソースのカスタム ドメイン名を作成します。 この名前は変更 **できません**。 詳細については、上記の警告を参照してください。

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription containing Speech resource
# If your Azure account has only one active subscription
# you can skip this step
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource
# WARNING! THIS IS NOT REVERSIBLE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- このセクションでは、Azure CLI の最新バージョンが必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="verify-custom-domain-name-availability"></a>カスタム ドメイン名の可用性を確認する

使用しようと考えているカスタム ドメインが空いているかどうかを確認する必要があります。 Cognitive Services REST API の[ドメインの可用性の確認方法](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability)を使用します。 

次のコード ブロックをコピーし、カスタム ドメイン名を挿入して、`subdomain.json` ファイルに保存します。

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

現在のフォルダーにファイルをコピーするか、Azure Cloud Shell にアップロードして、次のコマンドを実行します。 (`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` は、実際の Azure サブスクリプション ID に置き換えます)。

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
目的の名前が利用可能な場合は、次のような応答が返されます。
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

その名前が既に取得されている場合は、次の応答が返されます。
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enabling-custom-domain-name"></a>カスタム ドメイン名の有効化

選択した Speech リソースに対してカスタム ドメイン名を有効にするには、[az cognitiveservices account update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) コマンドを使用します。

Speech リソースを含んだ Azure サブスクリプションを選択します。 Azure アカウントにアクティブなサブスクリプションが 1 つしかない場合は、この手順を省略できます。 (`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` は、実際の Azure サブスクリプション ID に置き換えます)。
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
カスタム ドメイン名を、選択したリソースに設定します。 サンプル パラメーターの値を実際の値に置き換えて、次のコマンドを実行します。
> [!WARNING]
> 以下のコマンドを正常に実行した後で、Speech リソースのカスタム ドメイン名を作成します。 この名前は変更 **できません**。 詳細については、上記の警告を参照してください。
```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

**_

## <a name="enabling-private-endpoints"></a>プライベート エンドポイントの有効化

Azure portal、Azure PowerShell、または Azure CLI を使用してプライベート エンドポイントを有効にします。

プロビジョニング プロセス中に既定で作成するプライベート エンドポイントに必要な更新を行って、Virtual Network にアタッチされている[プライベート DNS ゾーン](../../dns/private-dns-overview.md)を使用することをお勧めします。 ただし、独自の DNS サーバーを使用している場合は、DNS 構成に追加の変更が必要になることがあります。 「[プライベート エンドポイントの DNS](#dns-for-private-endpoints)」のセクションを参照してください。 最適な方法は、運用環境の Speech リソースに対してプライベート エンドポイントをプロビジョニングする *前に*、DNS 戦略を決定することです。 また、特に独自の DNS サーバーを使用している場合は、事前テストをお勧めします。

プライベート エンドポイントを作成するには、以下の記事を使用します。 これらの記事では、プライベート エンドポイントで有効にするサンプル リソースとして Web アプリを使用しています。 次のパラメーターを代わりに使用します。

| 設定             | 値                                    |
|---------------------|------------------------------------------|
| リソースの種類       | **Microsoft.CognitiveServices/accounts** |
| リソース            | **\<your-speech-resource-name>**         |
| ターゲット サブリソース | **アカウント**                              |

- [Azure portal を使用してプライベート エンドポイントを作成する](../../private-link/create-private-endpoint-portal.md)
- [Azure PowerShell を使用してプライベート エンドポイントを作成する](../../private-link/create-private-endpoint-powershell.md)
- [Azure CLI を使用してプライベート エンドポイントを作成する](../../private-link/create-private-endpoint-cli.md)

### <a name="dns-for-private-endpoints"></a>プライベート エンドポイントの DNS

[Cognitive Services リソースでのプライベート エンドポイントの DNS](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints) の一般原則を理解します。 次に、DNS 構成が正しく機能していることを確認します (次のサブセクションを参照してください)。

#### <a name="mandatory-check-dns-resolution-from-the-virtual-network"></a>(必須ルール)。 仮想ネットワークからの DNS 解決

このセクションでは、Speech リソースの DNS 名のサンプルとして `my-private-link-speech.cognitiveservices.azure.com` を使用します。

プライベート エンドポイントをアタッチした仮想ネットワークにある仮想マシンにログオンします。 Windows コマンド プロンプトまたは Bash シェルを開き、'nslookup' コマンドを実行し、リソースのカスタム ドメイン名が正しく解決されていることを確認します。
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  168.63.129.16

Non-authoritative answer:
Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
Address:  172.28.0.10
Aliases:  my-private-link-speech.cognitiveservices.azure.com
```
解決された IP アドレスがプライベート エンドポイントのアドレスに対応していることを確認します。

#### <a name="optional-check-dns-resolution-from-other-networks"></a>(省略可能なチェック)。 他のネットワークからの DNS 解決

このチェックが必要になるのは、"ハイブリッド" モードでプライベート エンドポイントが有効になっている Speech リソースを使用する予定の場合、つまり、リソースの *[ネットワーク]* セクションで、 *[すべてのネットワーク]* または *[選択したネットワークとプライベート エンドポイント]* アクセス オプションを有効にしている場合です。 プライベート エンドポイントのみを使用してリソースにアクセスする予定の場合は、このセクションを省略できます。

このセクションでは、Speech リソースの DNS 名のサンプルとして `my-private-link-speech.cognitiveservices.azure.com` を使用します。

リソースへのアクセスを許可しているネットワークにアタッチされているすべてのマシンで、Windows コマンド プロンプトまたは Bash シェルを開き、'nslookup' コマンドを実行し、リソースのカスタム ドメイン名が正しく解決されていることを確認します。
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    vnetproxyv1-weu-prod.westeurope.cloudapp.azure.com
Address:  13.69.67.71
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          my-private-link-speech.privatelink.cognitiveservices.azure.com
          westeurope.prod.vnet.cog.trafficmanager.net
```

解決された IP アドレスは、VNet プロキシ エンドポイントを指しており、これは、プライベート エンドポイントが有効になっている Cognitive Services リソースにネットワーク トラフィックをディスパッチするために使用されます。 この動作は、カスタム ドメイン名が有効になっているが、プライベート エンドポイントは構成 *されていない* リソースでは異なります。 [このセクション](#dns-configuration)をご覧ください。

## <a name="adjusting-existing-applications-and-solutions"></a>既存のアプリケーションとソリューションの調整 

カスタム ドメインが有効になっている Speech リソースでは、Speech Services とのやり取りに別の方法を使用します。 これは、カスタム ドメインが有効になっている Speech リソースに、プライベート エンドポイントが[有効になっている](#using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled)場合と[有効になっていない](#using-speech-resource-with-custom-domain-name-without-private-endpoints)場合の両方に当てはまります。 このセクションに、両方の場合に必要な情報が記されています。

### <a name="using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled"></a>カスタム ドメイン名とプライベート エンドポイントが有効になっている Speech リソースの使用

カスタム ドメイン名とプライベート エンドポイントが有効になっている Speech リソースでは、Speech Services とのやり取りに別の方法を使用します。 このセクションでは、Speech Services REST API および [Speech SDK](speech-sdk.md) でこのようなリソースを使用する方法について説明します。

> [!NOTE]
> ただし、プライベート エンドポイントが有効になっていないが **カスタム ドメイン名** が有効になっている Speech リソースには、Speech Services とやり取りする特殊な方法もありますが、この方法はプライベート エンドポイントが有効になっている Speech リソースのシナリオとは異なります。 このようなリソースがある場合は (たとえば、プライベート エンドポイントが有効になっているリソースがあるが、削除することにした場合)、必ず[対応するセクション](#using-speech-resource-with-custom-domain-name-without-private-endpoints)を理解してください。

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-rest-api"></a>カスタム ドメイン名とプライベート エンドポイントが有効になっている Speech リソース。 REST API での使用方法

このセクションでは、Speech リソースの DNS 名 (カスタム ドメイン) のサンプルとして `my-private-link-speech.cognitiveservices.azure.com` を使用します。

##### <a name="note-on-speech-services-rest-api"></a>Speech Services REST API に関する注意事項

Speech Services には、[音声テキスト変換](rest-speech-to-text.md)および[テキスト読み上げ](rest-text-to-speech.md)用の REST API があります。 プライベート エンドポイントが有効になっているシナリオでは、次の点を考慮する必要があります。

Speech to Text には、2 つの異なる REST API があります。 各 API は、異なる目的に対応し、異なるエンドポイントを使用し、プライベート エンドポイントが有効になっているシナリオへのサインインに使用されるときには異なるアプローチを必要とします。

以下に、Speech to Text REST API を示します。
- [Speech to Text REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30): [バッチ文字起こし](batch-transcription.md)および [Custom Speech](custom-speech-overview.md) に使用されます。 v3.0 は、[v2.0 の後継](/azure/cognitive-services/speech-service/migrate-v2-to-v3)です。
- [Speech to Text REST API for short audio](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) は OnLine 文字起こしに使用されます。 

プライベート エンドポイント シナリオでの Speech to Text REST API for short audio と Text to Speech REST API の使用は同じであり、この記事の後半で説明する [Speech SDK での場合](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk)と同等です。 

Speech to Text REST API v3.0 は、異なるエンドポイントのセットを使用しているため、プライベート エンドポイントが有効になっているシナリオには別のアプローチが必要になります。

両方の場合について、以下のセクションで説明します。


##### <a name="speech-to-text-rest-api-v30"></a>Speech to Text REST API v3.0

通常、Speech リソースは、[Speech to Text REST API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) と通信するために、[Cognitive Services リージョン エンドポイント](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)を使用します。 これらのリソースの名前付け形式は次のとおりです。 <p/>`{region}.api.cognitive.microsoft.com`

要求 URL のサンプルを次に示します。

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```
Speech リソースのカスタム ドメインを有効にした後 (プライベート エンドポイントに必要)、このようなリソースは基本的な REST API エンドポイントに次の DNS 名パターンを使用します。 <p/>`{your custom name}.cognitiveservices.azure.com`

つまり、この例では REST API エンドポイント名は次のようになります。 <p/>`my-private-link-speech.cognitiveservices.azure.com`

また、上記の要求 URL の例は次のように変換する必要があります。
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
この URL には、プライベート エンドポイントがアタッチされている Virtual Network からアクセスできる ([正しい DNS 解決](#mandatory-check-dns-resolution-from-the-virtual-network)が提供されている) 必要があります。

したがって一般には、Speech リソースのカスタム ドメイン名を有効にした後で、すべての要求 URL のホスト名を新しいカスタム ドメインのホスト名に置き換える必要があります。 要求の他のすべての部分 (上記の例で `/speechtotext/v3.0/transcriptions` のパスなど) は同じままです。

> [!TIP]
> お客様の中には、リージョンのエンドポイント DNS 名のリージョン部分を使用する (たとえば、特定の Azure リージョンにデプロイされた Speech リソースに要求を送信する) アプリケーションを開発した場合があります。
>
> Speech リソースのカスタム ドメイン名には、リソースがデプロイされたリージョンに関する情報は含まれて **いません**。 そのため、上で説明したアプリケーション ロジックは機能 **しない** ため、変更する必要があります。

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Speech to Text REST API for short audio と Text to Speech REST API

[Speech to Text REST API for short audio](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) および [Text to Speech REST API](rest-text-to-speech.md) は、次の 2 種類のエンドポイントを使用します。
- 認証トークンを取得するために Cognitive Services REST API と通信するための [Cognitive Services リージョン エンドポイント](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)
- 他のすべての操作のための特別なエンドポイント

特別なエンドポイントの詳細と、プライベート エンドポイントが有効になっている Speech リソースに応じて URL を変換する方法の詳細については、以下の「Speech SDK での使用方法」の[このサブセクション](#general-principle)をご覧ください。 SDK について記載されている同じ原則が、Speech to Text v1.0 および Text to Speech REST API に適用されます。

前の段落で説明したサブセクションの資料を理解し、次の例をご覧ください。 (この例では、Text to Speech REST API について説明しています。Speech to Text REST API for short audio の使用方法もまったく同等です)

> [!NOTE]
> プライベート エンドポイント シナリオで **Speech to Text REST API for short audio** を使用する場合は、`Authorization` [ヘッダー](rest-speech-to-text.md#request-headers)を [介して渡された](rest-speech-to-text.md#request-headers)認証トークンを使用する必要があります。`Ocp-Apim-Subscription-Key` ヘッダーを介して Speech サブスクリプション キーを特別なエンドポイントに渡す方法は機能 **せず**、エラー 401 が生成されます。

**Text to Speech REST API の使用例。**

Azure リージョンのサンプルとして西ヨーロッパを、Speech リソース DNS 名 (カスタムドメイン) のサンプルとして `my-private-link-speech.cognitiveservices.azure.com` を使用します。 この例のカスタム ドメイン名 `my-private-link-speech.cognitiveservices.azure.com` は、西ヨーロッパ リージョンで作成された Speech リソースに属しています。

リージョンでサポートされている音声の一覧を取得するには、次の 2 つの操作を行う必要があります。

- 次の HTTP から認証トークンを取得する
```http
https://westeurope.api.cognitive.microsoft.com/sts/v1.0/issuetoken
```
- 取得したトークンを使用して、次の HTTP から音声の一覧を取得する
```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
(上記の手順の詳細については、[Text to Speech REST API に関するドキュメント](rest-text-to-speech.md)をご覧ください)

プライベート エンドポイントが有効になっている Speech リソースの場合、同じ操作シーケンスのエンドポイント URL を変更する必要があります。 同じシーケンスは次のようになります。
- 次の HTTP から認証トークンを取得する
```http
https://my-private-link-speech.cognitiveservices.azure.com/v1.0/issuetoken
```
(詳細については、上記の [Speech to Text REST API v3.0](#speech-to-text-rest-api-v30) のサブセクションを参照してください)
- 取得したトークンを使用して、次の HTTP から音声の一覧を取得する
```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
(下記の「Speech SDK での使用方法」セクションの「[一般的な原則](#general-principle)」サブセクションの詳細な説明をご覧ください)

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk"></a>カスタム ドメイン名とプライベート エンドポイントが有効になっている Speech リソース。 Speech SDK での使用方法

カスタム ドメイン名とプライベート エンドポイントが有効になっている Speech リソースで Speech SDK を使用する場合、アプリケーション コードのレビューと、おそらくは変更が必要になります。 プライベート エンドポイント シナリオのよりシームレスなサポートに向けての取り組みが行われています。

このセクションでは、Speech リソースの DNS 名 (カスタム ドメイン) のサンプルとして `my-private-link-speech.cognitiveservices.azure.com` を使用します。

##### <a name="general-principle"></a>一般的な原則

通常、SDK のシナリオ (および Text to Speech REST API シナリオ) では、Speech リソースは、さまざまなサービス内容に対して特別なリージョン エンドポイントを使用します。 これらのエンドポイントの DNS 名の形式は次のとおりです。 </p>`{region}.{speech service offering}.speech.microsoft.com`

例: </p>`westeurope.stt.speech.microsoft.com`

リージョン (DNS 名の最初の要素) に使用可能なすべての値は、[ここ](regions.md)に一覧表示されています。次の表は、Speech Services の内容 (DNS 名の 2 番目の要素) に使用できる値を示しています。

| DNS 名値 | Speech Services の内容                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [カスタム コマンド](custom-commands.md)                       |
| `convai`       | [会話の文字起こし](conversation-transcription.md) |
| `s2s`          | [音声翻訳](speech-translation.md)                 |
| `stt`          | [音声テキスト変換](speech-to-text.md)                         |
| `tts`          | [テキスト読み上げ](text-to-speech.md)                         |
| `voice`        | [Custom Voice](how-to-custom-voice.md)                      |

したがって、上の例 (`westeurope.stt.speech.microsoft.com`) は、西ヨーロッパの音声テキスト変換エンドポイントを表します。

プライベート エンドポイントが有効になっているエンドポイントは、特殊なプロキシを介して Speech Services と通信し、そのため **エンドポイント接続 URL を変更する必要があります**。 "標準" エンドポイント URL は次のパターンに従うという原則が適用されます <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

次のように変更します。 <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**例 1.** アプリケーションは次の URL を使用して通信しています (西ヨーロッパの米国英語の基本モデルを使用した音声認識)。 
```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Speech リソースのカスタム ドメイン名が `my-private-link-speech.cognitiveservices.azure.com` のときにプライベート エンドポイントが有効になっているシナリオでこれを使用するには、この URL を次のように変更する必要があります。
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

詳しく見てみましょう。
- ホスト名 `westeurope.stt.speech.microsoft.com` はカスタム ドメインのホスト名 `my-private-link-speech.cognitiveservices.azure.com` に置き換えられます
- 元の DNS 名 (`stt`) の 2 番目の要素は、URL パスの最初の要素になり、元のパスの前に置かれます。つまり、元の URL `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` は `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` になります
 
**例 2.** アプリケーションは次の URL を使用して通信しています (西ヨーロッパの米国英語の基本モデルを使用した音声合成)。 
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```
Speech リソースのカスタム ドメイン名が `my-private-link-speech.cognitiveservices.azure.com` のときにプライベート エンドポイントが有効になっているシナリオでこれを使用するには、この URL を次のように変更する必要があります。 
```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

例 1 と同じ原則が適用されますが、この場合の主要要素は `voice` になります。

##### <a name="modifying-applications"></a>アプリケーションの変更

前のセクションで説明した原則をアプリケーション コードに適用するには、次の 2 つの主要な作業を実行する必要があります。

- アプリケーションで使用しているエンドポイント URL を確認します
- 前のセクションで説明したようにエンドポイント URL を変更し、この変更された URL を明示的に使用して `SpeechConfig` クラスのインスタンスを作成します

###### <a name="determining-application-endpoint-url"></a>アプリケーション エンドポイント URL の特定

- [アプリケーションのログ記録を有効にし](how-to-use-logging.md)、アプリケーションを実行してログを生成します
- ログ ファイルで `SPEECH-ConnectionUrl` を検索します。 文字列には `value` パラメーターが含まれ、そのパラメーターにはアプリケーションで使用していた完全な URL が含まれます

エンドポイント URL を含むログ ファイル行の例を次に示します。
```
(114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
```
したがって、この例でアプリケーションが使用する URL は次のようになります。
```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```
###### <a name="creating-speechconfig-instance-using-full-endpoint-url"></a>完全なエンドポイント URL を使用した `SpeechConfig` インスタンスの作成

上記の「[一般的な原則](#general-principle)」の説明に従って、前のセクションで特定したエンドポイントを変更します。

次に、`SpeechConfig` のインスタンスの作成方法を変更する必要があります。 現在のアプリケーションではおそらく、次のようなコマンドを使用しています。
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
これは、前のセクションで説明したホスト名と URL の変更のため、プライベート エンドポイントが有効になっている Speech リソースに対しては機能しません。 プライベート エンドポイントが有効になっているリソースのキーを使用して、既存のアプリケーションを変更せずに実行しようとすると、認証エラー (401) が表示されます。

機能させるには、`SpeechConfig` クラスのインスタンス化方法を変更し、"エンドポイントから"/"エンドポイントで" の初期化を使用する必要があります。 次の 2 つの変数が定義されているとします。
- プライベート エンドポイントが有効になっている Speech リソースのキーを含んだ `subscriptionKey`
- 完全な **変更した** エンドポイント URL を含む `endPoint` (対応するプログラミング言語で必要な型を使用)。 この例では、この変数には次の内容が含まれている必要があります
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```
その後、次のように `SpeechConfig` クラスをインスタンス化する必要があります。
```csharp
var config = SpeechConfig.FromEndpoint(endPoint, subscriptionKey);
```
```cpp
auto config = SpeechConfig::FromEndpoint(endPoint, subscriptionKey);
```
```java
SpeechConfig config = SpeechConfig.fromEndpoint(endPoint, subscriptionKey);
```
```python
import azure.cognitiveservices.speech as speechsdk
speech_config = speechsdk.SpeechConfig(endpoint=endPoint, subscription=subscriptionKey)
```
```objectivec
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithEndpoint:endPoint subscription:subscriptionKey];
```
> [!TIP]
> エンドポイント URI で指定されたクエリ パラメーターは、他の API によって設定されている場合でも変更されません。 たとえば、認識言語が "language=en-US" というクエリ パラメーターとして URI に定義されていて、対応するプロパティを介して "ru-RU" にも設定されている場合、URI の言語設定が優先され、有効な言語は "en-US" になります。 他の API では、エンドポイント URI で指定されていないパラメーターのみを設定できます。

この変更を行った後、アプリケーションは、プライベートが有効になっている Speech リソースを操作する必要があります。 プライベート エンドポイント シナリオのよりシームレスなサポートに向けての取り組みが行われています。

### <a name="using-speech-resource-with-custom-domain-name-without-private-endpoints"></a>カスタム ドメイン名が有効になっていて、プライベート エンドポイントが有効になっていない Speech リソースの使用

この記事で何度か指摘しましたが、Speech リソースのカスタム ドメインを有効にすると **取り消しできず**、このようなリソースでは、Speech Services との通信に "通常の" もの (つまり、[リージョンのエンドポイント名](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)を使用するもの) とは異なる方法が使用されます。

このセクションでは、カスタム ドメイン名が有効になっているが Speech Services REST API および [Speech SDK](speech-sdk.md) を使用したプライベート エンドポイントが **有効になっていない** Speech リソースを使用する方法について説明します。 これは、プライベート エンドポイント シナリオで 1 回使用されたが、そのプライベート エンドポイントが削除されたリソースである場合があります。

#### <a name="dns-configuration"></a>DNS の構成

プライベート エンドポイントが有効になっている Speech リソースのカスタム ドメイン DNS 名が、[パブリック ネットワークから解決](#optional-check-dns-resolution-from-other-networks)される方法を思い出してください。 この場合、解決された IP アドレスは、VNet プロキシ エンドポイントを指しており、これは、プライベート エンドポイントが有効になっている Cognitive Services リソースに、ネットワーク トラフィックをディスパッチするために使用されます。

ただし、**すべて** のリソース プライベート エンドポイントが削除されたときに (またはカスタム ドメイン名を有効にした直後に)、Speech リソースの CNAME レコードは再プロビジョニングされ、対応する [Cognitive Services リージョン エンドポイント](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)の IP アドレスを指すようになります。

したがって `nslookup` のコマンドの出力は次のようになります。
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    apimgmthskquihpkz6d90kmhvnabrx3ms3pdubscpdfk1tsx3a.cloudapp.net
Address:  13.93.122.1
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          westeurope.api.cognitive.microsoft.com
          cognitiveweprod.trafficmanager.net
          cognitiveweprod.azure-api.net
          apimgmttmdjylckcx6clmh2isu2wr38uqzm63s8n4ub2y3e6xs.trafficmanager.net
          cognitiveweprod-westeurope-01.regional.azure-api.net
```
[このセクション](#optional-check-dns-resolution-from-other-networks)の出力と比較します。

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-rest-api"></a>カスタム ドメイン名が有効になっていて、プライベート エンドポイントが有効になっていない Speech リソース。 REST API での使用方法

##### <a name="speech-to-text-rest-api-v30"></a>Speech to Text REST API v3.0

Speech to Text REST API v3.0 の使用方法は、[プライベート エンドポイントが有効になっている Speech リソース](#speech-to-text-rest-api-v30)の場合と完全に同じです。

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Speech to Text REST API for short audio と Text to Speech REST API

この場合、Speech to Text REST API for short audio と Text to Speech REST API の使用方法は、一般的な場合では違いはありませんが、Speech to Text REST API for short audio には例外が 1 つあります (下記の注を参照)。 これらの API は両方とも、[Speech to Text REST API for short audio](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) と [Text to Speech REST API](rest-text-to-speech.md) のドキュメントの説明に従って使用する必要があります。

> [!NOTE]
> カスタム ドメイン シナリオで **Speech to Text REST API for short audio** を使用する場合は、`Authorization` [ヘッダー](rest-speech-to-text.md#request-headers)を [介して渡された](rest-speech-to-text.md#request-headers)認証トークンを使用する必要があります。`Ocp-Apim-Subscription-Key` ヘッダーを介して Speech サブスクリプション キーを特別なエンドポイントに渡す方法は機能 **せず**、エラー 401 が生成されます。

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-speech-sdk"></a>カスタム ドメイン名が有効になっていて、プライベート エンドポイントが有効になっていない Speech リソース。 Speech SDK での使用方法

カスタム ドメイン名が有効になっていて、プライベート エンドポイントが有効になって **いない** Speech リソースで Speech SDK を使用する場合、アプリケーション コードのレビューと、おそらくは変更が必要になります。 これらの変更は、[のプライベート エンドポイントが有効になっている Speech リソース](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk)の場合と比較して、**異なる** ことに注意してください。 プライベート エンドポイント/カスタム ドメイン シナリオのよりシームレスなサポートに向けての取り組みが行われています。

このセクションでは、Speech リソースの DNS 名 (カスタム ドメイン) のサンプルとして `my-private-link-speech.cognitiveservices.azure.com` を使用します。

[プライベート エンドポイントが有効になっている Speech リソース](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk)に関するセクションでは、使用されているエンドポイント URL を特定し、それを変更して、`SpeechConfig` クラス インスタンスの "エンドポイントから"/"エンドポイントで" の初期化を通じて機能させる方法について説明しました。

ただし、すべてのプライベート エンドポイントが削除された後に同じアプリケーションを実行しようとすると (対応する DNS レコードの再プロビジョニングにしばらく時間がかかることがあります)、内部サービスエラー (404) が発生します。 この理由は、[DNS レコード](#dns-configuration) が VNet プロキシではなく、Cognitive Services リージョン エンドポイントを指すようになって、`/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` などの URL パスが見つからないためであり、その結果、"見つかりません" というエラー (404) が表示されます。

アプリケーションを、次のスタイルで `SpeechConfig` の "標準" インスタンス化に "ロールバック" する場合は、
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
アプリケーションは、認証エラー (401) で終了します。

##### <a name="modifying-applications"></a>アプリケーションの変更

カスタム ドメイン名が有効になっていて、プライベート エンドポイントが有効になっていない Speech リソースのシナリオでアプリケーションを有効にするには、次の操作を行う必要があります。
- Cognitive Services REST API を介して認証トークンを要求します
- "認証トークンから"/"認証トークンで" のメソッドを使用して `SpeechConfig` クラスをインスタンス化します 

###### <a name="requesting-authorization-token"></a>認証トークンの要求

Cognitive Services REST API を介してトークンを取得する方法については、[この記事](../authentication.md#authenticate-with-an-authentication-token)をご覧ください。 

エンドポイント URL でカスタム ドメイン名を使用します。つまり、この URL は例では次のようになります。
```http
https://my-private-link-speech.cognitiveservices.azure.com/sts/v1.0/issueToken
```
> [!TIP]
> この URL は、Azure portal の Speech リソースの *[キーとエンドポイント]* (*リソース管理* グループ) セクションにあります。

###### <a name="creating-speechconfig-instance-using-authorization-token"></a>認証トークンを使用した `SpeechConfig` インスタンスの作成

前のセクションで取得した認証トークンを使用して `SpeechConfig` クラスをインスタンス化する必要があります。 次の変数が定義されているとします。

- 前のセクションで取得された認証トークンを含む `token`
- Speech リソース [リージョン](regions.md)の名前を含む `azureRegion` (例: `westeurope`)
- `outError` ([Objective C](/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithauthorizationtokenregionerror) の場合のみ)

その後、次のように `SpeechConfig` クラスをインスタンス化する必要があります。
```csharp
var config = SpeechConfig.FromAuthorizationToken(token, azureRegion);
```
```cpp
auto config = SpeechConfig::FromAuthorizationToken(token, azureRegion);
```
```java
SpeechConfig config = SpeechConfig.fromAuthorizationToken(token, azureRegion);
```
```python
import azure.cognitiveservices.speech as speechsdk
speech_config = speechsdk.SpeechConfig(auth_token=token, region=azureRegion)
```
```objectivec
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithAuthorizationToken:token region:azureRegion error:outError];
```
> [!NOTE]
> 呼び出し元は、認証トークンが有効であることを確認する必要があります。 認証トークンの有効期限が切れる前に、呼び出し元は、新しい有効なトークンを使用してこのセッターを呼び出すことによって、認証トークンを更新する必要があります。 新しい認識エンジン/シンセサイザーを作成するときに構成値がコピーされるため、既に作成されている認識エンジンに新しいトークン値は適用されません。 以前に作成された認識エンジン/シンセサイザーの場合、トークンを更新するには、対応する認識エンジン/シンセサイザーの認証トークンを設定する必要があります。 それ以外の場合、認識/合成中に認識エンジン/シンセサイザーでエラーが発生します。

この変更を行った後、アプリケーションは、カスタム ドメイン名が有効になっていて、プライベート エンドポイントが有効になっていない Speech リソースを操作する必要があります。 カスタム ドメイン/プライベート エンドポイント シナリオのよりシームレスなサポートに向けての取り組みが行われています。

## <a name="pricing"></a>価格

料金の詳細については、「[Azure Private Link の料金](https://azure.microsoft.com/pricing/details/private-link)」をご覧ください。

## <a name="next-steps"></a>次のステップ

* [Azure Private Link](../../private-link/private-link-overview.md) の詳細
* [Speech SDK](speech-sdk.md) の詳細
* [Speech to text REST API](rest-speech-to-text.md) の詳細
* [Text-to-speech REST API](rest-text-to-speech.md) の詳細
