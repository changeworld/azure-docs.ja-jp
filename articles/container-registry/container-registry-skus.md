---
title: Azure Container Registry SKU
description: Azure Container Registry で使用可能なさまざまなサービス階層の比較
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/15/2018
ms.author: marsma
ms.openlocfilehash: 5d9001bce4f835e4b9b82ba1c30d09f74eebd1d2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39442753"
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKU

Azure Container Registry (ACR) は、SKU と呼ばれる複数のサービス階層で使用できます。 これらの SKU は、Azure におけるプライベート Docker レジストリの容量と使用パターンに合ったさまざまなオプションと予測可能な価格を提供します。

| SKU | 管理者常駐型 | 説明 |
| --- | :-------: | ----------- |
| **Basic** | [はい] | Azure Container Registry について学習する開発者向けのコスト最適化エントリ ポイント。 Basic レジストリには Standard および Premium と同じプログラム機能があります (Azure Active Directory 認証の統合、イメージ削除、および Web フック) が、サイズと使用の制約があります。 |
| **Standard** | [はい] | Standard レジストリは、Basic と同じ機能を提供しますが、ストレージ制限とイメージ スループットが拡大されています。 Standard レジストリは、ほとんどの運用シナリオのニーズを満たすはずです。 |
| **Premium** | [はい] | Premium レジストリは、ストレージや同時実行操作などの制約の上限を高め、大容量シナリオを可能にします。 イメージ スループット容量の増加に加えて、Premium では、[geo レプリケーション][container-registry-geo-replication]のような機能が追加され、複数のリージョン間で 1 つのレジストリが管理され、各デプロイにネットワーク上の近いレジストリが確保されます。 |
| クラシック | いいえ  | クラシック レジストリ SKU により、Azure での Azure Container Registry サービスの最初のリリースが可能になりました。 クラシック レジストリは、Azure がサブスクリプションに作成するストレージ アカウントによってサポートされ、スループットの向上や geo レプリケーションなどの高いレベルの機能を提供する ACR の機能を制限します。 この機能制限のため、クラシック SKU は将来非推奨にする予定です。 |

選択する SKU レベルが高いほど、パフォーマンスは高く、スケールも大きくなりますが、プログラムの機能はすべての管理対象 SKU で共通です。 複数のサービス レベルがあることで、最初は Basic を導入し、その後、レジストリの使用量の増加に伴って Standard や Premium に切り替えることができます。

> [!NOTE]
> クラシック レジストリ SKU の計画的非推奨のため、すべての新しいレジストリに Basic、Standard、または Premium を使用することをお勧めします。 既存のクラシック レジストリの変換については、[クラシック レジストリのアップグレード][container-registry-upgrade]に関するページを参照してください。
>

## <a name="managed-vs-unmanaged"></a>管理対象と管理対象外

Basic、Standard、Premium の各 SKU はまとめて "*管理対象*" のレジストリと呼ばれます。一方、クラシック SKU は "*管理対象外*" のレジストリと呼ばれます。 両者の主な違いは、コンテナー イメージの保存方法にあります。

### <a name="managed-basic-standard-premium"></a>管理対象 (Basic、Standard、Premium)

管理対象レジストリは、Azure によって完全に管理されているイメージ ストレージから恩恵を受けられます。 つまりイメージの格納先となるストレージ アカウントは、ユーザーの Azure サブスクリプションには表示されません。 管理対象レジストリに該当するいずれかの SKU を使用することで、いくつかの利点が得られます。それらの利点については、「[Azure Container Registry へのコンテナー イメージの保存][container-registry-storage]」で詳しく説明します。 この記事では、管理対象レジストリの SKU とその機能について詳しく取り上げます。

### <a name="unmanaged-classic"></a>管理対象外 (クラシック)

クラシック レジストリが "管理対象外" と呼ばれるのは、その背後にあるストレージ アカウントが "*ユーザーの*" Azure サブスクリプション内に存在するためです。 そのためコンテナー イメージの格納先となるストレージ アカウントは、ユーザーが自ら管理する必要があります。 管理対象外レジストリでは、実際のニーズに変化が生じても (管理対象レジストリに[アップグレード][container-registry-upgrade]する以外) SKU を切り替えることができず、また、管理対象レジストリのいくつかの機能が利用できません (コンテナー イメージの削除、[geo レプリケーション][container-registry-geo-replication]、[webhook][container-registry-webhook] など)。

クラシック レジストリをいずれかの管理対象 SKU にアップグレードする方法について詳しくは、[クラシック レジストリのアップグレード][container-registry-upgrade]に関するページをご覧ください。

## <a name="sku-feature-matrix"></a>SKU 機能マトリックス

次の表に、Basic、Standard、および Premium サービス階層の機能と制限について説明します。

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>SKU の変更

レジストリの SKU 変更は、Azure CLI または Azure Portal で行うことができます。 変更後の SKU が最大記憶域容量の要件を満たしていれば、管理対象 SKU は自由に移動することができます。 いずれかの管理対象 SKU にクラシックから切り替えた場合は、二度とクラシックに戻すことはできません。あくまで一方向の変換となります。

### <a name="azure-cli"></a>Azure CLI

Azure CLI で SKU を切り替えるには、[az acr update][az-acr-update] コマンドを使用します。 たとえば Premium への切り替えは、次のようにして行います。

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure ポータル

Azure Portal のコンテナー レジストリの **[Overview]\(概要\)** で、**[Update]\(更新\)** を選択し、[SKU] ドロップダウン リストから新しい **SKU** を選択します。

![Azure Portal でのコンテナー レジストリ SKU の更新][update-registry-sku]

ご利用のレジストリがクラシック レジストリである場合、管理対象 SKU を Azure Portal 内で選択することはできません。 あらかじめ管理対象レジストリに[アップグレード][container-registry-upgrade]しておく必要があります (「[クラシックからの変更](#changing-from-classic)」を参照)。

## <a name="changing-from-classic"></a>クラシックからの変更

管理対象外のクラシック レジストリを管理対象の SKU (Basic、Standard、Premium のいずれか) に移行する際は、別途注意しなければならない点があります。 クラシック レジストリが大量のイメージを格納していて、何ギガバイトものサイズになると、移行プロセスにしばらく時間がかかる場合があります。 また、移行が完了するまでは、`docker push` の操作が無効になります。

クラシック レジストリをいずれかの管理対象 SKU にアップグレードする方法について詳しくは、「[Upgrade a Classic container registry (クラシック コンテナー レジストリのアップグレード)][container-registry-upgrade]」をご覧ください。

## <a name="pricing"></a>価格

各 Azure Container Registry SKU の価格については、「[Container Registry の価格][container-registry-pricing]」を参照してください。

## <a name="next-steps"></a>次の手順

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
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-webhook]: container-registry-webhook.md
