---
title: "SMS、電子メール、Webhook のレート制限 | Microsoft Docs"
description: "アクティビティ ログで特定のイベントが発生した場合に、SMS、webhook、および電子メールで通知を受け取ります。"
author: anirudhcavale
manager: carmonm
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
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 93f8d0f32a851d530df84186d419bd8e2d47f1bc
ms.lasthandoff: 03/31/2017


---

# <a name="rate-limiting-for-sms-emails-and-webhooks"></a>SMS、電子メール、Webhook のレート制限
レート制限とは、特定の電話番号または電子メールへの送信情報が多すぎる場合に発生する通知の中断です。 制限により、アクティビティ ログのアラートやサービス正常性のアラートに関する情報伝達が管理および実施可能になります。

SMS と電子メールのルールは同じです。 レート制限のしきい値は次のとおりです。
 - SMS - 1 時間に 10 件のメッセージ
 - Email - 1 時間に 100 件のメッセージ

## <a name="rate-limit-rules"></a>レート制限のルール
- 特定の電話番号または電子メールでしきい値を超える電話またはメールを受信した場合に、レート制限が適用されます。
- 電話番号または電子メールは、多数のサブスクリプション間のアクション グループの一部です。 レート制限はすべてのサブスクリプションに対して適用されます。つまり、複数のサブスクリプションから送信された場合であっても、しきい値に達するとただちに制限が適用されます。  
- 1 つの電話番号または電子メールにレート制限が適用されると、同じ種類のメッセージを追加で送信してレート制限が適用されたことを通知します。 レート制限が期限切れになった場合は、SMS や電子メールで示されます。

## <a name="rate-limit-of-webhooks"></a>Webhook のレート制限 ##
現在 webhook 用のレート制限はありません。

## <a name="next-steps"></a>次のステップ ##
詳細については、「[SMS アラート動作](monitoring-sms-alert-behavior.md)」を参照してください  
[アクティビティ ログ アラートの概要](monitoring-overview-alerts.md)を把握し、アラートを生成する方法について学習してください  
[サービスの正常性通知が投稿されるたびにアラートを設定](monitoring-activity-log-alerts-on-service-notifications.md)する方法

