---
title: Application Insights を使用したライブ Azure App Service アプリのプロファイリング | Microsoft Docs
description: Application Insights Profiler を使用して Azure App Service でライブ アプリをプロファイルします。
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: f3ec10a970406cbb1bb6a1a52ffa8508e37fc516
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414169"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Application Insights を使用したライブ Azure App Service アプリのプロファイリング

プロファイラーは、現在、Azure App Service で実行されている ASP.NET アプリおよび ASP.NET Core アプリに対して使用できます。 Profiler を使用するには、Basic 以上のサービス レベルが必要です。 現時点では、[この方法](profiler-aspnetcore-linux.md)でのみ Linux で Profiler を有効にできます。

## <a id="installation"></a> アプリの Profiler を有効にする
アプリで Profiler を有効にするには、次の手順に従います。 別の種類の Azure サービスを実行している場合、以下のサポートされている他のプラットフォームで Profiler を有効にする手順を参照してください。
* [Cloud Services](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric アプリケーション](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler は App Services ランタイムの一部としてプレインストールされますが、App Service アプリのプロファイルを取得するには、有効にする必要があります。 ソース コードに App Insights SDK を含めている場合でも、アプリをデプロイした後にプロファイラーを有効にするには、次の手順に従います。

1. Azure Portal の **[App Services]** ウィンドウに移動します。
2. **[設定] > [Application Insights]** ウィンドウに移動します。

   ![App Service ポータルで App Insights を有効にする](./media/profiler/AppInsights-AppServices.png)

3. ウィンドウの指示に従って新しいリソースを作成するか、既存の App Insights リソースを選択してアプリを監視します。 また、Profiler が**オン**になっていることを確認します。

   ![App Insights のサイト拡張機能を追加する][Enablement UI]

4. App Services のアプリ設定を使用して Profiler を有効にできるようになりました。

    ![Profiler 用のアプリ設定][profiler-app-setting]

## <a name="disable-profiler"></a>Profiler を無効にする

個々の Web アプリのインスタンスで Profiler を停止または再起動するには、**[Web ジョブ]** で Web Apps リソースに移動します。 Profiler を削除するには、**[拡張機能]** に移動します。

![Web ジョブで Profiler を無効にする][disable-profiler-webjob]

パフォーマンスの問題をできるだけ早く検出するために、すべてのアプリで Profiler を有効にすることをお勧めします。

Web アプリに対する変更を WebDeploy を使用してデプロイする場合は、デプロイ中の削除対象から App_Data フォルダーを除外してください。 そうしないと、次に Azure に Web アプリケーションをデプロイするときに Profiler 拡張機能のファイルが削除されます。



## <a name="next-steps"></a>次の手順

* [Visual Studio での Application Insights の操作](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
