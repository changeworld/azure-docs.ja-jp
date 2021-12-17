---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 08/26/2021
ms.author: larryfr
ms.openlocfilehash: 5d9575afdad08bed279f2f9ea3f9a455d54338a5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130241871"
---
Azure Machine Learning の __コンピューティング インスタンス__ (パブリック IP あり) または __コンピューティング クラスター__ を使用するときは、Azure Batch 管理サービスと Azure Machine Learning service からのインバウンド トラフィックを許可します。 パブリック IP (プレビュー) を持つコンピューティング インスタンスでは、このインバウンド通信は必要ありません。 このトラフィックを許可するネットワーク セキュリティ グループは自動的に動的に作成されますが、ファイアウォールがある場合は、ユーザー定義ルート (UDR) の作成も必要な場合があります。 このトラフィック用の UDR を作成する場合は、**IP アドレス** または **サービス タグ** を使用してトラフィックをルーティングできます。

> [!IMPORTANT]
> ユーザー定義ルートでのサービス タグの使用は現在プレビュー段階であり、完全にはサポートされていない可能性があります。 詳細については、[仮想ネットワーク ルーティング](../articles/virtual-network/virtual-networks-udr-overview.md#service-tags-for-user-defined-routes-preview)に関する記事を参照してください。

> [!TIP]
> パブリック IP (プレビュー機能) のないコンピューティング インスタンスでは、このインバウンド トラフィック用に UDR は必要ありませんが、コンピューティング クラスターまたはパブリック IP を持つコンピューティング インスタンスも使用する場合は、これらの UDR がやはり必要です。


# <a name="ip-address-routes"></a>[IP アドレス ルート](#tab/ipaddress)

Azure Machine Learning service の場合は、__プライマリ__ と __セカンダリ__ の両方のリージョンの IP アドレスを追加する必要があります。 セカンダリ リージョンを見つけるには、[Azure のペアになっているリージョンを使用したビジネス継続性とディザスター リカバリー](../articles/best-practices-availability-paired-regions.md#azure-regional-pairs)に関するページを参照してください。 たとえば、Azure Machine Learning service が米国東部 2 にある場合、セカンダリ リージョンは米国中部です。 

Batch サービスと Azure Machine Learning service の IP アドレスの一覧を取得するには、[Azure IP 範囲とサービス タグ](https://www.microsoft.com/download/details.aspx?id=56519)をダウンロードし、`BatchNodeManagement.<region>` と `AzureMachineLearning.<region>` についてファイルを検索します。ここで、`<region>` には Azure リージョンを指定します。

> [!IMPORTANT]
> IP アドレスは、時間の経過と共に変化する可能性があります。

UDR を作成するときは、 __[ネクスト ホップの種類]__ を __[インターネット]__ に設定します。 次の図に、Azure portal での IP アドレス ベースの UDR の例を示します。

:::image type="content" source="./media/machine-learning-compute-user-defined-routes/user-defined-route.png" alt-text="ユーザー定義ルートの構成の画像":::

# <a name="service-tag-preview-routes"></a>[サービス タグ (プレビュー) のルート](#tab/servicetag)

次のサービス タグに対してユーザー定義ルートを作成します。

* `AzureMachineLearning`
* `BatchNodeManagement.<region>`。`<region>` は Azure リージョンです。

次のコマンドは、これらのサービス タグのルートを追加する方法を示したものです。

```azurecli
az network route-table route create -g MyResourceGroup --route-table-name MyRouteTable -n AzureMLRoute --address-prefix AzureMachineLearning --next-hop-type Internet
az network route-table route create -g MyResourceGroup --route-table-name MyRouteTable -n BatchRoute --address-prefix BatchNodeManagement.westus2 --next-hop-type Internet
```

---

UDR の構成については、[ルーティング テーブルを使用したネットワーク トラフィックのルーティング](../articles/virtual-network/tutorial-create-route-table-portal.md)に関する記事を参照してください。