---
title: Azure Web PubSub サービス エンドポイントへのネットワーク アクセスをセキュリティで保護および制御する方法
description: Azure Web PubSub サービスのネットワーク アクセスを制御する方法の概要
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: 56e467b392a40a7d9e5389aa31f41468db7730c9
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997630"
---
# <a name="configure-network-access-control-for-azure-web-pubsub-service"></a>Azure Web PubSub サービスのネットワーク アクセス制御を構成する

Azure Web PubSub サービスでは、要求の種類と使用されるネットワークのサブセットに基づいて、サービス エンドポイントへのアクセス レベルをセキュリティで保護し、制御することができます。 ネットワーク ルールが構成されている場合、指定された一連のネットワークを介してデータを要求するアプリケーションのみが、Azure Web PubSub サービスにアクセスできます。

Azure Web PubSub サービスには、インターネット経由でアクセスできるパブリック エンドポイントがあります。 [Azure Web PubSub サービスのプライベート エンドポイント](howto-secure-private-endpoints.md)を作成することもできます。 プライベート エンドポイントは、VNet のプライベート IP アドレスを Azure Web PubSub サービスに割り当て、プライベート リンクを介して VNet と Azure Web PubSub サービス間のすべてのトラフィックを保護します。 Azure Web PubSub サービスのネットワーク アクセス制御は、パブリック エンドポイントとプライベート エンドポイントの両方に対するアクセス制御を提供します。

必要に応じて、パブリック エンドポイントと各プライベート エンドポイントに対する特定の種類の要求を許可するか拒否するかを選択できます。 

ネットワーク アクセス制御ルールが有効なときに Azure Web PubSub サービスにアクセスするアプリケーションでも、要求に対する適切な認可が必要です。

## <a name="scenario-a---no-public-traffic"></a>シナリオ A - パブリック トラフィックなし

すべてのパブリック トラフィックを完全に拒否するには、まず要求の種類を許可しないようにパブリック ネットワーク ルールを構成する必要があります。 次に、特定の VNet からのトラフィックにアクセスを許可するルールを構成する必要があります。 この構成では、アプリケーションに対してセキュリティで保護されたネットワーク境界を構築することができます。

## <a name="scenario-b---only-client-connections-from-public-network"></a>シナリオ B - パブリック ネットワークからのクライアント接続のみ

このシナリオでは、パブリック ネットワークからのクライアント接続のみを許可するようにパブリック ネットワーク ルールを構成できます。 その後、特定の VNet から送信された他の種類の要求を許可するように、プライベート ネットワーク ルールを構成することができます。 この構成では、パブリック ネットワークからアプリ サーバーが非表示になり、アプリ サーバーと Azure Web PubSub サービス間にセキュリティで保護された接続が確立されます。

## <a name="managing-network-access-control"></a>ネットワーク アクセス制御の管理

Azure portal を使用して、Azure Web PubSub サービスのネットワーク アクセス制御を管理できます。

### <a name="azure-portal"></a>Azure portal

1. セキュリティで保護する Azure Web PubSub サービスに移動します。

1. 設定メニューの **[ネットワーク アクセス制御]** を選択します。

    :::image type="content" source="./media/howto-secure-network-access-control/portal-network-access-control.png" alt-text="Azure portal の [ネットワーク アクセス制御]":::

1. 既定のアクションを編集するには、 **[許可/拒否]** ボタンを切り替えます。

    > [!TIP]
    > 既定のアクションは、一致する ACL ルールがない場合に実行するアクションです。 たとえば、既定のアクションが **[拒否]** の場合、下で明示的に承認されていない種類の要求は拒否されます。

1. パブリック ネットワーク ルールを編集するには、 **[パブリック ネットワーク]** で許可されている要求の種類を選択します。

    :::image type="content" source="./media/howto-secure-network-access-control/portal-public-network.png" alt-text="Azure portal でのパブリック ネットワーク ACL の編集":::

1. プライベート エンドポイント ネットワーク ルールを編集するには、 **[プライベート エンドポイント 接続]** の下の各行で許可された種類の要求を選択します。

1. **[保存]** を選択して変更を保存します。
