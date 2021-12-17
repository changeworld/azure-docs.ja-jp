---
title: Registry サービス階層と機能
description: Azure Container Registry の Basic、Standard、および Premium サービス階層 (SKU) の機能と制限 (クォータ) について説明します。
ms.topic: article
ms.date: 08/12/2021
ms.openlocfilehash: 8cda435193e5a54216a562bdac51f036f93829f1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473352"
---
# <a name="azure-container-registry-service-tiers"></a>Azure Container Registry サービス階層

Azure Container Registry は、複数のサービス階層 (別名 SKU) で使用できます。 これらの階層は、Azure におけるプライベート Docker レジストリの容量と使用パターンに合ったさまざまなオプションと予測可能な価格を提供します。

| レベル | 説明 |
| --- | ----------- |
| **Basic** | Azure Container Registry について学習する開発者向けのコスト最適化エントリ ポイント。 Basic レジストリには Standard および Premium と同じプログラム機能があります (Azure Active Directory [認証の統合](container-registry-authentication.md#individual-login-with-azure-ad)、[イメージ削除][container-registry-delete]、[Webhook][container-registry-webhook] など)。 ただし、含まれているストレージとイメージのスループットは、使用率が低いシナリオに最も適しています。 |
| **Standard** | Standard レジストリは、Basic と同じ機能を提供しますが、含まれているストレージとイメージ スループットが拡大されています。 Standard レジストリは、ほとんどの運用シナリオのニーズを満たすはずです。 |
| **Premium** | Premium レジストリは、含まれているストレージが最も大きく、同時実行操作数も最大であり、大容量シナリオに対応できます。 イメージのスループットの増加に加え、Premium では、複数のリージョン間で 1 つのレジストリを管理するための [geo レプリケーション][container-registry-geo-replication]、イメージ タグに署名するための[コンテンツの信頼](container-registry-content-trust.md)、レジストリへのアクセスを制限する[プライベート エンドポイントがあるプライベート リンク](container-registry-private-link.md)などの機能が追加されています。 |

Basic、Standard、および Premium 階層は、すべて同じプログラム機能を提供しています。 また、これらすべては、Azure によって完全に管理されている[イメージ ストレージ][container-registry-storage]から恩恵を受けられます。 上位の階層を選択するほど、パフォーマンスとスケールが向上します。 複数のサービス レベルがあることで、最初は Basic を導入し、その後、レジストリの使用量の増加に伴って Standard や Premium に切り替えることができます。

## <a name="service-tier-features-and-limits"></a>サービス階層の機能と制限

次の表に、Basic、Standard、および Premium サービス レベルの機能とレジストリの制限について説明します。

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="registry-throughput-and-throttling"></a>レジストリ スループットと調整

### <a name="throughput"></a>スループット 

高いレートでレジストリ操作が生成されている場合、予想される最大スループットのガイドとして、読み取りおよび書き込み操作と帯域幅に対して、サービス レベルの制限を使用します。 これらの制限は、イメージやその他の成果物の一覧表示、削除、プッシュ、プルなどのデータプレーン操作に影響します。

イメージのプルとプッシュのスループットを具体的に推定するには、レジストリの制限と次の要因を考慮します。 

* イメージ レイヤーの数とサイズ
* イメージ間でレイヤーまたは基本イメージを再利用する
* プルまたはプッシュごとに必要になる可能性がある追加の API 呼び出し

詳細については、[Docker HTTP API V2](https://docs.docker.com/registry/spec/api/) のドキュメントを参照してください。

レジストリ スループットの評価またはトラブルシューティングを行う場合は、クライアント環境の構成も考慮してください。

* 同時実行操作のための Docker デーモン構成
* レジストリのデータ エンドポイント (レジストリが [geo レプリケート](container-registry-geo-replication.md)されている場合は複数のエンドポイント) へのネットワーク接続。

レジストリへのスループットに関する問題が発生した場合は、[レジストリのパフォーマンスのトラブルシューティング](container-registry-troubleshoot-performance.md)」を参照してください。 

#### <a name="example"></a>例

1 つの 133 MB の `nginx:latest` イメージを Azure コンテナー レジストリにプッシュするには、イメージの 5 つのレイヤーに対して複数の読み取りおよび書き込み操作が必要です。 

* イメージ マニフェストがレジストリに存在する場合、それを読み取るための読み取り操作
* イメージの構成 BLOB を書き込むための書き込み操作
* イメージ マニフェストを書き込むための書き込み操作

### <a name="throttling"></a>Throttling

レジストリで、要求のレートがレジストリのサービス レベルで許可されている制限を超えたと判断されると、プルまたはプッシュ操作の調整が発生することがあります。 `Too many requests` のような HTTP 429 エラーが表示されることがあります。

読み取り操作と書き込み操作の平均レートがレジストリ制限内である場合でも、きわめて短期間でイメージのプルまたはプッシュ操作のバーストを生成すると、一時的に調整が発生する可能性があります。 コードに何らかのバックオフによる再試行ロジックを実装するか、レジストリへの要求の最大レートを引き下げる必要がある場合があります。

## <a name="show-registry-usage"></a>レジストリの使用状況を表示する

[az acr show-usage](/cli/azure/acr#az_acr_show_usage) コマンドまたは [List Usages](/rest/api/containerregistry/registries/list-usages) REST API を使用して、ご利用のレジストリにおけるストレージおよびその他のリソースの現在の使用量をそのレジストリのサービス レベルの制限と比較したスナップショットを取得します。 ストレージの使用状況は、ポータルにあるレジストリの **[概要]** ページにも表示されます。

使用状況に関する情報は、ご利用のレジストリが制限に近づいたときに[サービス レベルを変更](#changing-tiers)するかどうかを決めるのに役立ちます。 この情報はまた、[使用量を管理](container-registry-best-practices.md#manage-registry-size)するのにも役立ちます。 

> [!NOTE]
> レジストリのストレージの使用状況は、ガイドとしての使用に限定すべきであり、最近のレジストリ操作を反映していない場合があります。 レジストリの [StorageUsed](monitor-service-reference.md#container-registry-metrics) メトリックを監視すれば、最新のデータを確認できます。 

レジストリのサービス レベルに応じて、使用状況に関する情報には、次の内容の一部またはすべてと、そのサービス レベルの制限が含まれます。

* 使用されているストレージ (バイト単位) <sup>1</sup>
* [Webhook](container-registry-webhook.md) の数
* [geo レプリケーション](container-registry-geo-replication.md)の数 (ホーム レプリカを含む)
* [プライベート エンドポイント](container-registry-private-link.md)の数
* [IP アクセス規則](container-registry-access-selected-networks.md)の数
* [仮想ネットワーク規則](container-registry-vnet.md)の数

<sup>1</sup>geo レプリケートされたレジストリでは、ホーム リージョンのストレージ使用量が表示されます。 使用されたストレージの合計は、レプリケーション数をかけ算して求めます。

## <a name="changing-tiers"></a>階層の変更

レジストリのサービス階層の変更は、Azure CLI または Azure portal で行うことができます。 切り替え先の階層が最大記憶域容量の要件を満たしていれば、階層間で自由に切り替えを行うことができます。 

サービス階層間を移動しても、レジストリのダウンタイムやレジストリ操作への影響はありません。

### <a name="azure-cli"></a>Azure CLI

Azure CLI でサービス階層を切り替えるには、[az acr update][az-acr-update] コマンドを使用します。 たとえば Premium への切り替えは、次のようにして行います。

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure portal

Azure Portal のコンテナー レジストリの **[Overview]\(概要\)** で、 **[Update]\(更新\)** を選択し、[SKU] ドロップダウン リストから新しい **SKU** を選択します。

![Azure Portal でのコンテナー レジストリ SKU の更新][update-registry-sku]

## <a name="pricing"></a>価格

各 Azure Container Registry サービス階層の価格については、「[Container Registry の価格][container-registry-pricing]」を参照してください。

データ転送の価格に関する詳細については、「[帯域幅の料金詳細](https://azure.microsoft.com/pricing/details/bandwidth/)」を参照してください。 

## <a name="next-steps"></a>次のステップ

**Azure Container Registry ロードマップ**

GitHub の [ACR ロードマップ][acr-roadmap]を参照して、サービスの今後の機能に関する情報を確認してください。

**Azure Container Registry UserVoice**

[ACR UserVoice][container-registry-uservoice] で、新機能に関する提案を送信し、投票してください。

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/d365community/forum/180a533d-0d25-ec11-b6e6-000d3a4f0858

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
