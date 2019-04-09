---
title: Azure Stream Analytics ジョブを開始する方法
description: この記事では、Stream Analytics ジョブを開始する方法について説明します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: fb1d724907c09e2eb77930f5a235336ca8cd3a25
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57886849"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Azure Stream Analytics ジョブを開始する方法

Azure portal、Visual Studio、PowerShell を使用して、Azure Stream Analytics ジョブを開始できます。 ジョブを開始するときは、ジョブが出力の作成を開始する時刻を選択します。 Azure portal、Visual Studio、PowerShell は、開始時刻を設定する方法がそれぞれ異なります。 それらの方法について以下に説明します。

## <a name="azure-portal"></a>Azure ポータル

Azure portal でジョブに移動し、概要ページで **[開始]** を選択します。 **ジョブ出力の開始時刻**を選択し、**[開始]** を選択します。

**[ジョブ出力の開始時刻]** には、*[今すぐ]*、*[カスタム]*、*[最終停止時刻]* の 3 つのオプションがあります。 *[今すぐ]* を選択すると、現在の時刻にジョブが開始されます。 *[カスタム]* を選択すると、過去または将来のユーザー設定時刻をジョブの開始時刻に設定できます。 データを失うことなく停止したジョブを再開するには、*[最終停止時刻]* を選択します。

## <a name="visual-studio"></a>Visual Studio

ジョブ ビューで、緑色の矢印ボタンを選択してジョブを開始します。 **[ジョブ出力の開始モード]** を設定し、**[開始]** を選択します。 ジョブの状態が **[実行中]** に変わります。

**[ジョブ出力の開始モード]** には、*JobStartTime*、*CustomTime*、*LastOutputEventTime* の 3 つのオプションがあります。 このプロパティが指定されていない場合、既定値は *JobStartTime* です。

*JobStartTime* では、出力イベント ストリームの開始点がジョブの開始時刻と同じになります。

*CustomTime* では、*OutputStartTime* パラメーターで指定されているユーザー設定時刻に出力が開始されます。

*LastOutputEventTime* では、出力イベント ストリームの開始点が最終イベント出力時刻と同じになります。

## <a name="powershell"></a>PowerShell

PowerShell を使用してジョブを開始するには、次のコマンドレットを使用します。

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

**OutputStartMode** には、*JobStartTime*、*CustomTime*、*LastOutputEventTime* の 3 つのオプションがあります。 このプロパティが指定されていない場合、既定値は *JobStartTime* です。

*JobStartTime* では、出力イベント ストリームの開始点がジョブの開始時刻と同じになります。

*CustomTime* では、*OutputStartTime* パラメーターで指定されているユーザー設定時刻に出力が開始されます。

*LastOutputEventTime* では、出力イベント ストリームの開始点が最終イベント出力時刻と同じになります。

`Start-AzStreamAnalyitcsJob` コマンドレットの詳細については、[Start-AzStreamAnalyticsJob リファレンス](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob)をご覧ください。

## <a name="next-steps"></a>次の手順

* [クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)
* [クイック スタート:Azure PowerShell を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-powershell.md)
* [クイック スタート:Visual Studio の Azure Stream Analytics ツールを使用した Stream Analytics ジョブの作成](stream-analytics-quick-create-vs.md)
