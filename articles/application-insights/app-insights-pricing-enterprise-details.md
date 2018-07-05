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
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: mbullwin
ms.openlocfilehash: 65307eab0bf1b5f502f11c14c369826cd12e0966
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2018
ms.locfileid: "36311086"
---
# <a name="enterprise-plan-details"></a>Enterprise プランの詳細

Azure Application Insights には、Basic と Enterprise の 2 つの価格プランがあります。 [Basic](app-insights-pricing.md) 価格プランは既定のプランです。 これには、Enterprise プランのすべての機能が追加コストなしで含まれます。 Basic プランでは基本的に、取り込まれるデータの量に基づいて請求されます。 

Enterprise プランはノードあたりの料金で、各ノードが 1 日あたりの無料データ利用分を受け取ります。 Enterprise 価格プランでは、含まれる無料利用分を超えて取り込まれたデータに対して課金されます。 Operations Management Suite を使用している場合は、Enterprise プランを選択する必要があります。 

お客様の通貨およびリージョンでの現在の価格については、「[Application Insights の価格](http://azure.microsoft.com/pricing/details/application-insights/)」をご覧ください。

> [!NOTE]
> 2018 年 4 月に、Azure Monitoring 用の新しい価格モデルを[導入](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)しました。 このモデルでは、監視サービスのポートフォリオ全体で単純な "従量課金制" モデルを採用しています。 [新しい価格モデル](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs)の詳細、使用パターンに基づいて[このモデルへの移行の影響を評価](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model)する方法、[新しいモデルを有効にする方法](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)をご確認ください。

## <a name="how-the-enterprise-plan-works"></a>Enterprise プランのしくみ

* Enterprise プランでは、アプリに対してテレメトリを送信するノードごとに料金が課金されます。
 * *ノード*とは、アプリをホストする物理または仮想サーバー マシン (または Platform-as-a-Service (PaaS) ロール インスタンス) のことです。
 * 開発マシン、クライアントのブラウザー、およびモバイル デバイスはノードとしてカウントされません。
 * テレメトリを送信するコンポーネント (Web サービスやバックエンド ワーカーなど) がアプリに複数ある場合、それらは個別にカウントされます。
 * [ライブ メトリック ストリーム](app-insights-live-stream.md) データは、課金対象としてカウントされません。 サブスクリプション内では、料金はノード単位で計算されます (アプリ単位ではありません)。 12 のアプリに対して 5 つのノードがテレメトリを送信する場合、料金は 5 ノード分になります。
* 料金の見積りは月単位で計算されますが、実際の料金は、ノードがアプリからテレメトリを送信した時間分しか課金されません。 1 時間あたりの料金は、1 か月あたりの見積り額を 744 で割ったものです (1 か月 31 日の時間数)。
* 検出された各ノードに、1 日あたり 200 MB のデータ量の割り当てが (時間単位の精度で) 与えられます。 未使用のデータ割り当て分が日をまたいで繰り越されることはありません。
 * Enterprise の価格プランを選んだお客様には、テレメトリをサブスクリプション内の Application Insights リソースに送信するノードの数に基づいて、日単位の無料データ利用分が提供されます。 したがって、終日データを送信する 5 つのノードがある場合、サブスクリプション内のすべての Application Insights リソースに 1 GB の許容量が適用されます。 無料データ利用分はすべてのノードで共有されるため、一部のノードのデータ送信量が他のノードより多くても問題はありません。 特定の日に、Application Insights リソースがサブスクリプションの日単位の無料データ割り当て分を超えるデータを受信した場合は、1 GB ごとに超過データ料金が適用されます。 
 * 1 日あたりの無料データ利用分は、各ノードが 1 日にテレメトリを送信する時間数 (UTC を使用) を 24 で割った値に、200 MB を掛け合わせて計算します。 つまり、4 つのノードが、1 日 24 時間のうち 15 時間テレメトリを送信した場合、その日のデータ量は ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB になります。 データ超過分 1 GB あたり 2.30 米国ドルの料金ですので、その日ノードが 1 GB のデータを送信した場合、超過データ料金は 1.15 米国ドルになります。
 * Enterprise プランの 1 日あたりの無料データ利用分は、Basic プランが選択されたアプリケーションとは共有できません。 無料データの未使用分は、翌日への引き継ぎはできません。 

## <a name="examples-of-how-to-determine-distinct-node-count"></a>個別のノード カウントを決定する方法の例

| シナリオ                               | 日単位の合計ノード数 |
|:---------------------------------------|:----------------:|
| 1 つのアプリケーションで 3つの Azure App Service インスタンスと 1 つの仮想サーバーが使用されている。 | 4 |
| 3 つのアプリケーションが 2 つの VM で実行され、これらのアプリケーションの Application Insights リソースが、同じサブスクリプションの Enterprise プランにある。 | 2 | 
| 4 つのアプリケーションの Application Insights リソースが同じサブスクリプションにあり、各アプリケーションはピーク外の時間帯 16 時間は 2 つのインスタンス、ピーク時間帯 8 時間は 4 つのインスタンスを実行している。 | 13.33 | 
| Cloud Services に 1 つの Worker ロールと 1 つの Web ロールが含まれ、それぞれ 2 つのインスタンスを実行している。 | 4 | 
| 5 つのノードがある Azure Service Fabric クラスターが 50 のマイクロサービスを実行し、各マイクロサービスが 3 つのインスタンスを実行している。 | 5|

* 正確なノード カウントは、アプリケーションで使用している Application Insights SDK によって異なります。 
  * SDK バージョン 2.2 以降では、Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) と [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) の両方が、各アプリケーション ホストをノードとして報告します。 例としては、物理サーバーと VM ホストのコンピューター名やクラウド サービスのインスタンス名があります。  唯一の例外は、[.NET Core](https://dotnet.github.io/) と Application Insights Core SDK のみを使用するアプリケーションです。 その場合は、ホスト名が使用できないためすべてのホストに対して 1 つのノードが報告されます。 
  * 以前のバージョンの SDK である [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) は、新しいバージョンの SDK と同じように動作しますが、[Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) は実際のアプリケーション ホストの数に関係なく 1 つのノードのみ報告します。 
  * アプリケーションで SDK を使用して**ロール インスタンス**をカスタム値に設定すると、既定ではノード カウントの決定に同じ値が使用されます。 
  * クライアント コンピューターやモバイル デバイスから実行されているアプリで新しいバージョンの SDK を使用している場合は、ノード カウントが (クライアント コンピューターやモバイル デバイスの数が多いため) 非常に大きい数値を返す可能性があります。 
