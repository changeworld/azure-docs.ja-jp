---
title: CloudSimple アクティビティ管理
titleSuffix: Azure VMware Solution by CloudSimple
description: VMware Solution by CloudSimple でのアクティビティの概念について説明します
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 985fcc0aa99723fde5e0c11babe3777c31cbd4c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025097"
---
# <a name="activity-management-overview"></a>アクティビティ管理の概要

CloudSimple では、プライベート クラウド環境の機能に影響を与える可能性がある、あらゆるアクティビティが追跡記録されます。 アクティビティには、アラート、イベント、タスク、監査アクティビティなどがあります。 [[アクティビティ]](monitor-activity.md) ページには、現在のすべてのアクティビティがまとめられており、詳細情報をドリルダウンできます。

## <a name="events"></a>events

イベントは、CloudSimple ポータルでユーザーおよびシステム アクティビティを追跡します。 イベントは特定のリソースに関連するアクティビティと影響の重大度を示します。  CloudSimple ポータルからイベントを表示できます。

## <a name="alerts"></a>警告

アラートは、CloudSimple 環境での重要なアクティビティの通知です。 請求やユーザー アクセスに影響するイベントはアラートとして表示されます。  アラートは CloudSimple ポータルから受信確認できます。

## <a name="tasks"></a>処理手順

タスクでは、完了するまでに 30 秒以上かかる、あらゆるユーザー操作が追跡されます。 タスクの進行状況は CloudSimple ポータルから監視できます。  タスクが完了すると、完了にかかった合計時間が情報に含まれます。

## <a name="audit"></a>Audit

監査ログでは、ユーザーの操作が追跡されます。 監査ログには、ユーザーが操作に指定したパラメーターが含まれます。  監査ログを使用して、すべてのユーザーのユーザー アクティビティを監視できます。

## <a name="next-steps"></a>次のステップ

* [アカウントの概要を表示する](account.md)