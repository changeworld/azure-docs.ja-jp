---
title: Azure Kubernetes Service (AKS) 上のアプリケーションを Application Insights で監視する - Azure Monitor | Microsoft Docs
description: Azure Monitor は、Kubernetes で実行されているアプリケーションとシームレスに統合され、アプリケーションの問題を即座に見つけることができます。
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 76f9f922697ef6be6c959ea7f9bafd0872dba6ff
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773770"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Kubernetes に対するゼロ インストルメンテーション アプリケーション監視 - Azure Monitor Application Insights

> [!IMPORTANT]
>  現時点では、コードをインストルメント化しなくても、Kubernetes で実行されている Java アプリの監視を有効にすることができます。[Java スタンドアロン エージェント](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)を使用します。 アプリケーションの監視をシームレスに有効にするソリューションは他の言語でも機能しますが、AKS で実行されているアプリを監視するには以下の SDK を使用します。[ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)、[ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)、[Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs)、[JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)、および [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)。

## <a name="application-monitoring-without-instrumenting-the-code"></a>コードのインストルメント化を伴わないアプリケーションの監視
現時点では、Java のみで、コードをインストルメント化しなくてもアプリケーションの監視を有効にすることができます。 他の言語でアプリケーションを監視するには、SDK を使用します。 

## <a name="java"></a>Java
Java エージェントは、有効になると、最も広く使用されているライブラリとフレームワークから多数の要求、依存関係、ログ、およびメトリックを自動的に収集します。

Kubernetes アプリやその他の環境で実行されている Java アプリを監視するには、[詳細な手順](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)に従います。 

## <a name="other-languages"></a>その他の言語

他の言語のアプリケーションでは、現在、以下の SDK を使用することをお勧めしています。
* [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
* [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
* [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs) 
* [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
* [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

## <a name="next-steps"></a>次のステップ

* [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) と [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) の詳細を学習する
* [分散トレース](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)の概要を把握し、[アプリケーション マップ](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net)を業務で活用する方法を確認する