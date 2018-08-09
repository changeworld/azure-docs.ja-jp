---
title: Azure での管理ソリューションのビルド | Microsoft Docs
description: 管理ソリューションには、Azure のパッケージ化された管理シナリオが含まれており、お客様はそれを Log Analytics ワークスペースに追加できます。  この記事では、管理ソリューションを作成してお使いの環境で使用したり顧客に提供したりする方法について、詳しく説明します。
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a07a17105b4d84b51689e9636cfacc7a3b5428ad
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528029"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Azure での管理ソリューションの設計とビルド (プレビュー)
> [!NOTE]
> 本記事は、現在プレビュー段階である Azure の管理ソリューションの作成に関する暫定版ドキュメントです。 本記事で説明するスキーマは、変更されることがあります。

[管理ソリューション]( monitoring-solutions.md)はパッケージ化された管理シナリオを提供します。お客様はそれらを Log Analytics ワークスペースに追加できます。  この記事では、最も一般的な要件に適した管理ソリューションを設計および構築する基本的な手順について説明します。  管理ソリューションを初めて構築する場合は、このプロセスを出発点として使用し、要件の変化に応じてより複雑なソリューションの概念を活用できます。

## <a name="what-is-a-management-solution"></a>管理ソリューションとは

管理ソリューションには、連携して特定の管理シナリオを実現する Azure のリソースが含まれています。  管理ソリューションは、[リソース管理テンプレート](../azure-resource-manager/resource-manager-template-walkthrough.md)として実装されます。テンプレートには、ソリューションがインストールされたときに、含まれているリソースをインストールして構成する方法の詳細が含まれています。

管理ソリューションの構築は、基本的には、Azure 環境内で個々のコンポーネントを作成することから開始します。  正常に機能したら、それらのコンポーネントを[管理ソリューション ファイル]( monitoring-solutions-solution-file.md)にパッケージ化できます。 


## <a name="design-your-solution"></a>ソリューションの設計
管理ソリューションの最も一般的なパターンを次の図に示します。  このパターンのさまざまなコンポーネントについては、以下で説明します。

![管理ソリューションの概要](media/monitoring-solutions-creating/solution-overview.png)


### <a name="data-sources"></a>データ ソース
ソリューションを設計する最初の手順では、Log Analytics リポジトリから取得する必要があるデータを決定します。  このデータは[データ ソース](../log-analytics/log-analytics-data-sources.md)や[別のソリューション]( monitoring-solutions.md)から収集できます。また、データを収集するプロセスを自分のソリューションに追加することが必要な場合もあります。

「[Log Analytics のデータ ソース](../log-analytics/log-analytics-data-sources.md)」で説明しているように、Log Analytics リポジトリではさまざまなデータ ソースからデータを収集できます。  これには、Windows イベント ログのイベントや、Syslog によって生成されたイベントのほか、Windows と Linux の両方のクライアントのパフォーマンス カウンターがあります。  また、Azure Monitor によって収集された Azure のリソースからデータを収集することもできます。  

利用できるどのデータ ソースからもアクセスできないデータが必要な場合は、[HTTP データ コレクター API](../log-analytics/log-analytics-data-collector-api.md) を使用できます。この API を使用すると、REST API を呼び出すことができる任意のクライアントから Log Analytics リポジトリにデータを書き込むことができます。  管理ソリューションにおけるカスタム データ収集の最も一般的な方法は、Azure または外部のリソースから必要なデータを収集し、データ コレクター API を使用してリポジトリに書き込む [Azure Automation の Runbook](../automation/automation-runbook-types.md) を作成することです。  

### <a name="log-searches"></a>ログ検索
[ログ検索](../log-analytics/log-analytics-log-searches.md)は、Log Analytics リポジトリ内のデータの抽出および分析に使用します。  ビューやアラートで使用されるほか、ユーザーがリポジトリ内のデータに対してアドホック分析を実行することもできます。  

