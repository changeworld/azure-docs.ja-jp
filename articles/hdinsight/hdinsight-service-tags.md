---
title: Azure HDInsight で使用されるネットワーク セキュリティ グループ (NSG) サービス タグ
description: ネットワーク セキュリティ グループに IP アドレスを明示的に追加することなく、HDInsight の正常性と管理サービス ノードからクラスターへの受信トラフィックを許可するには、HDInsight のサービス タグを使用します。
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/10/2020
ms.openlocfilehash: a72753d5553e79a8ed28c3afcc7e54af6c2d230c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117240"
---
# <a name="network-security-group-nsg-service-tags-for-azure-hdinsight"></a>Azure HDInsight で使用されるネットワーク セキュリティ グループ (NSG) サービス タグ

ネットワーク セキュリティ グループ (NSG) 用の HDInsight サービス タグは、正常性および管理サービスのための IP アドレスのグループです。 これらのグループを使用すると、セキュリティ規則の作成の複雑さを最小限に抑えることができます。 特定の IP アドレスからの受信トラフィックを許可するために、ネットワーク セキュリティ グループで[管理 IP アドレス](../virtual-network/security-overview.md#service-tags)を個別に入力する代わりの方法として、[サービス タグ](hdinsight-management-ip-addresses.md)を使用できます。

これらのサービス タグは、HDInsight サービスによって作成および管理されます。 独自のサービス タグを作成したり、既存のタグを変更したりすることはできません。 サービス タグと一致するアドレス プレフィックスの管理は Microsoft によって行われ、アドレスが変化するとサービス タグは自動的に更新されます。

## <a name="getting-started-with-service-tags"></a>サービス タグの概要

ネットワーク セキュリティ グループでサービスタ グを使用するには、次の 2 つのオプションがあります。

1. 単一の HDInsight サービ スタグを使用する - このオプションを選択すると、すべてのリージョンでクラスターを監視するために HDInsight サービスによって使用されているすべての IP アドレスに対して、仮想ネットワークが開かれます。 このオプションは最も簡単な方法ですが、セキュリティ要件の制限が厳しい場合は適さないことがあります。

1. リージョンごとに複数のサービス タグを使用する - このオプションを選択すると、その特定のリージョンで HDInsight によって使用されている IP アドレスに対してのみ、仮想ネットワークが開かれます。 ただし、複数のリージョンを使用している場合は、複数のサービス タグを仮想ネットワークに追加する必要があります。

## <a name="use-a-single-global-hdinsight-service-tag"></a>単一のグローバル HDInsight サービス タグを使用する

HDInsight クラスターでサービス タグを使い始める最も簡単な方法は、ネットワーク セキュリティ グループの規則にグローバル タグ `HDInsight` を追加することです。

1. [[Azure portal]](https://portal.azure.com/) で、使用しているネットワーク セキュリティ グループを選択します。

1. **[設定]** で **[受信セキュリティ規則]** を選択し、 **[+ 追加]** を選択します。

1. **[ソース]** ドロップダウン リストで、 **[サービス タグ]** を選択します。

1. **[発信元サービス タグ]** ドロップダウン リストから、 **[HDInsight]** を選択します。

    ![Azure portal のサービス タグの追加](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

このタグを使うと、HDInsight が使用可能なすべてのリージョンの正常性および管理サービスの IP アドレスが組み込まれ、クラスターが作成された場所に関係なく、必要な正常性および管理サービスと通信できることが保証されます。

## <a name="use-regional-hdinsight-service-tags"></a>リージョンごとの HDInsight サービス タグを使用する

より制限の厳しいアクセス許可が必要になるため、1 番目のオプションが機能しない場合は、お使いのリージョンに適用されるサービス タグだけを許可することができます。 クラスターが作成されるリージョンに応じて、1 つ、2 つ、または 3 つのサービス タグを適用できます。

リージョンに追加されるサービス タグを確認するには、以下のセクションを参照してください。

### <a name="use-a-single-regional-service-tag"></a>1 つのリージョン サービス タグを使用する

2 番目のサービス タグ オプションを使い、クラスターがこの表に記載されているいずれかのリージョンにある場合は、1 つのリージョン サービス タグをネットワーク セキュリティ グループに追加するだけで済みます。

| Country | リージョン | サービス タグ |
| ---- | ---- | ---- |
| オーストラリア | オーストラリア東部 | HDInsight.AustraliaEast |
| &nbsp; | オーストラリア南東部 | HDInsight.AustraliaSoutheast |
| &nbsp; | オーストラリア中部 | HDInsight.AustraliaCentral |
| 中国 | 中国東部 2 | HDInsight.ChinaEast2 |
| &nbsp; | 中国北部 2 | HDInsight.ChinaNorth2 |
| アメリカ | 米国中北部 | HDInsight.NorthCentralUS |
| &nbsp; | 米国西部 2 | HDInsight.WestUS2 |
| &nbsp; | 米国中西部 | HDInsight.WestCentralUS |
| カナダ | カナダ東部 | HDInsight.CanadaEast |
| ブラジル | ブラジル南部 | HDInsight.BrazilSouth |
| 韓国 | 韓国中部 | HDInsight.KoreaCentral |
| &nbsp; | 韓国南部 | HDInsight.KoreaSouth |
| インド | インド中部 | HDInsight.CentralIndia |
| &nbsp; | インド南部 | HDInsight.SouthIndia |
| 日本 | 西日本 | HDInsight.JapanWest |
| フランス | フランス中部| HDInsight.FranceCentral |
| 英国 | 英国南部 | HDInsight.UKSouth |
| Azure Government | USDoD 中部   | HDInsight.USDoDCentral |
| &nbsp; | USGov テキサス | HDInsight.USGovTexas |
| &nbsp; | USDoD 東部 | HDInsight.USDoDEast |
| &nbsp; | USGov アリゾナ | HDInsight.USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>複数のリージョン サービス タグを使用する

2 番目のサービス タグ オプションを使い、クラスターが作成されるリージョンが上の一覧に含まれない場合は、複数のリージョン サービス タグを許可する必要があります。 複数のリージョンを使用する必要があるのは、リージョンによってリソース プロバイダーの配置に違いがあるためです。

残りのリージョンは、使用されるリージョン サービス タグに基づいてグループに分割されます。

#### <a name="group-1"></a>グループ 1

次の表のいずれかのリージョンにクラスターが作成される場合は、一覧で示されているリージョン サービス タグに加えて、サービス タグ `HDInsight.WestUS` と `HDInsight.EastUS` を許可します。 このセクションのリージョンには、3 つのサービス タグが必要です。

たとえば、クラスターが `East US 2` リージョンに作成される場合は、次のサービス タグをネットワーク セキュリティ グループに追加する必要があります。

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Country | リージョン | サービス タグ |
| ---- | ---- | ---- |
| アメリカ | 米国東部 2 | HDInsight.EastUS2 |
| &nbsp; | 米国中部 | HDInsight.CentralUS |
| &nbsp; | 米国中北部 | HDInsight. NorthCentralUS |
| &nbsp; | 米国中南部 | HDInsight.SouthCentralUS |
| &nbsp; | East US | HDInsight.EastUS |
| &nbsp; | 米国西部 | HDInsight.WestUS |
| 日本 | 東日本 | HDInsight.JapanEast |
| ヨーロッパ | 北ヨーロッパ | HDInsight.NorthEurope |
| &nbsp; | 西ヨーロッパ| HDInsight.WestEurope |
| アジア | 東アジア | HDInsight.EastAsia |
| &nbsp; | 東南アジア | HDInsight.SoutheastAsia |
| オーストラリア | オーストラリア東部 | HDInsight.AustraliaEast |

#### <a name="group-2"></a>グループ 2

**中国北部**および**中国東部**リージョンのクラスターでは、2 つのサービス タグ `HDInsight.ChinaNorth` と `HDInsight.ChinaEast` を許可する必要があります。

#### <a name="group-3"></a>グループ 3

**US Gov アイオワ**および **US Gov バージニア** リージョンのクラスターでは、2 つのサービス タグ `HDInsight.USGovIowa` と `HDInsight.USGovVirginia` を許可する必要があります。

#### <a name="group-4"></a>グループ 4

**ドイツ中部**および**ドイツ北東部**リージョンのクラスターでは、2 つのサービス タグ `HDInsight.GermanyCentral` と `HDInsight.GermanyNorthEast` を許可する必要があります。

## <a name="next-steps"></a>次のステップ

- [ネットワーク セキュリティ グループ - サービス タグ](../virtual-network/security-overview.md#security-rules)
- [Azure HDInsight クラスターの仮想ネットワークの作成](hdinsight-create-virtual-network.md)
