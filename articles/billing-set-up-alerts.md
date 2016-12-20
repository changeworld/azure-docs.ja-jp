---
title: "Microsoft Azure サブスクリプションの課金アラートの設定 | Microsoft Docs"
description: "予想外の課金を回避するために Azure の請求に関する通知を設定する方法について説明します。"
services: 
documentationcenter: 
author: vikdesai
manager: mbaldwin
editor: 
tags: billing
ms.assetid: 9b7b3eeb-cd9d-4690-86a3-51b1e2a8974f
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2016
ms.author: vikdesai
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 76e72abdb4876af7a3ca9be082ff125a2c70c6c7


---
# <a name="set-up-billing-alerts-for-your-microsoft-azure-subscriptions"></a>Microsoft Azure サブスクリプションの課金アラートの設定
Azure サブスクリプションにかかる月額料金に懸念がありますか。 Azure サブスクリプションのアカウント管理者の場合、Azure 課金アラート サービスを使用して、Azure アカウントの課金アクティビティを監視、管理するのに役立つ、カスタマイズされた課金アラートを作成できます。

このサービスはプレビュー サービスのため、最初にサインアップする必要があります。 この機能を有効にするため、Azure アカウント管理ポータルの [[プレビュー機能]](https://account.windowsazure.com/PreviewFeatures) ページにアクセスします。

## <a name="set-the-alert-threshold-and-email-recipients"></a>アラートのしきい値と電子メールの受信者を設定する
サブスクリプションで課金サービスが有効になったという確認メールを受信したら、アカウント ポータルの [[サブスクリプション] ページ](https://account.windowsazure.com/Subscriptions) を開きます。 監視するサブスクリプションをクリックし、 **[アラート]**をクリックします。

![][Image1]

次に、**[アラートの追加]** をクリックして、最初のアラートを作成します。サブスクリプションあたり、合計 5 つの課金アラートを設定できます。それぞれのアラートに対して、別のしきい値と最大 2 名までの電子メール受信者を設定できます。

![][Image2]

アラートを追加するとき、一意の名前を付けて、使用金額のしきい値を選択して、アラートの送信先の電子メール アドレスを選択します。 しきい値を設定するとき、**[アラート対象]** リストから **[請求合計]** または **[通貨クレジット]** のいずれかを選択できます。 請求合計の場合は、サブスクリプションの使用金額がしきい値を超えるとアラートが送信されます。 通貨クレジットの場合は、通貨クレジットが制限を下回るとアラートが送信されます。 通常、通貨クレジットは、無料試用版と MSDN アカウントに関連付けられているサブスクリプションに適用されます。

![][Image3]

Azure は、任意の電子メール アドレスをサポートしていますが、電子メール アドレスが有効であるかは確認されないため、入力ミスがないようダブルチェックしてください。

## <a name="check-on-your-alerts"></a>アラートを確認する
アラートを設定した後、アカウント センターで一覧表示され、設定可能な残数も表示されます。 それぞれのアラートに対し、送信された日付と時刻、アラートが請求合計または通貨クレジットであるか、設定した制限が表示されます。 日付と時刻形式は 24 時間協定世界時 (UTC) で、日付は yyyy-mm-dd の形式です。 アラートを編集するには、リストのアラートのプラス記号をクリックします。アラートを削除するには、ごみ箱アイコンをクリックします。

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png



<!--HONumber=Nov16_HO3-->


