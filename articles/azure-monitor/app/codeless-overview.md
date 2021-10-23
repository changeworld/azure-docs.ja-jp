---
title: コードを変更せずにアプリを監視する - Azure Monitor Application Insights の自動インストルメンテーション |Microsoft Docs
description: Azure Monitor Application Insights の自動インストルメンテーションの概要 - コード不要のアプリケーションのパフォーマンス管理
ms.topic: conceptual
ms.date: 08/31/2021
ms.openlocfilehash: 8603837d9d9ac098f7093a8760f326a1c0e44f89
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133635"
---
# <a name="what-is-auto-instrumentation-for-azure-monitor-application-insights"></a>Azure Monitor Application Insights の自動インストルメンテーションとは

自動インストルメンテーションを使用すると、コードを変更することなく Application Insights を使用したアプリケーションの監視を有効にすることができます。  

Application Insights は、さまざまなリソース プロバイダーと統合されており、さまざまな環境で動作します。 基本的には、エージェントを有効にし、場合によっては構成するだけで済みます。これによりテレメトリは自動的に収集されます。 すぐに、Application Insights リソースにメトリック、要求、依存関係が表示されます。これにより、潜在的な問題が発生する前に原因を特定し、エンドツーエンドのトランザクション ビューを使用して根本原因を分析することができます。

## <a name="supported-environments-languages-and-resource-providers"></a>サポートされている環境、言語、リソース プロバイダー

新たな統合を追加していくと、自動インストルメンテーション機能のマトリックスは複雑になります。 次の表は、さまざまなリソース プロバイダー、言語、環境のサポートに関して、現在の状態を示しています。

|環境/リソース プロバイダー          | .NET            | .NET Core       | Java            | Node.js         | Python          |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|
|Azure App Service on Windows           | GA、OnBD*       | GA、オプトイン      | パブリック プレビュー  | パブリック プレビュー  | サポートされていません   |
|Azure App Service on Linux             | 該当なし             | サポートされていません   | GA              | GA              | サポートされていません   |
|Azure Functions - 基本                | GA、OnBD*       | GA、OnBD*       | GA、OnBD*       | GA、OnBD*       | GA、OnBD*       |
|Azure Functions - 依存関係         | サポートされていません   | サポートされていません   | パブリック プレビュー  | サポートされていません   | [拡張機能](monitor-functions.md#distributed-tracing-for-python-function-apps)を介する   |
|Azure Spring Cloud                     | サポートされていません   | サポートされていません   | パブリック プレビュー  | サポートされていません   | サポートされていません   |
|Azure Kubernetes Service               | 該当なし             | サポートされていません   | エージェント経由   | サポートされていません   | サポートされていません   |
|Azure VM Windows                      | パブリック プレビュー  | サポートされていません   | エージェント経由   | サポートされていません   | サポートされていません   |
|オンプレミスの VM Windows                | GA、オプトイン      | サポートされていません   | エージェント経由   | サポートされていません   | サポートされていません   |
|スタンドアロン エージェント - 任意の環境            | サポートされていません   | サポートされていません   | GA              | サポートされていません   | サポートされていません   |

*OnBD は On by Default (既定でオン) の短縮形です。サポートされている環境でアプリをデプロイすると、Application Insights が自動的に有効になります。 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

Azure App Service on Windows でのアプリケーション監視は、 **[ASP.NET](./azure-web-apps-net.md)** (既定で有効)、 **[ASP.NET Core](./azure-web-apps-net-core.md)** 、 **[Java](./azure-web-apps-java.md)** (パブリック プレビュー)、 **[Node.js](./azure-web-apps-nodejs.md)** アプリケーションで使用できます。 Python アプリを監視するには、[SDK](./opencensus-python.md) をコードに追加します。

> [!NOTE]
> Windows の場合、App Service におけるアプリケーションの監視は、現在コードベースまたはマネージド サービスで使用できます。 App Service における Windows コンテナーのアプリの監視は、Application Insights との統合を通してまだサポートされていません。

### <a name="linux"></a>Linux
App Service 内で Linux で実行されている **[Java](./azure-web-apps-java.md?)** 、 **[Node.js](./azure-web-apps-nodejs.md?tabs=linux)** 、および **[ASP.NET Core](./azure-web-apps-net-core.md?tabs=linux) (プレビュー)** アプリの監視は、ポータルを使用して有効にできます。 

[Python](./opencensus-python.md) の場合は、SDK を使用します。

## <a name="azure-functions"></a>Azure Functions

Azure Functions の基本的な監視は、ログ、パフォーマンス、エラー データ、HTTP 要求を収集するために、既定で有効になっています。 Java アプリケーションでは、分散トレースを使用した高度な監視を有効にし、エンドツーエンドのトランザクションの詳細を取得することができます。 Java 用のこの機能は Windows でパブリック プレビュー段階であり、[Azure portal で有効にする](./monitor-functions.md)ことができます。

## <a name="azure-spring-cloud"></a>Azure Spring Cloud

### <a name="java"></a>Java 
Azure Spring Cloud で実行されている Java アプリのアプリケーション監視はポータルに統合されており、Azure portal から直接 Application Insights を有効にできます。これは既存および新規作成のどちらの Azure Spring Cloud リソースにも当てはまります。  

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Azure Kubernetes Service のコード不要のインストルメンテーションは、現在、[スタンドアロン エージェント](./java-in-process-agent.md)を介して Java アプリケーションで利用できます。 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Azure Windows VM と仮想マシン スケール セット

Azure VM と仮想マシン スケール セットの自動インストルメンテーションを [.NET](./azure-vm-vmss-apps.md) および [Java](./java-in-process-agent.md) で使用できます。このエクスペリエンスは、ポータルに統合されていません。 監視は、スタンドアロン ソリューションを使用していくつかの手順で有効にされ、コードを変更する必要はありません。  

## <a name="on-premises-servers"></a>オンプレミスのサーバー
[.NET アプリケーション](./status-monitor-v2-overview.md)用および [Java アプリ](./java-in-process-agent.md)用のオンプレミスの Windows サーバーの監視を、簡単に有効にすることができます。

## <a name="other-environments"></a>その他の環境
多用途の Java スタンドアロン エージェントはあらゆる環境で動作しますが、コードをインストルメント化する必要はありません。 [ガイド](./java-in-process-agent.md)に従って Application Insights を有効にし、Java エージェントのすばらしい機能について確認してください。 エージェントはパブリック プレビュー段階にあり、すべてのリージョンで利用できます。 

## <a name="next-steps"></a>次のステップ

* [Application Insights の概要](./app-insights-overview.md)
* [アプリケーション マップ](./app-map.md)
* [エンドツーエンドのパフォーマンスの監視](../app/tutorial-performance.md)
