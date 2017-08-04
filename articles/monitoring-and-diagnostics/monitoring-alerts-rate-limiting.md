---
title: "SMS、電子メール、Webhook のレート制限 | Microsoft Docs"
description: "アクティビティ ログで特定のイベントが発生した場合に、SMS、webhook、および電子メールで通知を受け取ります。"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: b2e954405500921c0c1e9c7cd71ce57130c98d64
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="rate-limiting-for-sms-emails-and-webhooks"></a>SMS、電子メール、Webhook のレート制限
レート制限とは、特定の電話番号または電子メールへの送信情報が多すぎる場合に発生する通知の中断です。 レート制限では、アラートを管理可能およびアクション可能にすることができます。

SMS と電子メールのルールは同じです。 レート制限のしきい値は次のとおりです。
 - SMS - 1 時間に 10 件のメッセージ
 - Email - 1 時間に 100 件のメッセージ

## <a name="rate-limit-rules"></a>レート制限のルール
- 特定の電話番号または電子メールでしきい値を超える電話またはメールを受信した場合に、レート制限が適用されます。
- 電話番号または電子メールは、多数のサブスクリプション間のアクション グループの一部です。 レート制限はすべてのサブスクリプションに対して適用されます。つまり、複数のサブスクリプションから送信された場合であっても、しきい値に達するとただちに制限が適用されます。  
- 1 つの電話番号または電子メールにレート制限が適用されると、通知を追加で送信してレート制限が適用されたことを通知します。 レート制限が期限切れになった場合は、通知で示されます。

## <a name="rate-limit-of-webhooks"></a>Webhook のレート制限 ##
現在 webhook 用のレート制限はありません。

## <a name="next-steps"></a>次のステップ ##
詳細については、「[SMS アラート動作](monitoring-sms-alert-behavior.md)」を参照してください  
[アクティビティ ログ アラートの概要](monitoring-overview-alerts.md)を把握し、アラートを生成する方法について学習してください  
[サービスの正常性通知が投稿されるたびにアラートを設定](monitoring-activity-log-alerts-on-service-notifications.md)する方法

