---
title: "別のプランへの Azure サブスクリプションの切り替え | Microsoft Docs"
description: "サブスクリプション管理ポータルを使用して Azure サブスクリプションを変更し、別のプランに切り替える方法について説明します。"
services: 
documentationcenter: 
author: genlin
manager: mbaldwin
editor: 
tags: billing,top-support-issue
ms.assetid: aae227b3-6d64-4550-a5b6-d359f53f0a59
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c7fdfb6e915269d171f1a9fa1a397faefd8b8a99


---
# <a name="switch-your-azure-subscription-to-another-offer"></a>別のプランへの Azure サブスクリプションの切り替え
[従量課金](https://azure.microsoft.com/offers/ms-azr-0003p/)のお客様は、[アカウント センター](https://account.windowsazure.com/Subscriptions)で Azure サブスクリプションを別のプランに切り替えることができます。 たとえば、この機能を使えば、[Visual Studio サブスクライバー向けの月単位のクレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効活用できます。 [無料試用版](https://azure.microsoft.com/free/)をご利用の場合は、[従量課金制にアップグレードする](billing-upgrade-azure-subscription.md)方法を確認してください。

#### <a name="whats-supported"></a>サポート対象:
| ファイル | To |
| --- | --- |
| [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) |[開発テスト用の従量課金制プラン](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) |[MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [!NOTE]
> その他のプランの変更については、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
> 
> 

## <a name="switch-subscription-offer"></a>サブスクリプション プランの切り替え
> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
> 
> 

1. [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインします。
2. 自分の従量課金のサブスクリプションを選択します。
3. **[別のプランに切り替える]**をクリックします。 このボタンは、従量課金制を利用しており、最初の請求期間が終了している場合にのみクリックできます。
   
   ![ページの右側にあるプランの切り替えボタンに注目](./media/billing-how-to-switch-azure-offer/switchbutton.png)
4. 現在のサブスクリプションから移行可能なプランの一覧から、**目的のプランを選択します**。 この一覧の内容はアカウントが関連付けられているメンバーシップに基づいて決まります。 何も表示されない場合は、[移行可能なプランの一覧](#whats-supported)をチェックして、適切なメンバーシップを持っていることを確認してください。 
   
   ![切り替え先のプランの選択](./media/billing-how-to-switch-azure-offer/selectoffer.png)
5. 切り替え先のプランによっては、切り替えによって生じる影響について注意が表示される場合があります。 先に進む前に、その内容をよく確認し、記載されている指示に従ってください。
   
   ![注意事項の確認](./media/billing-how-to-switch-azure-offer/thingstonote.png)
6. サブスクリプションの名前を変更できます。 既定では、サブスクリプションは新しいプラン名に設定されます。 **[プランの切り替え]** をクリックしてプロセスを完了します。
   
   ![緑色のボタンをクリック](./media/billing-how-to-switch-azure-offer/confirmpage.png)
7. 成功です。 これでサブスクリプションは新しいプランに切り替わりました。

## <a name="why-cant-i-switch-offers"></a>プランを切り替えられないのですが、なぜでしょうか?
次の場合、 **[別のプランに切り替える]** は表示されません。

* [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)を利用していない。 現時点では、別のプランに切り替えられるのは従量課金のサブスクリプションのみです。
  
  * [無料試用版](https://azure.microsoft.com/free/)をご利用の場合は、[従量課金制にアップグレードする](billing-upgrade-azure-subscription.md)方法を確認してください。
  * 別のサブスクリプションからプランを切り替えるには、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
* 最初の請求期間中である場合。プランを切り替えるには、最初の請求期間が終わるまで待つ必要があります。

次の場合、**"現在、お客様がお住まいのリージョンまたは国で利用できるプランはありません。"** と表示される可能性があります。

* プラン切り替えの対象ではない場合。 [切り替え可能なプランの一覧](#whats-supported)をご覧ください。

## <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Azure のプランを切り替えると、サービスと請求はどうなりますか?
以下で、アカウント センターで Azure のプランを切り替えるとどうなるかを詳しく説明します。

### <a name="access-to-services"></a>サービスへのアクセス
サブスクリプションに関連付けられているユーザーにサービスのダウンタイムは生じません。 ただし、切り替え先のプランによっては、制限がある場合があります。 たとえば、一部のプランでは運用環境での使用が禁止されているため、該当する場合は、運用環境のリソースを別のサブスクリプションに移動する必要があります。

### <a name="billing"></a>課金
切り替えを行った日に、すべての未処理料金の請求書が生成されます。 その後、サブスクリプションは新しいプランの価格条件に従って課金されます。 サブスクリプションの請求日はプランを変更した日付に変更されます。 プラン変更前の使用状況と課金データは保存されないので、切り替え前にコピーをダウンロードしておくことをお勧めします。

> [!NOTE]
> 請求関連の制約により、サブスクリプションの作成後、最初の請求サイクル内にプランを切り替えることはできません。
> 
> 

## <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>従量課金制から [Cloud Solution Provider](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) または [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA) に移行することはできますか?
現時点では、アカウント センターでプランを CSP または EA に切り替えることはできません。 既存のサブスクリプションを EA に移行するには、登録管理者に依頼してアカウントを EA に追加してもらう必要があります。 そうすると、招待メールが届きます。 手順に従って招待を受け入れると、サブスクリプションはエンタープライズ契約に自動的に移行されます。 CSP に移行するには、「[Azure Subscription Migration to CSP (CSP への Azure サブスクリプションの移行)](https://blogs.technet.microsoft.com/hybridcloudbp/2016/08/26/azure-subscription-migration-to-csp/)」を参照してください。

## <a name="next-steps"></a>次のステップ
* サブスクリプションの [管理者ロールを管理する](billing-add-change-azure-subscription-administrator.md) 方法を確認する
*  [使用状況データと請求書をダウンロード](billing-download-azure-invoice-daily-usage-date.md)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。




<!--HONumber=Nov16_HO3-->


