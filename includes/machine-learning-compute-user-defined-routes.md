---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 08/12/2021
ms.author: larryfr
ms.openlocfilehash: 8f60a29adcdf8e679a523748a97a24ba2f06cbdd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739149"
---
Azure Machine Learning の __コンピューティング インスタンス__ または __コンピューティング クラスター__ を使用するときは、Azure Batch 管理サービスと Azure Machine Learning service からのインバウンド トラフィックを許可します。 このトラフィックにユーザー定義ルートを作成する場合は、**IP アドレス** または **サービス タグ** を使用してトラフィックをルーティングできます。

> [!IMPORTANT]
> ユーザー定義ルートでのサービス タグの使用は現在プレビュー段階であり、完全にはサポートされていない可能性があります。 詳細については、[仮想ネットワーク ルーティング](/azure/virtual-network/virtual-networks-udr-overview.md#service-tags-for-user-defined-routes-preview)に関する記事を参照してください。

# <a name="ip-address-routes"></a>[IP アドレス ルート](#tab/ipaddress)

Azure Machine Learning service の場合は、__プライマリ__ と __セカンダリ__ の両方のリージョンの IP アドレスを追加する必要があります。 セカンダリ リージョンを見つけるには、[Azure のペアになっているリージョンを使用したビジネス継続性とディザスター リカバリー](/azure/best-practices-availability-paired-regions.md#azure-regional-pairs)に関するページを参照してください。 たとえば、Azure Machine Learning service が米国東部 2 にある場合、セカンダリ リージョンは米国中部です。 

Batch サービスと Azure Machine Learning service の IP アドレスの一覧を取得するには、次のいずれかの方法を使用します。

* [Azure の IP 範囲とサービス タグ](https://www.microsoft.com/download/details.aspx?id=56519)をダウンロードし、`BatchNodeManagement.<region>` と `AzureMachineLearning.<region>` のファイルを検索する。ここで、`<region>` は Azure リージョンです。

* [Azure CLI](/cli/azure/install-azure-cli) を使用して情報をダウンロードする。 次の例では、IP アドレス情報をダウンロードし、米国東部 2 リージョン (プライマリ) と米国中部リージョン (セカンダリ) の情報を除外します。

    ```azurecli-interactive
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
    # Get primary region IPs
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
    # Get secondary region IPs
    az network list-service-tags -l "Central US" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='centralus']"
    ```

    > [!TIP]
    > 米国バージニア、米国アリゾナ、または中国東部 2 のリージョンを使用している場合、これらのコマンドは IP アドレスを返しません。 代わりに、次のいずれかのリンクを使用して IP アドレスの一覧をダウンロードします。
    >
    > * [Azure Government の Azure IP 範囲とサービス タグ](https://www.microsoft.com/download/details.aspx?id=57063)
    > * [Azure China の Azure IP 範囲とサービス タグ](https://www.microsoft.com//download/details.aspx?id=57062)

> [!IMPORTANT]
> IP アドレスは、時間の経過と共に変化する可能性があります。

UDR を作成するときは、 __[ネクスト ホップの種類]__ を __[インターネット]__ に設定します。 次の図に、Azure portal での IP アドレス ベースの UDR の例を示します。

:::image type="content" source="./media/machine-learning-compute-user-defined-routes/user-defined-route.png" alt-text="ユーザー定義ルートの構成の画像":::

# <a name="service-tag-routes"></a>[サービス タグのルート](#tab/servicetag)

次のサービス タグに対してユーザー定義ルートを作成します。

* `AzureMachineLearning`
* `BatchNodeManagement.<region>`。`<region>` は Azure リージョンです。

次のコマンドは、これらのサービス タグのルートを追加する方法を示したものです。

```azurecli
az network route-table route create -g MyResourceGroup --route-table-name MyRouteTable -n AzureMLRoute --address-prefix AzureMachineLearning --next-hop-type Internet
az network route-table route create -g MyResourceGroup --route-table-name MyRouteTable -n BatchRoute --address-prefix BatchNodeManagement.westus2 --next-hop-type Internet
```

---

UDR の構成については、[ルーティング テーブルを使用したネットワーク トラフィックのルーティング](/azure/virtual-network/tutorial-create-route-table-portal.md)に関する記事を参照してください。