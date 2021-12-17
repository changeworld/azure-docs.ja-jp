---
title: Azure Virtual Desktop のユーザー単位のアプリ ストリーミング コストを見積もる - Azure
description: Azure Virtual Desktop のユーザー単位の課金コストを見積もる方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 4d0218031a79e5271dcdff7531f856094175c477
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798928"
---
# <a name="estimate-per-user-app-streaming-costs-for-azure-virtual-desktop"></a>Azure Virtual Desktop のユーザー単位のアプリ ストリーミング コストを見積もる

Azure Virtual Desktop のユーザー単位のアクセス価格では、組織外のユーザーに Azure Virtual Desktop でホストされているアプリおよびデスクトップへのアクセスを付与できます。 Azure Virtual Desktop のユーザー単位のアクセス価格の詳細については、「[ライセンスとユーザー単位のアクセス価格について](licensing.md)」を参照してください。 この記事では、デプロイにかかる正規のユーザー アクセス コストを見積もる方法について説明します。

>[!NOTE]
>この記事の料金は、キャンペーン オファーや割引が適用されていない、正規料金のユーザー単位のサブスクリプションに基づいています。 また、Azure Virtual Desktop のデプロイには、インフラストラクチャの使用コストなどの他のコストもかかることに留意してください。 これら他のコストの詳細については、「[Azure Virtual Desktop デプロイの総コストについて](total-costs.md)」を参照してください。

## <a name="requirements"></a>要件

既存のデプロイについてユーザー単位のアクセス コストを見積もるには、次のものが必要になります。

- 前月にアクティブなユーザーが存在した Azure Virtual Desktop デプロイ。
- [Azure Virtual Desktop デプロイ用の Azure Monitor](../azure-monitor.md)

## <a name="measure-monthly-user-activity-in-a-host-pool"></a>ホスト プール内の月間ユーザー アクティビティを測定する

ホスト プールの運用にかかる総コストを見積もるには、まず、過去 1 か月間のアクティブ ユーザー数を把握する必要があります。 この数は、Azure Monitor for Azure Virtual Desktop を使用することで調べられます。

Azure Monitor で月間アクティブ ユーザーを確認するには:

1. Azure portal を開き、**Azure Virtual Desktop** を検索して選択します。 その後、 **[分析情報]** を選択し、Azure Monitor for Azure Virtual Desktop を開きます。

2. 測定するサブスクリプションまたはホスト プールの名前を選択します。

3. **[概要]** タブの **[使用率]** セクションで、 **[月間ユーザー数 (MAU)]** グラフを見つけます。

4. 直近の日付における月間アクティブ ユーザー数 (MAU) の値を確認します。 MAU は、該当日から遡って 28 日以内にこのホスト プールに接続したユーザーの数を示しています。

## <a name="estimate-per-user-access-costs-for-an-azure-virtual-desktop-host-pool"></a>Azure Virtual Desktop ホスト プールのユーザー単位のアクセス コストを見積もる

次に、請求期間の請求料金を確認します。 これは、登録済みサブスクリプションにおいて 1 つ以上のセッション ホストに接続したユーザーの人数から求められます。

さらに、価格レベルはユーザーに応じて 2 つに分かれます。

- RemoteApp アプリケーション グループのみに接続したユーザー。
- 1 つ以上のデスクトップ アプリケーション グループに接続したユーザー。

サブスクリプション内のセッション ホストに接続した、各価格レベルのユーザーの数を調べることで、総コストを見積もることができます。

各レベルのユーザー数を確認するには:

1. [Azure Virtual Desktop の価格ページ](https://azure.microsoft.com/pricing/details/virtual-desktop/)にアクセスして、ご自分のリージョンの "アプリ" および "アプリ + デスクトップ" の価格を調べます。
2. [ホスト プール内の月間アクティビティの測定](#measure-monthly-user-activity-in-a-host-pool)に関するセクションの手順 4 で調べた接続量の数字を基に、ユーザー アクセス コストの総額を計算します。
   
   ホスト プールで RemoteApp アプリケーション グループを使用している場合は、"アプリ" に表示される価格を接続量に掛ける必要があります。 つまり、次の式を使う必要があります。

   接続量 x "アプリ" のユーザー単位の価格 = 総コスト

   ホスト プールでデスクトップ アプリケーション グループを使用している場合は、以下のように、"アプリ + デスクトップ" のユーザー単位の価格を掛けます。

   接続量 x "アプリ + デスクトップ" のユーザー単位の価格 = 総コスト

>[!IMPORTANT]
>環境によっては、実際の価格は、以上の手順で求めた見積もりから大きく異なる場合があります。 たとえば、複数のホスト プールのリソースにユーザーがアクセスしているものの、請求期間ごとにユーザー 1 人につき 1 回しか課金されていない場合、見積もり額は実際のコストよりも大きくなる可能性があります。 また、データの基準にした 28 日間のユーザー アクティビティが、通常の月間ユーザー アクティビティと一致していない場合には、見積もり額は小さくなる可能性があります。 たとえば、1 週間にわたる連休や大規模なサービス停止期間が含まれる月は、ユーザー アクティビティが平均を下回るため、正確な見積もりの参考にすることはできません。

## <a name="next-steps"></a>次のステップ

Azure Virtual Desktop デプロイ全体にかかる総コストの見積もりについては、「[Azure Virtual Desktop デプロイの総コストについて](total-costs.md)」を参照してください。 ライセンス要件とコストの詳細については、「[ライセンスとユーザー単位のアクセス価格について](licensing.md)」を参照してください。
