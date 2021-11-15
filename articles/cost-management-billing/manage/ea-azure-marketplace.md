---
title: Azure Marketplace
description: EA のお客様が Azure Marketplace を使用する方法について説明します
author: bandersmsft
ms.reviewer: sapnakeshari
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: banders
ms.openlocfilehash: f22dd59be380a7535d8bdc724d48152eeec4041f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130244193"
---
# <a name="azure-marketplace"></a>Azure Marketplace

この記事では、EA のお客様とパートナーがマーケットプレースの料金を確認し、Azure Marketplace で購入したものを有効にする方法について説明します。

## <a name="azure-marketplace-for-ea-customers"></a>EA お客様向け Azure Marketplace

ダイレクトのお客様の場合、Azure Marketplace の料金は [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/BillingAccounts) に表示されます。 Azure Marketplace での購入および消費については、Azure 前払いとは別に、四半期ごとまたは月単位で未払い分が請求されます。 [Azure portal での Azure Marketplace の管理](direct-ea-administration.md#enable-azure-marketplace-purchases)に関する記事をご覧ください。

インダイレクトのお客様は、Azure エンタープライズ ポータルの **[サブスクリプションの管理]** ページで Azure Marketplace のサブスクリプションを確認できますが、価格は非表示になります。 Azure Marketplace の料金については、ライセンス ソリューション プロバイダー (LSP) にお問い合わせください。

新規の月単位または年単位の定期的な Azure Marketplace での購入については、Azure Marketplace 項目が購入された期間の全額が請求されます。 これらの項目は、次の期間の最初の購入と同じ日に自動更新されます。

既存の、毎月の定期的な料金は引き続き、各カレンダー月の最初の日に更新されます。 年間料金は、購入日の応当日に更新されます。

Azure Marketplace で利用可能な一部のサードパーティ リセラー サービスでは、エンタープライズ契約 (EA) の Azure 前払い残高が消費されるようになりました。 従来、これらのサービスは EA Azure 前払い以外で課金され、個別に請求されていました。 Azure Marketplace のこれらのサービスの EA Azure 前払いは、お客様の購入と支払管理を簡素化するのに役立ちます。 Azure 前払いが消費されるようになったサービスの完全なリストについては、[Azure Web サイト (更新日: 2018 年 3 月 6 日)](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/) を参照してください。

### <a name="partners"></a>パートナー

LSP は、Azure エンタープライズ ポータルの価格シート ページから、Azure Marketplace の価格表をダウンロードすることができます。 右上にある **[Marketplace Price list]\(Marketplace の価格表\)** リンクを選択します。 Azure Marketplace の価格表には、利用可能なすべてのサービスとその価格が表示されます。

価格表をダウンロードするには、次のようにします。

1. Azure エンタープライズ ポータルで、 **[レポート]**  >  **[価格シート]** の順に移動します。
1. 右上隅のユーザー名の下にある Azure Marketplace の価格表へのリンクを見つけます。
1. リンクを右クリックして、 **[対象をファイルに保存]** を選択します。
1. **[保存]** ウィンドウで、ドキュメントのタイトルを `AzureMarketplacePricelist.zip` に変更します。これにより、ファイルが .xlsx から .zip ファイルに変更されます。
1. ダウンロードが完了した後、国固有の価格表を含む zip ファイルを入手できます。
1. LSP は、国固有の価格について、個々の国ファイルを参照する必要があります。 LSP は **[通知]** タブを使用して、まったく新しい、または廃止された SKU を確認できます。
1. 料金が頻繁に変わることはありません。 LSP には、メールで 30 日前に値上げと外国為替 (FX) の変更が通知されます。
1. LSP は、加入契約、ISV、および四半期ごとに 1 枚の請求書を受け取ります。

### <a name="enabling-azure-marketplace-purchases"></a>Azure Marketplace での購入の有効化

エンタープライズ管理者は、その加入契約下のすべての Azure サブスクリプションに対して、Azure Marketplace での購入を無効または有効にすることができます。 エンタープライズ管理者が購入を無効にし、既に Azure Marketplace サブスクリプションが含まれている Azure サブスクリプションがある場合は、それらの Azure Marketplace サブスクリプションが取り消されたり、影響を受けたりすることはありません。

お客様は Azure エンタープライズ ポータルで加入契約に関連付けることで、ダイレクト Azure サブスクリプションを Azure EA に変換することはできますが、このアクションでは、子のサブスクリプションが自動的に変換されません。

Azure Enterprise Portal で Azure Marketplace の購入を有効にするには:

1. エンタープライズ管理者として、Azure エンタープライズ ポータルにサインインします。
1. **[管理]** に移動します。
1. **[加入契約の詳細]** で、 **[Azure Marketplace]** 品目の横にある鉛筆アイコンを選択します。
1. **有効または無効**、あるいは必要に応じて、無料の **BYOL SKU のみ** に切り替えます。
1. **[保存]** を選択します。

ダイレクトのお客様は、Azure portal での Azure Marketplace の購入を有効にすることができます。

1. Azure portal にサインインします。
1. **[コストの管理と請求]** に移動します。
1. 左側のメニューで [課金スコープ] を選択し、課金アカウントのスコープを選択します。
1. 左側のメニューで **[ポリシー]** を選択します。
1. [Azure Marketplace] で、ポリシーを **[オン]** に設定します。
1. **[保存]** を選択します。

> [!NOTE]
> BYOL (ライセンス持ち込み) および無料のみのオプションでは、Azure Marketplace SKU の購入と取得が BYOL と無料の SKU のみに制限されます。

### <a name="services-billed-hourly-for-azure-ea"></a>Azure EA に対して時間単位で課金されるサービス

次のサービスについては、MOSP での月額料金ではなく、エンタープライズ契約で 1 時間ごとに課金されます。

- Application Delivery Network
- Web アプリケーション ファイアウォール

### <a name="azure-remoteapp"></a>Azure RemoteApp

エンタープライズ契約のお客様は、エンタープライズ契約の価格レベルに基づいて、Azure RemoteApp の料金をお支払いただきます。 追加料金はかかりません。 標準価格には最初の 40 時間が含まれます。 無制限の価格では、最初の 80 時間が対象となります。 RemoteApp では、80 時間を超える使用量の生成が停止されます。

## <a name="next-steps"></a>次のステップ

- [価格](ea-pricing-overview.md)の詳細を確認します。
- Azure Marketplace サービスと Azure EA 前払いに関する質問と回答の一覧については、[Cost Management + Billing に関する FAQ](../cost-management-billing-faq.yml) を参照してください。
