---
title: "Azure での自動スケールの使用 | Microsoft Docs"
description: "Azure でリソースをスケールする方法について説明します。"
author: rajram
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: rajram
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: a0a420adc57a0bcbb3779183324ab7bfafcb5bf1
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="get-started-with-auto-scale-in-azure"></a>Azure での自動スケールの使用
この記事では、Azure Portal でリソースの自動スケール設定をセットアップする方法について説明します。

Azure Monitor の自動スケールは、仮想マシン スケール セット (VMSS)、Cloud Services、App Service プラン、App Service Environment にのみ適用されます。 

# <a name="lets-get-started"></a>作業の開始

## <a name="discover-the-auto-scale-settings-in-your-subscriptions"></a>サブスクリプションで自動スケール設定を検出する
Azure Monitor で自動スケールを適用できるすべてのリソースを検出できます。 リソースを検出するには、次の手順に従います。

- [Azure Portal][1] を開きます。
- 左側のナビゲーション ウィンドウで、Azure Monitor のアイコンをクリックします。
  ![Azure Monitor の起動][2]
- [自動スケール] 設定をクリックすると、自動スケールを適用できるすべてのリソースと、現在の自動スケールの状態が表示されます。![Azure Monitor での自動スケールの検出][3]

上部のフィルター ウィンドウを使用して一覧の範囲を絞ることで、特定のリソース グループ、特定のリソースの種類、または特定のリソースを選択できます。

各リソースについて、現在のインスタンス数と自動スケールの状態を確認できます。 自動スケールの状態は次のいずれかになります。

- 未構成: このリソースの自動スケール設定はまだ有効になっていません。
- 有効: このリソースの自動スケール設定は有効になっています。
- 無効: このリソースの自動スケール設定は無効になっています。

## <a name="create-your-first-auto-scale-setting"></a>最初の自動スケール設定を作成する

簡単な手順に従って、最初の自動スケール設定を作成します。

- Azure Monitor で [自動スケール] ブレードを開き、スケールするリソースを選択します  (以下の手順では、Web アプリに関連付けられている App Service プランを使用します。 [初めての ASP.NET Web アプリを Azure に 5 分で作成][4]できます)。
- リソースのスケール設定ブレードで、現在のインスタンス数が 1 であることを確認します。 [Enable autoscale (自動スケールを有効にする)] をクリックします。
  ![新しい Web アプリのスケール設定][5]
- スケール設定の名前を指定し、[ルールの追加] をクリックします。 スケール ルールのオプションが、右側にコンテキスト ペインとして表示されます。 既定では、リソースの CPU の割合が 70% を超えた場合に、インスタンス数を 1 つ増やしてスケールするオプションが設定されています。 このオプションを既定値のままにし、[追加] をクリックします。
  ![Web アプリのスケール設定の作成][6]
- これで最初のスケール ルールが作成されました。 ベスト プラクティスが示され、少なくとも 1 つのスケール イン ルールを含めるよう勧められます。 これを行うには、[ルールの追加] をクリックし、[演算子] を [より小さい]、[しきい値] を "20"、[操作] を [カウントを減らす量] にそれぞれ設定します。 これで、CPU 使用率に基づいてスケールアウト/スケールインするスケール設定が作成されます。
  ![CPU に基づくスケール][8]
- [保存] をクリックします。

おめでとうございます。 CPU 使用率に基づいて Web アプリを自動スケールする最初のスケール設定が作成されました。

> 注: VMSS またはクラウド サービス ロールを使用する場合も同じ手順が適用されます。

# <a name="other-considerations"></a>その他の考慮事項
## <a name="scale-based-on-a-schedule"></a>スケジュールに基づくスケール変更
常に CPU に基づいてスケールするだけでなく、特定の曜日に異なる方法でスケールを設定することもできます。

- [Add a scale condition (スケール条件の追加)] をクリックします。
- スケール モードとルールの設定は既定の条件と同じです。
- スケジュールについて、[Repeat specific days (特定の曜日を繰り返す)] を選択します。
- 曜日を選択し、選択した曜日にスケール条件を適用する開始時刻と終了時刻を選択します。

![スケジュールに基づくスケール条件][9]
## <a name="scale-differently-on-specific-dates"></a>特定の日に異なる方法でスケールする
常に CPU に基づいてスケールするだけでなく、特定の日に異なる方法でスケールを設定することもできます。

- [Add a scale condition (スケール条件の追加)] をクリックします。
- スケール モードとルールの設定は既定の条件と同じです。
- スケジュールについて、[Specify start/end dates (開始日/終了日を指定する)] を選択します。
- 開始日と終了日を選択し、選択した日にスケール条件を適用する開始時刻と終了時刻を選択します。

![日付に基づくスケール条件][10]

## <a name="view-the-scale-history-of-your-resource"></a>リソースのスケール履歴を表示する
リソースをスケールアップ/スケールダウンするたびに、アクティビティ ログにイベントが記録されます。 [実行履歴] タブに切り替えることで、過去 24 時間のリソースのスケール履歴を表示できます。

![実行履歴][11]

すべてのスケール履歴 (最大 90 日間) を表示する場合は、[Click here to see more details (詳細を表示するには、ここをクリックしてください)] をクリックします。 これにより、リソースが含まれ、カテゴリとして "自動スケール" が事前に選択されたアクティビティ ログが表示されます。

## <a name="view-the-scale-definition-of-the-resource"></a>リソースのスケール定義を表示する
自動スケール設定は ARM リソースです。 [JSON] タブに切り替えることで、JSON でスケール定義を表示できます。

![スケール定義][12]

必要に応じて、JSON に直接変更を加えることができます。 これらの変更は保存時に反映されます。

## <a name="disable-autoscale-and-manually-scale-your-instances"></a>自動スケールを無効にしてインスタンスを手動でスケールする
現在のスケール設定を無効にし、リソースを手動でスケールすることが必要な場合があります。

上部の [Disable autocale (自動スケールを無効にする)] をクリックします。
![自動スケールの無効化][13]

このオプションを使用すると、構成が無効になりますが、自動スケールを再度有効にすると、元の構成に戻すことができます。 自動スケールを無効にすると、手動でスケールするインスタンス数を設定できるようになります。

![手動スケールの設定][14]

[Enable autoscale (自動スケールを有効にする)] をクリックし、[保存] をクリックすることで、いつでも自動スケールに戻すことができます。

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/monitoring-autoscale-get-started/azure-monitor-launch.png
[3]: ./media/monitoring-autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet
[5]: ./media/monitoring-autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/monitoring-autoscale-get-started/scale-in-recommendation.png
[8]: ./media/monitoring-autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/monitoring-autoscale-get-started/scale-condition-schedule.png
[10]: ./media/monitoring-autoscale-get-started/scale-condition-dates.png
[11]: ./media/monitoring-autoscale-get-started/scale-history.png
[12]: ./media/monitoring-autoscale-get-started/scale-definition-json.png
[13]: ./media/monitoring-autoscale-get-started/disable-autoscale.png
[14]: ./media/monitoring-autoscale-get-started/set-manualscale.png
