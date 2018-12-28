---
title: Application Insights を使用したライブ Azure Web アプリのプロファイリング | Microsoft Docs
description: Application Insights Profiler を使用して Azure でライブ Web アプリをプロファイルします。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 4d957c26bd4e4ae278c0909c9df1476b02954b86
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138009"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Application Insights を使用してライブ Azure Web アプリをプロファイルする

Profiler は現在、Web Apps 上で実行されている ASP.NET および ASP.NET Core Web アプリに対して機能します。 Profiler を使用するには、Basic 以上のサービス レベルが必要です。 現時点では、[この方法](app-insights-profiler-aspnetcore-linux.md)でのみ Linux で Profiler を有効にできます。

## <a id="installation"></a> Web Apps で Profiler を有効にする
Web アプリで Profiler を有効にするには、次の手順に従います。 別の種類の Azure サービスを実行している場合、以下のサポートされている他のプラットフォームで Profiler を有効にする手順を参照してください。
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric アプリケーション](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler は App Services ランタイムの一部としてプレインストールされますが、Azure Web アプリのプロファイルを取得するには、有効にする必要があります。 ソース コードに App Insights SDK を含めている場合でも、Web アプリをデプロイした後にプロファイラーを有効にするには、次の手順に従います。

1. Azure Portal の **[App Services]** ウィンドウに移動します。
1. **[設定] > [Application Insights]** ウィンドウに移動します。

   ![App Service ポータルで App Insights を有効にする](./media/app-insights-profiler/AppInsights-AppServices.png)

1. ウィンドウの指示に従って新しいリソースを作成するか、既存の App Insights リソースを選択して Web アプリを監視します。 また、Profiler が**オン**になっていることを確認します。

   ![App Insights のサイト拡張機能を追加する][Enablement UI]

1. App Services のアプリ設定を使用して Profiler を有効にできるようになりました。

    ![Profiler 用のアプリ設定][profiler-app-setting]

## <a name="disable-profiler"></a>Profiler を無効にする

個々の Web アプリ インスタンスで Profiler を停止または再起動するには、**[Web ジョブ]** で Web Apps リソースに移動します。 Profiler を削除するには、**[拡張機能]** に移動します。

![Web ジョブで Profiler を無効にする][disable-profiler-webjob]

パフォーマンスの問題をできるだけ早く検出するために、すべての Web アプリで Profiler を有効にすることをお勧めします。

Web アプリに対する変更を WebDeploy を使用してデプロイする場合は、デプロイ中の削除対象から App_Data フォルダーを除外してください。 そうしないと、次に Azure に Web アプリケーションをデプロイするときに Profiler 拡張機能のファイルが削除されます。



## <a name="next-steps"></a>次の手順

* [Visual Studio での Application Insights の操作](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png

