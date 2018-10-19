---
title: Azure メンテナンス スケジュール (プレビュー) |Microsoft Docs
description: メンテナンス スケジューリングにより、お客様は、Azure SQL データ ウェアハウス サービスが新しい機能をロールアウト、アップグレード、パッチするために必要な、スケジュールされたメンテナンス イベントを計画することができます。
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/07/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: a6eedc0bac7aab69a9138f4f63d0d9d802e74dfc
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228197"
---
# <a name="change-a-maintenance-schedule"></a>メンテナンス スケジュールの変更 

## <a name="portal"></a>ポータル
メンテナンス スケジュールをいつでも更新または、変更できます。 ただし、選択されたインスタンスが現在アクティブなメンテナンス サイクルを行っている場合、設定は保存され、次に特定されたメンテナンス期間中のみ有効になります。 アクティブなメンテナンス イベント中にデータウェアハウスを監視する方法について[さらに理解する](https://docs.microsoft.com/azure/service-health/resource-health-overview)。 

プレビューでは、7 日間の 2 つのメンテナンス ウィンドウを選択するように求められます。 各メンテナンス ウィンドウはそれぞれ 3 時間から 8 時間の間で、3 時間は現在利用可能な最短オプションです。 メンテナンスは、特定されたメンテナンス ウィンドウ内のいつでも実行できます。しかし、事前に通知された時間ウィンドウの外では発生しません。 また、サービスが新しいコードをデータ ウェアハウスにデプロイする際に、接続が一時切断されることもあります。 

## <a name="identifying-the-primary-and-secondary-windows"></a>プライマリとセカンダリのウィンドウを識別します。

プライマリ ウィンドウとセカンダリ ウィンドウを異なる期間内に指定する必要があります。プライマリ ウィンドウ (火曜日～木曜日) 、セカンダリ ウィンドウ (土曜日～日曜日)。

ポータルのデータウェア ハウスに適用されたメンテナンス スケジュールを変更するには、次の手順を実行します。
1.  [Azure Portal](https://portal.azure.com/) にサインインします。
2.  更新するデータ ウェア ハウスを選択します。 [概要] ブレードで、ページが開きます。 
3.  メンテナンス スケジュール設定ページにアクセスするには、概要ブレードのメンテナンス スケジュール (プレビュー) 概要リンクをクリックするか、左側のリソース メニューのメンテナンス スケジュール オプションを使用します。  

    ![概要ブレードのオプション](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. プライマリ メンテナンス ウィンドウの優先日の範囲は、ページ上部のラジオ ボタンを使用して特定できます。 この選択は、平日または週末にプライマリ  ウィンドウが発生するかどうかを決定します。 選択した値に応じて、下のドロップダウン値が更新されます。 プレビュー中、一部のリージョンでは、まだ利用可能な日のオプションのフルセットをサポートしていない可能性があります。 これらの値は今後数か月で更新されます。

   ![メンテナンスの設定ブレード](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. 以下の日、開始時間、時間ウィンドウのドロップダウンを使用して、希望のプライマリおよびセカンダリのメンテナンス ウィンドウを選択します。 ブレードの下部にあるスケジュールの概要は、選択されたドロップダウン値に基づいて更新されます。

#### <a name="dropdown-options"></a>ドロップダウン リストのオプション
- 日：選択したウィンドウでメンテナンスを実行するための優先日。
- 開始時間：優先メンテナンス ウィンドウの開始時間。
- 時間ウィンドウ: 時間ウィンドウの優先期間。

  優先のメンテナンス期間を選択した後は、[保存] をクリックします。 新しいスケジュールが有効でないことを確認する確認メッセージが表示されます。 メンテナンス スケジューリングをまだサポートしていないリージョンにスケジュールを保存すると、次のメッセージが表示されます。 選択したリージョンで機能が使用可能になると、設定が保存され、アクティブになります。    

    ![トーストのリージョンでアクティブではありません。](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>次の手順
- ログ アラート ルールの Webhook アクションについて、[さらに理解する](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)。
- Azure Service Health の詳細について、[さらに理解する](https://docs.microsoft.com/azure/service-health/service-health-overview)。


