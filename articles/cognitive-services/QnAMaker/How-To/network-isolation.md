---
title: ネットワークの分離
description: ユーザーは QnA Maker リソースへのパブリック アクセスを制限できます。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 8fe8c07866b23e5d990b71bfc9cd556c338634d3
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203370"
---
# <a name="recommended-settings-for-network-isolation"></a>ネットワーク分離の推奨設定

QnA Maker リソースへのパブリック アクセスを制限するには、次の手順に従う必要があります。 [仮想ネットワークを構成](../../cognitive-services-virtual-networks.md?tabs=portal)して、Cognitive Services リソースをパブリック アクセスから保護します。

## <a name="restrict-access-to-cognitive-search-resource"></a>Cognitive Search リソースへのアクセスを制限する

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

Cognitive Search を VNET 内のプライベート エンドポイントとして構成する。 QnA Maker リソースの作成中に検索インスタンスが作成されるとき、完全に顧客の VNET 内に作成されたプライベート エンドポイント構成をサポートするよう Cognitive Search に強制することができます。

プライベート エンドポイントを使用するには、すべてのリソースが同じリージョンに作成されている必要があります。

* QnA Maker リソース
* 新しい Cognitive Search リソース
* 新しい Virtual Network リソース

[Azure portal](https://portal.azure.com) で次の手順を実行します。

1. [QnA Maker リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)を作成します。
2. エンドポイント接続 (データ) が "_プライベート_" に設定された新しい Cognitive Search リソースを作成します。 このリソースは、手順 1. で作成した QnA Maker リソースと同じリージョンに作成します。 [Cognitive Search リソースの作成](../../../search/search-create-service-portal.md)について理解を深めてから、こちらのリンクを使用して[リソースの作成ページ](https://ms.portal.azure.com/#create/Microsoft.Search)に直接移動します。
3. 新しい[仮想ネットワーク リソース](https://ms.portal.azure.com/#create/Microsoft.VirtualNetwork-ARM)を作成します。
4. この手順の手順 1. で作成した App Service リソース上に VNET を構成します。 手順 2. で作成した新しい Cognitive Search リソース用に、Cognitive Search の IP アドレスへの新しい DNS エントリを VNET に作成します。 。
5. 手順 2. で作成した[新しい Cognitive Search リソースに App Service を関連付け](../how-to/set-up-qnamaker-service-azure.md)ます。 その後は、手順 1. で作成した元の Cognitive Search リソースを削除してかまいません。
    
[QnA Maker ポータル](https://www.qnamaker.ai/)で、初めてのナレッジ ベースを作成します。

#  <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

Azure Search リソースに[プライベート エンドポイントを作成](../reference-private-endpoint.md)します。

---

## <a name="restrict-access-to-app-service-qna-runtime"></a>App Service (QnA Runtime) へのアクセスを制限する

App Service 許可リストに IP を追加してアクセスを制限したり、QnA Maker App Service をホストするように App Service 環境を構成したりできます。

#### <a name="add-ips-to-app-service-allowlist"></a>App Service 許可リストへの IP の追加

1. Cognitive Services IP からのトラフィックのみを許可します。 これらは、既にサービスタグ `CognitiveServicesManagement` に含まれています。 これは、オーサリング API シリーズ (KB の作成/更新) で App Service を起動し、それに応じて Azure Search Service を更新するために必要です。 [サービス タグに関する詳細](../../../virtual-network/service-tags-overview.md)を確認します。
2. Azure Bot Service、QnA Maker ポータルなどの他のエントリ ポイントにも予測 "GenerateAnswer" API アクセスを許可していることを確認します。
3. IP アドレスの範囲を許可リストに追加するには、次の手順に従ってください。

   1. [すべてのサービス タグの IP 範囲](https://www.microsoft.com/download/details.aspx?id=56519)をダウンロードします。
   2. "CognitiveServicesManagement" の IP を選択します。
   3. App Service リソースのネットワーク セクションに移動し、[アクセス制限の構成] をクリックして、許可リストに IP を追加します。

    ![受信ポートの例外](../media/inbound-ports.png)

また、App Service に対して同じ処理を行う自動スクリプトも用意されています。 [許可リストを構成するための PowerShell スクリプト](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1)については、GitHub 上にあります。 スクリプト パラメーターとして、サブスクリプション ID、リソース グループ、および実際の App Service 名を入力する必要があります。 スクリプトを実行すると、App Service の許可リストに IP が自動的に追加されます。

#### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>QnA Maker App Service をホストするように App Service Environment を構成する
    
App Service Environment (ASE) を使用して、QnA Maker App Service をホストできます。 次の手順に従ってください。

1. App Service Environment を作成し、"外部" としてマークします。 手順については、[チュートリアル](../../../app-service/environment/create-external-ase.md)を参照してください。
2.  App Service Environment 内に App Service を作成します。
    1. App Service の構成を確認し、アプリケーション設定として "PrimaryEndpointKey" を追加します。 'PrimaryEndpointKey' の値は、“\<app-name\>-PrimaryEndpointKey” に設定されている必要があります。 アプリ名は、App Service の URL で定義されています。 たとえば、App Service の URL が "mywebsite.myase.p.azurewebsite.net" の場合、アプリ名は "mywebsite" になります。 この場合、'PrimaryEndpointKey' の値は “mywebsite-PrimaryEndpointKey” に設定されている必要があります。
    2. Azure Search サービスを作成します。
    3. Azure Search とアプリの設定が適切に構成されていることを確認します。 
          この[チュートリアル](../reference-app-service.md?tabs=v1#app-service)を参照してください。
3.  App Service Environment に関連付けられているネットワーク セキュリティ グループを更新します。
    1. 事前に作成された受信セキュリティ規則を要件に従って更新します。
    2. ソースが 'Service Tag'、ソース サービス タグが 'CognitiveServicesManagement' の新しい受信セキュリティ規則を追加します。
4.  Azure Resource Manager を使用して QnA Maker Cognitive Services インスタンス (Microsoft.CognitiveServices/accounts) を作成します。QnA Maker エンドポイントは上記で作成した App Service Environment (https:// mywebsite.myase.p.azurewebsite.net) に設定する必要があります。
    
---
