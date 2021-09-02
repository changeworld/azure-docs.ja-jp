---
title: Registry サービス階層と機能
description: Azure Container Registry の Basic、Standard、および Premium サービス階層 (SKU) の機能と制限 (クォータ) について説明します。
ms.topic: article
ms.date: 06/24/2021
ms.openlocfilehash: 8c27426cae6d80e31aef3d7ef9b75d28a14bd923
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2021
ms.locfileid: "113437542"
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
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
