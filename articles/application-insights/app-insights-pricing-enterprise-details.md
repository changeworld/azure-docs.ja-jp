---
title: Azure Application Insights の従来の Enterprise プラン 価格プランの詳細 | Microsoft Docs
description: Application Insights でテレメトリの量を管理し、コストを監視します。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 6e7591ccf0f21099474a08dda088422c377135f6
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="enterprise-plan-details"></a>Enterprise プランの詳細

Application Insights には、2 種類の価格プランがあります。 既定のプランは [Basic](app-insights-pricing.md) であり、取り込んだデータのボリュームでは、基本的には追加のコストや請求なしで Enterprise プラン プランに相当する機能をすべて含みます。 Operations Management Suite を使用している場合は、日次でのデータ許容量に沿ってノード単位で課金され、含まれる許容量を超える取り込みデータに対してさらに課金される Enterprise プランを選択できます。

お客様の通貨および地域での現在の料金については、[Application Insights の価格ページ](http://azure.microsoft.com/pricing/details/application-insights/)をご覧ください。

## <a name="heres-how-the-enterprise-plan-works"></a>以下に、Enterprise プランのしくみを説明します。

* Enterprise プランでは、アプリに対してテレメトリを送信しているノードごとに料金が課金されます。
 * *ノード*とは、アプリをホストする物理または仮想サーバー マシン (または Platform-as-a-Service (PaaS) ロール インスタンス) のことです。
 * 開発マシン、クライアントのブラウザー、およびモバイル デバイスはノードとしてカウントされません。
 * テレメトリを送信するコンポーネント (Web サービスやバックエンド ワーカーなど) がアプリに複数ある場合、それらは個別にカウントされます。
 * [ライブ メトリック ストリーム](app-insights-live-stream.md) データは、課金対象としてカウントされません。サブスクリプション全体を通じて、料金はノード単位で計算されます (アプリ単位ではありません)。 12 のアプリに対して 5 つのノードがテレメトリを送信している場合、料金は 5 ノード分になります。
* 料金の見積りは月単位で計算されますが、実際の料金は、ノードがアプリからテレメトリを送信した時間分しか課金されません。 1 時間あたりの料金は、1 か月あたりの見積り額/744 です (1 か月 31 日の時間数)。
* 検出された各ノードに、1 日あたり 200 MB のデータ量の割り当てが (時間単位の精度で) 与えられます。 未使用のデータ割り当て分が日をまたいで繰り越されることはありません。
 * Enterprise の価格オプションを選んだお客様には、テレメトリをサブスクリプション内の Application Insights リソースに送信するノードの数に基づいて、日単位の無料データ利用分が提供されます。 したがって、終日データを送信する 5 つのノードがある場合、サブスクリプション内のすべての Application Insights リソースに 1 GB の許容量が適用されます。 無料データ利用分はすべてのノードで共有されるため、一部のノードのデータ送信量が他のノードより多くても問題はありません。 特定の日に、Application Insights リソースがサブスクリプションの日単位の無料データ割り当て分を超えるデータを受信した場合は、1 GB ごとに超過データ料金が適用されます。 
 * 1 日あたりの無料データ利用分は、各ノードが 1 日にテレメトリを送信する時間数 (UTC を使用) を 24 で割った値に、200 MB を掛け合わせて計算します。 つまり、4 つのノードが、1 日 24 時間のうち 15 時間テレメトリを送信した場合、その日のデータ量は ((4 x 15) / 24) x 200 MB = 500 MB になります。 データ超過分 1 GB あたり 2.30 米国ドルの料金ですので、その日ノードが 1 GB のデータを送信した場合、超過データ料金は 1.15 米国ドルになります。
 * Enterprise プランの日単位の無料利用分は、Basic オプションを選択したアプリケーションとは共有されず、未使用の無料利用分は日をまたいで繰り越されません。 

## <a name="here-are-some-examples-of-determining-distinct-node-count"></a>個別のノード数を決定するいくつかの例を次に示します。

| シナリオ                               | 日単位の合計ノード数 |
|:---------------------------------------|:----------------:|
| 1 つのアプリケーションで 3つの Azure App Service インスタンスと 1 つの仮想サーバーが使用されている。 | 4 |
| 3 つのアプリケーションが 2 つの VM で実行され、これらのアプリケーションの Application Insights リソースが、同じサブスクリプションの Enterprise プランにある。 | 2 | 
| Applications Insights リソースが同じサブスクリプションにある 4 つのアプリケーション。 各アプリケーションが 2 つのインスタンスをピーク時以外の 16 時間実行し、4 つのインスタンスをピーク時の 8 時間実行している。 | 13.33 | 
| Cloud Services に 1 つの Worker ロールと 1 つの Web ロールが含まれ、それぞれ 2 つのインスタンスを実行している。 | 4 | 
| 5 つのノードがある Service Fabric クラスターが 50 のマイクロサービスを実行し、各マイクロサービスが 3 つのインスタンスを実行している。 | 5|

* 正確なノード カウントの動作は、アプリケーションで使用している Application Insights SDK によって異なります。 
  * SDK バージョン 2.2 以降では、Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) と [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) の両方で各アプリケーション ホストがノードとして報告されます (例: 物理サーバーと VM ホストのコンピューター名、または Cloud Services の場合はインスタンス名)。  唯一の例外は [.NET Core](https://dotnet.github.io/) と Application Insights Core SDK のみを使用するアプリケーションです。この場合はホスト名が使用できないため、すべてのホストについて報告されるのは 1 つのノードのみです。 
  * 以前のバージョンの SDK である [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) は、新しいバージョンの SDK と同じように動作しますが、[Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) は実際のアプリケーション ホストの数に関係なく 1 つのノードのみ報告します。 
  * アプリケーションで SDK を使用してロール インスタンスをカスタム値に設定すると、既定でノード数の決定に同じ値が使用されます。 
  * クライアント コンピューターやモバイル デバイスから実行されているアプリで新しいバージョンの SDK を使用している場合は、ノードのカウントが (多数のクライアント コンピューターやモバイル デバイスから) 非常に大きい数値を返す可能性があります。 
