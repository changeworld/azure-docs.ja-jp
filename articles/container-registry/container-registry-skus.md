---
title: Azure Container Registry SKU
description: "Azure Container Registry で使用可能なさまざまなサービス階層間の比較"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: mmacy
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: stevelas
ms.openlocfilehash: dae97084bdaab77efd38169cdf7e70c827b0b5ab
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2017
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKU

Azure Container Registry (ACR) は、SKU と呼ばれる複数のサービス階層で使用できます。 これらの SKU は、予測可能な価格と、Azure でプライベート Docker レジストリを使用する方法についてのいくつかのオプションを提供します。 上位の SKU を選択するほど、パフォーマンスとスケールが向上します。 ただし、すべての SKU が同じプログラム機能を提供するので、開発者は Basic から開始して、レジストリの使用量の増加に合わせて、Standard や Premium に変換できます。

## <a name="basic"></a>基本
Azure Container Registry について学習する開発者向けのコスト最適化エントリ ポイント。 Basic レジストリには Standard および Premium と同じプログラム機能があります (Azure Active Directory 認証の統合、イメージ削除、および Web フック) が、サイズと使用の制約があります。

## <a name="standard"></a>標準
Standard レジストリは、Basic と同じ機能を提供しますが、ストレージ制限とイメージ スループットが拡大されています。 Standard レジストリは、ほとんどの運用シナリオのニーズを満たすはずです。

## <a name="premium"></a>プレミアム
Premium レジストリは、ストレージや同時実行操作などの制約の上限を高め、大容量シナリオを可能にします。 イメージ スループット容量の増加に加えて、Premium では、[geo レプリケーション](container-registry-geo-replication.md)のような機能が追加され、複数のリージョン間で 1 つのレジストリが管理され、各デプロイにネットワーク上の近いレジストリが確保されます。

## <a name="classic"></a>クラシック
クラシック レジストリ SKU により、Azure での Azure Container Registry サービスの最初のリリースが可能になりました。 クラシック レジストリは、Azure がサブスクリプションに作成するストレージ アカウントによってサポートされ、スループットの向上や geo レプリケーションなどの高いレベルの機能を提供する ACR の機能を制限します。 この機能制限のため、クラシック SKU は将来非推奨にする予定です。

> [!NOTE]
> クラシック レジストリ SKU の計画的非推奨のため、すべての新しいレジストリに Basic、Standard、または Premium を使用することをお勧めします。 既存のクラシック レジストリの変換については、「[SKU の変更](#changing-skus)」を参照してください。
>

## <a name="registry-sku-feature-matrix"></a>レジストリ SKU 機能マトリックス

次の表に、Basic、Standard、および Premium サービス階層の機能と制限について説明します。

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="manage-registry-size"></a>レジストリ サイズの管理
各 SKU のストレージ制約は、一般的なシナリオに一致するように意図されています。開始用の Basic、大半の運用アプリケーションに対応する Standard、ハイパースケール パフォーマンスと[geo レプリケーション](container-registry-geo-replication.md)に対応する Premium があります。 レジストリの有効期間を通して、使用されていないコンテンツを定期的に削除することによって、そのサイズを管理する必要があります。

Azure Portal のコンテナー レジストリの **[Overview] (概要)** で、レジストリの現在の使用状況を見つけることができます。

![Azure Portal でのレジストリ使用状況情報](media/container-registry-skus/registry-overview-quotas.png)

Azure Portal でリポジトリを削除することによって、レジストリのサイズを管理できます。

**[SERVICES] (サービス)** で、**[Repositories] (リポジトリ)** を選択し、削除するリポジトリを右クリックして、**[Delete] (削除)** を選択します。

![Azure Portal でのリポジトリの削除](media/container-registry-skus/delete-repository-portal.png)

## <a name="changing-skus"></a>SKU の変更

Azure Portal で、レジストリの SKU を変更できます。

Azure Portal のレジストリの **[Overview] (概要)** で、**[Update] (更新)** を選択し、[SKU] ドロップダウン リストから新しい **SKU** を選択します。

![Azure Portal でのコンテナー レジストリ SKU の更新](media/container-registry-skus/update-registry-sku.png)

## <a name="changing-from-classic"></a>クラシックからの変更
クラシック レジストリを Basic、Standard、または Premium に変更すると、Azure が、サブスクリプション内の関連付けられたストレージ アカウントから、既存のコンテナー イメージを、Azure によって管理されているストレージ アカウントにコピーします。 このプロセスにはしばらく時間がかかることがあります。

変換中に、`docker pull` は機能し続けますが、変換が完了するまで、`docker push` はブロックされます。

完了すると、サブスクリプション ストレージ アカウントは ACR で使用されなくなります。

### <a name="why-change-from-classic-to-basic-standard-or-premium"></a>クラシックから Basic、Standard、または Premium に変更する理由

クラシック レジストリの制限された機能のため、クラシック レジストリを Basic、Standard、または Premium 階層に変更することをお勧めします。 高いレベルの SKU ほど、レジストリが Azure の機能に深く統合されます。 これらには、次のような機能があります。

* 個人の認証用の Azure Active Directory 統合 ([az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login) を参照してください)
* イメージとタグの削除のサポート
* [geo レプリケーション](container-registry-geo-replication.md)
* [Webhook](container-registry-webhook.md)

中でも、クラシック レジストリは、レジストリの作成時に、Azure によって Azure サブスクリプションに自動的にプロビジョニングされたストレージ アカウントに依存しています。 これに対し、Basic、Standard、および Premium SKU では、*管理対象ストレージ*を利用します。 つまり、Azure は自動的にイメージのストレージを透過的に管理するため、独自のサブスクリプションに個別のストレージ アカウントが作成されません。

Basic、Standard、および Premium レジストリによって提供される管理対象ストレージには、次のような利点があります。

* コンテナー イメージは[保存時に暗号化](../storage/common/storage-service-encryption.md)されます。
* イメージは、[geo 冗長ストレージ](../storage/common/storage-redundancy.md#geo-redundant-storage)を使用して格納され、複数リージョンのレプリケーションによって、イメージのバックアップが確保されます。
* [SKU 間の移動](#changing-skus)機能によって、上位の SKU を選択するほど、より高いスループットが可能になります。 各 SKU では、ニーズの拡大に合わせて、ACR がスループット要件を満たすことができます。 ACR が目的のスループットを実現する方法の基盤の実装は、*インテント*として表され (高い SKU を選択することによって)、ユーザーが実装の詳細を管理する必要がありません。

## <a name="pricing"></a>価格

各 Azure Container Registry SKU の価格については、「[Container Registry の価格](https://azure.microsoft.com/pricing/details/container-registry/)」を参照してください。

## <a name="next-steps"></a>次のステップ

**Azure Container Registry ロードマップ**

GitHub の [ACR ロードマップ](https://aka.ms/acr/roadmap)を参照して、サービスの今後の機能に関する情報を確認してください。

**Azure Container Registry UserVoice**

[ACR UserVoice](https://feedback.azure.com/forums/903958-azure-container-registry) で、新機能に関する提案を送信し、投票してください。