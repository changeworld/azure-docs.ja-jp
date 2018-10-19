---
title: Azure のメンテナンス スケジュール (プレビュー) |Microsoft Docs
description: メンテナンス スケジューリングにより、お客様は、Azure SQL Data warehouse サービスが新しい機能、アップグレードと修正プログラム (パッチ) をロールアウトするために使用する必要なスケジュールされたメンテナンス イベントを計画することができます。
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/06/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 6fbf914c8035344d33e8d2739fb9d92d5757c3d1
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228167"
---
# <a name="viewing-a-maintenance-schedule"></a>メンテナンス スケジュールの表示 

## <a name="portal"></a>ポータル

デフォルトでは、新しく作成されたすべての Azure SQL Data Warehouse インスタンスには、デプロイ中に適用される 8 時間のシステム定義済みのプライマリおよびセカンダリ メンテナンス ウィンドウが適用されます。これは、デプロイが完了するとすぐに編集できます。 事前に通知することなく、指定されたメンテナンス期間の外でメンテナンスは行われません。
ポータルのデータ ウェアハウスに適用されたメンテナンス スケジュールを表示するには、次の手順を実行します。

ポータルのデータ ウェアハウスに適用されたメンテナンス スケジュールを表示するには、次の手順を実行します。
1.  [Azure Portal](https://portal.azure.com/) にサインインします。
2.  表示したいデータウェア ハウスを選択します。 
3.  選択した Azure SQL Data Warehouse が概要ブレードで開きます。 選択したデータ ウェアハウスに適用されたメンテナンス スケジュールは、メンテナンス スケジュール (プレビュー) の下に表示されます。

![[概要] ブレード](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>次の手順
Azure Monitor を使用してアラートの作成、表示、および管理について[詳しく理解します](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage)。
ログ アラート ルールの Webhook アクションについて[詳しく理解します](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)。
Azure Service Map について[詳しく理解します](https://docs.microsoft.com/azure/service-health/service-health-overview)


