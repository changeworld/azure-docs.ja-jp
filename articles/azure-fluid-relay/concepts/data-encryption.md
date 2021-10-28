---
title: Azure Fluid Relay でのデータの暗号化
description: Fluid Relay Server のデータ暗号化についての理解を深めます
author: hickeys
ms.author: hickeys
ms.date: 10/08/2021
ms.service: app-service
ms.topic: reference
ms.openlocfilehash: 8a818ea51e1d4a3f2df03ae1c4bafcf16da35b13
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261008"
---
# <a name="data-encryption-in-azure-fluid-relay"></a>Azure Fluid Relay でのデータの暗号化

> [!NOTE]
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。

Microsoft Azure Fluid Relay Server には、[Azure Kubernetes](../../aks/enable-host-encryption.md)、[Microsoft Azure Cosmos DB]/azure/cosmos-db/database-encryption-at-rest)、[Azure Blob Storage](../../storage/common/storage-service-encryption.md) の保存時の暗号化機能が利用されています。 AFRS とこれらのリソース間のサービス間通信は TLS で暗号化され、Azure 仮想ネットワーク教会で囲まれ、ネットワーク セキュリティ規則によって外部からの干渉から保護されています。

次の図は、Azure Fluid Relay Server がどのように実装され、どのようにデータ ストレージが処理されているかの概要を示しています。

:::image type="content" source="../images/data-encryption.png" alt-text="Azure Fluid Relay のデータ ストレージの図":::

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="how-much-more-does-azure-fluid-relay-server-cost-if-encryption-is-enabled"></a>暗号化を有効にした場合、Azure Fluid Relay Server のコストはどのくらい増えますか?

保存時の暗号化は既定で有効になっています。 追加コストはかかりません。

### <a name="who-manages-the-encryption-keys"></a>だれが暗号化キーを管理するのですか?

キーは Microsoft によって管理されます。

### <a name="how-often-are-encryption-keys-rotated"></a>暗号化キーが交換される頻度はどの程度ですか?

Microsoft には、暗号化キーのローテーションに関する一連の内部ガイドラインがあり、Azure Fluid Relay Server はそれに従っています。 個々のガイドラインは公開されていません。 Microsoft が公開している[セキュリティ開発ライフ サイクル (SDL)](https://www.microsoft.com/sdl/default.aspx) は、内部用ガイダンスのサブセットと見なされ、開発者に便利なベスト プラクティスが収められています。

### <a name="can-i-use-my-own-encryption-keys"></a>独自の暗号化キーを使用できますか?

いいえ、この機能はまだ使用できません。 今後の更新情報にご注目ください。 

### <a name="what-regions-have-encryption-turned-on"></a>どのリージョンで暗号化が有効になっていますか?

すべての Azure Fluid Relay Server リージョンで、すべてのユーザー データの暗号化が有効になっています。

### <a name="does-encryption-affect-the-performance-latency-and-throughput-slas"></a>暗号化はパフォーマンス (待機時間およびスループット) の SLA に影響しますか?

A: 保存時の暗号化を有効にしても、パフォーマンス SLA への影響や変化はありません。

## <a name="see-also"></a>関連項目

- [Azure Fluid Relay アーキテクチャの概要](architecture.md)
- [Azure Fluid Relay トークン コントラクト](../how-tos/fluid-json-web-token.md)
- [アプリの認証と承認](authentication-authorization.md)