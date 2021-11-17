---
title: ネットワークの分離と Private Link - 質問応答
description: ユーザーは、質問応答リソースへのパブリック アクセスを制限できます。
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 9ab30b82a9bac9d06e2d5228b2004c36d465d392
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458211"
---
#  <a name="network-isolation-and-private-endpoints"></a>ネットワーク分離とプライベート エンドポイント

下の手順では、パブリック アクセスを質問応答リソースに制限する方法と、Azure Private Link を有効にする方法について説明します。 [仮想ネットワークを構成](../../../cognitive-services-virtual-networks.md?tabs=portal)して、Cognitive Services リソースをパブリック アクセスから保護します。

## <a name="private-endpoints"></a>プライベート エンドポイント

Azure プライベート エンドポイントは、Azure Private Link を使用するサービスにプライベートかつ安全に接続するネットワーク インターフェイスです。  質問応答では、Azure Search Service へのプライベート エンドポイントの作成がサポートされています。

プライベート エンドポイントは、別サービスとして、[Azure Private Link](../../../../private-link/private-link-overview.md) によって提供されます。 コストの詳細については、[価格ページ](https://azure.microsoft.com/pricing/details/private-link/)を参照してください。

## <a name="steps-to-enable-private-endpoint"></a>プライベート エンドポイントを有効にするための手順

1. "共同作成者" ロールを Azure Search サービス インスタンスの言語リソース (コンテキストによっては Text Analytics リソースとして表示される場合があります) に割り当てます。 この操作では、サブスクリプションへの "*所有者*" アクセス権が必要です。 サービス リソースの [ID] タブに移動して、ID を取得します。

> [!div class="mx-imgBorder"]
> ![Text Analytics ID](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoints-identity.png)

2. [Azure Search Service IAM]\(Azure Search サービスの IAM\) タブに移動して、上記の ID を *[共同作成者]* として追加します。

![マネージド サービス IAM](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. *[ロールの割り当ての追加]* を選択して、ID を追加し、 *[保存]* を選択します。

![マネージド ロールの割り当て](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. 次に、Azure Search サービス インスタンスの *[ネットワーク]* タブに移動し、エンドポイント接続データを *[パブリック]* から *[プライベート]* に切り替えます。 この操作は実行時間の長いプロセスであり、完了するまで最大で 30 分かかることがあります。 

![マネージド Azure 検索ネットワーク](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. 言語リソースの *[ネットワーク]* タブに移動し、 *[許可するアクセス元]* で *[選択したネットワークとプライベート エンドポイント]* オプションを選択し、 *[保存]* を選択します。
 
> [!div class="mx-imgBorder"]
> ![Text Analytics ネットワーク](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-custom-qna.png)

これにより、言語リソースと Azure Cognitive Search サービス インスタンスの間にプライベート エンドポイント接続が確立されます。 Azure Cognitive Search サービス インスタンスの *[ネットワーク]* タブで、プライベート エンドポイント接続を確認できます。 操作全体が完了したら、質問応答が有効になっている言語リソースを使用できます。

![マネージド ネットワーク サービス](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)

## <a name="support-details"></a>サポートの詳細
 * 言語リソースへのプライベート アクセスを有効にすると、Azure Cognitive Search サービスへの変更はサポートされません。 プライベート アクセスを有効にした後に [機能] タブで Azure Cognitive Search サービスを変更すると、言語リソースを使用できなくなります。

 * プライベート エンドポイント接続を確立した後に Azure Cognitive Search サービス ネットワークを [パブリック] に切り替えると、言語リソースを使用できなくなります。 プライベート エンドポイント接続を機能させるには、Azure Search サービス ネットワークが [プライベート] になっている必要があります。

## <a name="restrict-access-to-cognitive-search-resource"></a>Cognitive Search リソースへのアクセスを制限する

下の手順に従って、パブリック アクセスを質問応答言語リソースに制限します。 [仮想ネットワークを構成](../../../cognitive-services-virtual-networks.md?tabs=portal)して、Cognitive Services リソースをパブリック アクセスから保護します。

VNet に基づいて Cognitive Service リソースへのアクセスを制限した後、オンプレミス ネットワークまたはローカル ブラウザーから Language Studio ポータル上のナレッジ ベースを参照します。
- [オンプレミス ネットワーク](../../../cognitive-services-virtual-networks.md?tabs=portal#configuring-access-from-on-premises-networks)へのアクセスを許可します。
- [ローカルのブラウザーとマシン](../../../cognitive-services-virtual-networks.md?tabs=portal#managing-ip-network-rules)へのアクセスを許可します。
- **[ネットワーク]** タブの **[ファイアウォール] セクションの下にマシンのパブリック IP アドレス** を追加します。既定では、`portal.azure.com` には現在アクセスしているマシンのパブリック IP が表示され (このエントリを選択します)、次に **[保存]** を選択します。

     > [!div class="mx-imgBorder"]
     > [ ![ファイアウォールと仮想ネットワークの構成 UI のスクリーンショット]( ../../../qnamaker/media/network-isolation/firewall.png) ](  ../../../qnamaker/media/network-isolation/firewall.png#lightbox)