ビューやアラートで使用されていない場合でも、ユーザーにとって便利だと思われるすべてのクエリを定義する必要があります。  こうしたクエリはポータルで保存された検索として利用でき、カスタム ビューの [[クエリのリスト] 視覚エフェクト パーツ](../log-analytics/log-analytics-view-designer-parts.md#list-of-queries-part)に含めることもできます。

### <a name="alerts"></a>アラート
[Log Analytics のアラート](../log-analytics/log-analytics-alerts.md)では、リポジトリ内のデータに対する[ログ検索](#log-searches)によって問題を特定します。  アラートが発生した場合、ユーザーに通知するか、アクションを自動的に実行します。 アプリケーションのさまざまなアラートの状態を特定し、対応するアラート ルールをソリューション ファイルに含める必要があります。

自動化されたプロセスで問題を修正できる可能性がある場合は、通常、その修復を実行する Azure Automation の Runbook を作成します。  ほとんどの Azure サービスは、[コマンドレット](/powershell/azure/overview)を使用して管理できます。Runbook では、コマンドレットを活用して、そうした機能を実行します。

アラートに対する応答でソリューションに外部の機能が必要な場合は、[webhook の応答](../log-analytics/log-analytics-alerts-actions.md)を使用できます。  これにより、アラートから情報を送信する外部 Web サービスを呼び出すことができます。

### <a name="views"></a>ビュー
Log Analytics のビューは、Log Analytics リポジトリのデータを視覚化するために使用します。  各ソリューションには、通常、ユーザーのメイン ダッシュボードに表示される[タイル](../log-analytics/log-analytics-view-designer-tiles.md)を使用した 1 つのビューを含めます。  ビューには任意の数の[視覚エフェクト パーツ](../log-analytics/log-analytics-view-designer-parts.md)を含めて、収集したデータをさまざまな形で視覚化してユーザーに表示できます。

[ビュー デザイナーを使用してカスタム ビューを作成](../log-analytics/log-analytics-view-designer.md)し、後でエクスポートしてソリューション ファイルに含めることができます。  


## <a name="create-solution-file"></a>ソリューション ファイルの作成
ソリューションのコンポーネントを構成し、テストしたら、[ソリューション ファイルを作成]( monitoring-solutions-solution-file.md)できます。  ファイル内の他のリソースとリレーションシップがある[ソリューション リソース]( monitoring-solutions-solution-file.md#solution-resource)を含む [Resource Manager テンプレート](../azure-resource-manager/resource-group-authoring-templates.md)にソリューションのコンポーネントを実装します。  


## <a name="test-your-solution"></a>ソリューションのテスト
ソリューションを開発している場合は、ワークスペースにインストールしてテストする必要があります。  これには、[Resource Manager テンプレートをテストおよびインストール](../azure-resource-manager/resource-group-template-deploy.md)できる方法であればどれでも使用できます。

## <a name="publish-your-solution"></a>ソリューションを発行する
ソリューションが完成してテストしたら、次のソースを使用して、顧客がソリューションを利用できるようにします。

- **Azure クイック スタート テンプレート**。  [Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/)は、GitHub を通じてコミュニティから提供された一連の Resource Manager テンプレートです。  [投稿に関するガイド](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE)の情報に従って、ソリューションを利用できるようにすることができます。
- **Azure Marketplace**。  [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) では、ソリューションを他の開発者、ISV、IT プロフェッショナルに配布および販売することができます。  Azure Marketplace にソリューションを発行する方法については、「[Azure Marketplace でプランを発行して管理する方法](../marketplace/marketplace-publishers-guide.md)」を参照してください。



## <a name="next-steps"></a>次の手順
* 管理ソリューションの[ソリューション ファイルの作成]( monitoring-solutions-solution-file.md)方法について
* [Azure Resource Manager のテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)の詳細について
* Resource Manager テンプレートの様々なサンプルは、[Azure クイックスタート テンプレート](https://azure.microsoft.com/documentation/templates) で検索できます。