---
title: "SMS、電子メール、Azure アプリ プッシュ通知、webhook のレート制限 | Microsoft Docs"
description: "アクション グループから送信される可能性がある SMS、電子メール、Azure アプリ プッシュ通知、または webhook 通知の数を Azure で制限する方法を説明します。"
author: dukek
manager: chrad
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/8/2017
ms.author: dukek
ms.openlocfilehash: c76bf5cf51f18a32b33060d528c64d119e31dbbd
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2017
---
# <a name="rate-limiting-for-sms-messages-emails-azure-app-push-notifications-and-webhook-posts"></a>SMS メッセージ、電子メール、Azure アプリ プッシュ通知、webhook 投稿のレート制限
レート制限とは、特定の電話番号、電子メール アドレス、またはデバイスに送信される通知が多すぎる場合に、通知が一時的に停止されることです。 レート制限によって、アラートを管理しやすくなりアクション可能な状態が保証されます。

レート制限のしきい値は次のとおりです。

 - **SMS**: 5 分ごとに 1 件以下の SMS。
 - **電子メール**: 1 時間に 100 件のメッセージ。
 - **Azure アプリ プッシュ通知**: プッシュ通知のレート制限はありません。
 - **webhook**: webhook 用のレート制限はありません。

## <a name="rate-limit-rules"></a>レート制限のルール
- 特定の電話番号または電子メールでしきい値で許可された数を超えるメッセージを受信した場合に、レート制限が適用されます。
- 電話番号または電子メールは、多数のサブスクリプション間のアクション グループの一部です。 レート制限はすべてのサブスクリプションに対して適用されます。 メッセージが複数のサブスクリプションから送信された場合であっても、しきい値に達するとただちに制限が適用されます。  
- 電子メール アドレスにレート制限が適用されると、レート制限を伝える追加の通知が送信されます。 レート制限が期限切れになった場合は、通知で示されます。

## <a name="next-steps"></a>次のステップ ##
* 詳細については、「[SMS アラート動作](monitoring-sms-alert-behavior.md)」を参照してください。
* [アクティビティ ログ アラートの概要](monitoring-overview-alerts.md)を把握し、アラートを受信する方法について学習します。  
* [サービスの正常性通知が投稿されるたびにアラートを設定](monitoring-activity-log-alerts-on-service-notifications.md)する方法について学習します。
