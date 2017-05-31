---
title: "複数のロール、マイクロサービス、およびコンテナーに対する Azure Application Insights のサポート | Microsoft Docs"
description: "複数のコンポーネントまたはロールから成るアプリのパフォーマンスと使用状況を監視。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: dad17277452081427a01d077128a3a137b2190f5
ms.contentlocale: ja-jp
ms.lasthandoff: 05/17/2017


---
# <a name="monitor-multi-role-applications-with-application-insights-preview"></a>Application Insights (プレビュー) でマルチロール アプリケーションを監視する

複数のコンポーネント、ロール、またはサービスから成るアプリを、[Azure Application Insights](app-insights-overview.md) で監視することができます。 ロールの正常性とロール間の関係は、単一のアプリケーション マップに表示されます。 複数のロールによる個々の操作を、自動 HTTP 関連付けでトレースすることができます。 コンテナー診断は、アプリケーション テレメトリに統合し、関連付けることができます。 アプリケーションのすべてのロールに対して、単一の Application Insights リソースを使用します。 

![マルチロール アプリケーション マップ](./media/app-insights-monitor-multi-role-apps/app-map.png)

ここでは "ロール" は広い意味で使用されており、個別のプロジェクトとして構築された任意のアプリケーション コンポーネントまたはサービスを指します。 たとえば、典型的なビジネス アプリケーションは、REST API を通じて通信する複数のロールで構成されています。 ロールは、Docker、Service Fabric などのコンテナーや、クラウドまたはオンプレミスのホストでホストできます。 

### <a name="sharing-a-single-application-insights-resource"></a>単一の Application Insights リソースの共有 

ここでの主要なテクニックは、アプリケーションのすべてのロールから同じ Application Insights リソースにテレメトリを送信し、必要であれば `cloud_RoleName` プロパティを使用してロールを区別することです。 

一部のケースでは、この方法が適切ではなく、ロールのグループごとに個別のリソースを使用する方がよい場合があります。 たとえば、管理や課金のために、異なるリソースを使用しなければならないことがあります。 個別のリソースを使用する場合は、すべてのロールが単一のアプリケーション マップには表示されず、[Analytics](app-insights-analytics.md) で複数のロールにわたるクエリを実行することができません。 また、リソースごとにセットアップを行う必要があります。

そのことを考慮して、このドキュメントの残りの部分では、複数のロールから 1 つの Application Insights リソースにデータを送信することを想定しています。

## <a name="configure-multi-role-applications"></a>マルチロール アプリケーションを構成する

マルチロール アプリケーション マップを取得するには、以下の目標を達成する必要があります。

* アプリケーションの各ロールに、Application Insights パッケージの**最新のプレリリースをインストール**します。 
* アプリケーションのすべてのロールで、**単一の Application Insights リソースを共有**します。
* [プレビュー] ブレードで、**マルチロール アプリケーション マップを有効**にします。

アプリケーションの各ロールを、その種類に適した方法で構成します  ([ASP.NET](app-insights-asp-net.md)、[Java](app-insights-java-get-started.md)、[Node.js](app-insights-nodejs.md))。

### <a name="1-install-the-latest-pre-release-package"></a>1.最新のプレリリース パッケージをインストールする

各ロールのプロジェクトで、Appication Insights パッケージを更新するかインストールします。 Visual Studio を使用している場合:

1. プロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。 
2. **[プレリリースを含める]** を選択します。
3. Application Insights パッケージが [更新] に表示されたら、それを選択します。 

    表示されない場合は、以下の適切なパッケージを参照してインストールします。
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric - Service Fabric アプリケーションで実行されているゲスト実行可能ファイルや Docker コンテナーとして実行されているロールの場合
    * Microsoft.ApplicationInsights.ServiceFabric.Native - ServiceFabric アプリケーションのリライアブル サービスの場合
    * Microsoft.ApplicationInsights.Kubernetes - Kubernetes 上の Docker で実行されているロールの場合

### <a name="2-share-a-single-application-insights-resource"></a>2.単一の Application Insights リソースを共有する

* Visual Studio でプロジェクトを右クリックして **[Application Insights の構成]** を選択するか、**[Application Insights] > [構成]** の順に選択します。 最初のプロジェクトでは、ウィザードを使用して Application Insights リソースを作成します。 その後のプロジェクトでは、同じリソースを選択します。
* [Application Insights] メニューがない場合は、次のようにして手動で構成します。

   1. [Azure Portal](https://portal,azure.com) で、他のロール用に既に作成した Application Insights リソースを開きます。
   2. [概要] ブレードで [要点] ドロップダウン タブを開き、**インストルメンテーション キー**をコピーします。
   3. プロジェクトで ApplicationInsights.config を開き、`<InstrumentationKey>your copied key</InstrumentationKey>` を挿入します。

![インストルメンテーション キーを .config ファイルにコピーする](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-multi-role-application-map"></a>3.マルチロール アプリケーション マップを有効にする

Azure Portal でアプリケーションのリソースを開きます。 [プレビュー] ブレードで "*マルチロール アプリケーション マップ*" を有効にします。

### <a name="4-enable-docker-metrics-optional"></a>4.Docker メトリックを有効にします (省略可能) 

Azure Windows VM でホストされている Docker でロールが実行されている場合は、コンテナーから追加のメトリックを収集できます。 [Azure 診断](../monitoring-and-diagnostics/azure-diagnostics.md)構成ファイルに次のコードを挿入します。

```
"DiagnosticMonitorConfiguration": {
        ...
        "sinks": "applicationInsights",
        "DockerSources": {
                "Stats": {
                    "enabled": true,
                    "sampleRate": "PT1M"
                }
            },
        ...
    }
    ...   
    "SinksConfig": {
        "Sink": [{
            "name": "applicationInsights",
            "ApplicationInsights": "<your instrumentation key here>"
        }]
    }
    ...
}

```

## <a name="use-cloudrolename-to-separate-roles"></a>cloud_RoleName を使用してロールを区別する

`cloud_RoleName` プロパティは、すべてのテレメトリに関連付けられます。 このプロパティで、テレメトリを生成したロールまたはサービスを識別できます  (cloud_RoleInstance とは異なります。このプロパティは、複数のサーバー プロセスやマシンで並列して実行されている同じロールを区別するものです)。

ポータルでこのプロパティを使用して、テレメトリをフィルター処理したりセグメント化したりすることができます。 この例では [障害] ブレードがフィルター処理され、フロントエンド Web サービスからの情報だけが表示されます。CRM API バックエンドの障害は除外されます。

![クラウド ロール名によってセグメント化されたメトリック グラフ](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-roles"></a>ロール間の操作をトレースする

個々の操作の処理中に行われた、1 つのサービスから別のサービスへの呼び出しをトレースすることができます。


![操作のテレメトリを表示する](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

クリックして、フロントエンド Web サーバーとバックエンド API を対象に、この操作に関連するテレメトリの一覧を表示します。

![複数のロールにわたって検索する](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>次のステップ

* [開発、テスト、および運用のテレメトリを分離する](app-insights-separate-resources.md)

