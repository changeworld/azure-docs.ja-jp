---
title: Azure Cosmos DB アカウントでプロビジョニングされる合計スループットを制限する
description: Azure Cosmos DB アカウントでプロビジョニングされる合計スループットを制限する方法を説明します
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/04/2021
ms.author: thweiss
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1423d4444c7ec53e40f77d951acb58a63538cfbf
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557796"
---
# <a name="limit-the-total-throughput-provisioned-on-your-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントでプロビジョニングされる合計スループットを制限する
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

[プロビジョニング スループット](./set-throughput.md) モードで Azure Cosmos DB アカウントを使うと、通常、コストの大部分はアカウント全体でプロビジョニングしたスループットの量から発生します。 具体的には、これらのコストは次のものから直接影響を受けます。

- 共有スループットのあるデータベースの数。
- 専用スループットのあるコンテナーの数。
- これらの各リソースに対してプロビジョニングされたスループットの量。
- アカウントが利用可能なリージョンの数。

アカウント全体でプロビジョニングしたスループットの合計量を追跡することは困難な場合があります。Azure Cosmos DB を使い始めるときは特にそうです。 その結果、超えるとは思わなかった特定の予算をこの金額が超えて、予期しない料金が発生する可能性があります。 コストをより適切に管理できるように、Azure Cosmos DB では、アカウントでプロビジョニングされるスループットの合計を制限できます。

> [!NOTE]
> この機能は、[サーバーレス](./serverless.md) アカウントでは使えません。

アカウントの合計スループットに制限を設けると、以下の操作を実行するとこの制限を超える場合はすべてブロックされ、明示的に失敗します。

- 共有スループットを使う新しいデータベースの作成。
- 専用スループットを使う新しいコンテナーの作成。
- 標準 (手動) モードで構成されたリソースでのプロビジョニング スループットの増量。
- 自動スケーリング モードで構成されたリソースでの最大プロビジョニング スループットの増量。
- アカウントへの新しいリージョンの追加。

> [!NOTE]
> 自動スケーリング モードで構成されたリソースについては、アカウントの合計スループットに対してカウントされるリソースで構成される最大スループットです。

> [!IMPORTANT]
> アカウントで合計スループット制限が有効になると、新しいコンテナーを作成するときに明示的にスループット値を渡す必要があります。 明示的なスループット値を指定せずにコンテナーを作成しようとすると、現在エラーが発生します。

## <a name="set-the-total-throughput-limit-from-the-azure-portal"></a>Azure portal で合計スループット制限を設定する

### <a name="new-account"></a>新しいアカウント

ポータルで新しい Azure Cosmos DB アカウントを作成するときに、アカウントの合計スループットを制限するオプションがあります。

:::image type="content" source="./media/limit-total-account-throughput/create-account.png" alt-text="新しいアカウントを作成するときにアカウントの合計スループットを制限する方法を示す Azure portal のスクリーンショット" border="true":::

このオプションをオンにすると、アカウントの合計スループットが 4,000 RU/秒に制限されます。 この値は、アカウントの作成後に変更できます。

### <a name="existing-account"></a>既存のアカウント

Azure portal で Azure Cosmos DB アカウントに移動し、左側のメニューから **[コスト管理]** を選びます。

:::image type="content" source="./media/limit-total-account-throughput/existing-account.png" alt-text="既存のアカウントの合計アカウント スループットを更新する方法を示す Azure portal のスクリーンショット" border="true":::

このセクションでは、アカウントでプロビジョニングされている合計スループットの概要が示され、合計スループット制限を構成できます。 次の 3 つのオプションを使用できます。

- **[Limit the account's total provisioned throughput to the amount included in the free tier discount]\(アカウントの合計プロビジョニング スループットを、Free レベルの割引に含まれる量に制限する\)** 。 このオプションは Free レベルのアカウントでのみ使用でき、アカウントの合計スループットは 1,000 RU/秒に制限されます。 このオプションをオンにすると、プロビジョニング スループットに対する課金は発生しません。
- **[Allow the account's total throughput to be provisioned up to a custom amount]\(アカウントの合計スループットをカスタム量までプロビジョニングできるようにする\)** 。 このオプションを使うと、超えてはならない合計プロビジョニング スループットを入力できます。 参考として、入力に対応する月額料金の見積もりが表示されます。
  > [!NOTE]
  > このカスタム制限は、アカウント全体で現在プロビジョニングされている合計スループットより少なくすることはできません。
- **[No limit, allow the account's total throughput to be provisioned to any amount]\(無制限。アカウントの合計スループットを任意の量にプロビジョニングできるようにする\)** 。 このオプションは、制限を無効にします。

## <a name="set-the-total-throughput-limit-programmatically"></a>プログラムで合計スループット制限を設定する

### <a name="using-azure-resource-manager-templates"></a>Azure リソース マネージャーのテンプレートを作成する

Azure Resource Manager で Azure Cosmos DB アカウントを作成または更新するときに、`properties.capacity.totalThroughputLimit` プロパティを設定して、合計スループット制限を構成できます。

```json
{
  "location": "West US",
  "kind": "DocumentDB",
  "properties": {
    "locations": [
      {
        "locationName": "West US",
        "failoverPriority": 0,
        "isZoneRedundant": false
      }
    ],
    "databaseAccountOfferType": "Standard",
    "capacity": {
        "totalThroughputLimit": 2000
    }
  }
}
```

制限を無効にするには、このプロパティを `-1` に設定します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

#### <a name="are-there-situations-where-the-total-provisioned-throughput-can-exceed-the-limit"></a>プロビジョニングされたスループットの合計が、制限を超え得るような状況はありますか。

Azure Cosmos DB では、格納データ 1 GB あたり 10 RU/秒の最小スループットが適用されます。 既にその最小値に達しているときにデータを取り込んでいる場合、リソースでプロビジョニングされるスループットは、GB あたり 10 RU/秒を優先するように自動的に増加されます。 この場合に、またこの場合にのみ、プロビジョニングされるスループットの合計が、設定した制限を超える可能性があります。

## <a name="next-steps"></a>次のステップ

- Azure Cosmos DB での[コストの計画と管理](./plan-manage-costs.md)を始める。
- [プロビジョニング スループット](./set-throughput.md)の詳細を確認する。
- [プロビジョニング スループットのコストを最適化する](./optimize-cost-throughput.md)方法を調べる。
