---
title: "Azure App Service 診断の概要 | Microsoft Docs"
description: "App Service 診断を使用して Web アプリに関する問題をトラブルシューティングする方法について説明します。"
keywords: "アプリ サービス, azure app service, 診断, サポート, web アプリ, トラブルシューティング, セルフヘルプ"
services: app-service
documentationcenter: 
author: jen7714
manager: cfowler
editor: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.openlocfilehash: 9526817ce7969edcd5e9c56ec153bb4e3ebaa501
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2018
---
# <a name="azure-app-service-diagnostics-overview"></a>Azure App Service 診断の概要 

Web アプリケーションを実行するに当たっては、500 エラーが発生したり、サイトがダウンした場合に向けての準備体制が重要になります。 App Service 診断は、Web アプリのトラブルシューティングをインテリジェントな対話型のエクスペリエンスで支援する、構成不要のツールです。 Web アプリに問題が発生した場合、App Service 診断は問題点を指摘し、その問題をすばやく簡単に解決するための適切な情報へとユーザーをガイドします。 
 
この機能は Web アプリの問題発生時刻が過去 24 時間以内である場合に特に役立ちますが、いずれの診断グラフも、任意の時点を対象に分析を実行することができます。 右側の列には、追加のトラブルシューティング ツールと、役立つドキュメントやフォーラムへのリンクが表示されます。

## <a name="open-app-service-diagnostics"></a>App Service 診断を開く

App Service 診断にアクセスするには、[Azure ポータル](https://portal.azure.com)で App Service Web アプリに移動します。 

左側のナビゲーションで、**[問題の診断と解決]** をクリックします。

![ホームページ](./media/app-service-diagnostics/Homepage1.png)

## <a name="health-checkup"></a>正常性検査

Web アプリのどこに問題があるのかわからない場合や、問題のトラブルシューティングをどこから始めればよいかわからない場合には、正常性検査を実行することをお勧めします。 正常性検査機能は、Web アプリケーションを分析し、何が正常で何が異常かを指摘する簡潔な対話型の要約情報によって、調査が必要な箇所をユーザーに示します。 また、インテリジェントな対話型のインターフェイスが、トラブルシューティングのプロセスを通じてユーザーをガイドします。  

![正常性検査](./media/app-service-diagnostics/HealthCheckup2.png)

過去 24 時間以内に特定の問題カテゴリで問題が検出された場合は、フル診断レポートを表示することができます。また、App Service 診断によって、詳しいトラブルシューティング アドバイスや、さらなるガイドを得るための次の手順を参照するように勧められる場合もあります。

![トラブルシューティングと次の手順](./media/app-service-diagnostics/Troubleshooting3.png)

## <a name="tile-shortcuts"></a>タイル ショートカット

どのようなトラブルシューティング情報が必要なのかが正確にわかっている場合は、タイル ショートカットから、関心のある問題カテゴリのフル診断レポートに直接アクセスできます。 正常性検査と比べて、タイル ショートカットでは診断メトリックによりダイレクトにアクセスすることができますが、提供されるガイドは少なくなります。  

![タイル ショートカット](./media/app-service-diagnostics/TileShortcuts4.png)

## <a name="diagnostic-report"></a>診断レポート

[正常性検査](#health-checkup)の実行後に詳細な情報を参照する場合でも、[ショートカット タイル](#tile-shortcuts)をクリックした場合でも、フル診断レポートには、過去 24 時間の記録から関連するグラフ メトリックが表示されます。 アプリにダウンタイムが発生した場合は、タイムラインの下にオレンジ色のバーでダウンタイムが示されます。 ダウンタイムを選択すると、そのダウンタイムに関する分析データと、推奨の解決策が表示されます。 

![診断レポート](./media/app-service-diagnostics/DiagnosticReport5.png)

