---
title: アプリ開発を増やし、インフラ管理を減らす
description: Azure サーバーレスは、Azure Logic Apps と Azure Functions を使用する際に、インフラストラクチャの管理にかかる費用を抑えながら、クラウドベースのアプリケーション作成に集中する
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 07/15/2021
ms.openlocfilehash: 335a553897f2487eef317852f23cd475a92b2f81
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114362682"
---
# <a name="azure-serverless-overview-create-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure サーバーレスの概要: Azure Logic Apps と Azure Functions でクラウドベースのアプリやソリューションを作成する

サーバーレスは「サーバーなし」を意味しませんが、Azure サーバーレスはインフラストラクチャの管理に費やす時間を減らします。 従来のアプリ開発では、アプリの要件や要求を満たすために、ホスティング、スケーリング、モニタリングのソリューションについて議論し、対処することに多くの時間とエネルギーを費やすことができます。 サーバーレス アプリとソリューションを使用すると、アプリまたはソリューションの一部としてこれらの懸念事項を簡単に処理できます。 サーバーレスには、開発の高速化、最小限のコード、簡素化、柔軟なスケーリングなど、その他の利点があります。 これらすべての機能により、ビジネス ロジックに集中できます。 また、サーバーレスは通常、使用量に基づいて課金または課金されます。 そのため、使用しない場合、料金は発生しません。 詳細については、[Azure サーバーレス](https://azure.microsoft.com/solutions/serverless/)を参照してください。

このアーティクルでは、Azure で提供されているサーバーレスの中核となる「Azure Logic Apps」と「Azure Functions」について簡単にまとめています。 どちらのサービスも、前述の原則と一致し、最小限のコードで堅牢なクラウド アプリとソリューションを構築するのに役立ちます。

入門情報については、Azure のページ [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)と、[Azure Functions](https://azure.microsoft.com/services/functions/)を参照してください。 詳細については、[Azure Logic Apps とは](logic-apps-overview.md)と、[Azure Functions とは](../azure-functions/functions-overview.md)のドキュメント ページを参照してください。

## <a name="azure-logic-apps"></a>Azure Logic Apps

このサービスは、クラウドで実行されスケーリングする自動化されたイベント駆動型統合ワークフローを設計、開発、調整する簡単な方法を提供します。 この Azure Logic Apps ビジュアル デザイナーを使用して、ビジネス プロセスをワークフローとして迅速にモデル化できます。 ワークフローは常に最初の手順としてトリガーで始まります。 トリガーに従って、1 つ以上のアクションによってワークフロー内の後続の操作が実行されます。 これらの操作には、条件付きロジックやデータ変換など、さまざまなアクションを組み合わせられます。

コードを記述せずにワークフローを他の Azure サービス、Microsoft サービス、クラウドベースの環境、オンプレミス環境に接続するには、[数百種類のコネクタ](/connectors/connector-reference/connector-reference-logicapps-connectors/) (すべて Microsoft が管理) から選択して、あらかじめ組み込まれたトリガーやアクションをワークフローに追加できます。 各コネクタは、実際には API のプロキシまたはラッパーで、基礎となるサービスと Azure Logic Apps との通信を可能にします。 たとえば、**新しい電子メールが届いたとき** という名前の Office 365 Outlook トリガーを使用したいとします。 サーバーレス アプリとソリューションの場合は、Azure Logic Apps を使って、Azure Functions で作成した複数の関数を調整できます。 これにより、特に外部の API やシステムとの連携が必要な処理では、様々な機能を 1 つのプロセスとして簡単に呼び出すことができます。

ニーズを満たすために使用できるコネクタがない場合は、組み込みの HTTP 操作または要求トリガーを使用して、任意のサービス エンドポイントと通信できます。 または、既存の API を使用して独自のコネクタを作成することもできます。

選択したロジック アプリのリソースの種類に応じて、関連するワークフローは、マルチテナント型の Azure Logic Apps、シングルテナント型の Azure Logic Apps、または専用の統合サービス環境 (ISE) のいずれかで実行されます。 それぞれに独自の機能、利点、課金モデルがあります。 Azure portal は、ロジック アプリ ワークフローの作成を開始するための最速の方法を提供します。 ただし、他のツール (Visual Studio Code、Visual Studio、Azure PowerShell など) も使用できます。 詳細については、[Azure Logic Apps とは](logic-apps-overview.md)を参照してください。

Azure Logic Apps の概要については、[Microsoft Azure portal で初めてロジック アプリ ワークフローを作成するクイックスタート](quickstart-create-first-logic-app-workflow.md)を参照してください。 または、[Visual Studio で Azure Logic Apps と Azure Functions を使ったサーバーレスアプリの例を作成する](create-serverless-apps-visual-studio.md)を試してみてください。

詳細については、次のドキュメントを参照してください。

* [Azure Logic Apps とは](logic-apps-overview.md)
* [Azure Logic Apps のカスタム コネクタについて](../connectors/apis-list.md)
* [コネクタ - Azure Logic Apps、Microsoft Power Automate、Microsoft Power Apps](/connectors/connectors)
* [Azure Logic Apps でのシングルテナント、マルチテナント、統合サービス環境の比較](single-tenant-overview-compare.md)
* [Azure Logic Apps の使用量計測、課金、価格設定モデル](logic-apps-pricing.md)

## <a name="azure-functions"></a>Azure Functions

このサービスは、コードや *機能* の一部をクラウド上で書いて実行するためのシンプルな方法を提供します。 完全なアプリや必要なインフラストラクチャを設定せずに、現在の問題に必要なコードのみを記述できます。これにより、開発の速度と生産性が向上します。 C#、Java、JavaScript、PowerShell、Python、TypeScript などの開発言語を選択できます。 支払いはコードの実行時間に対してだけであり、Azure が必要に応じてスケールします。

Azure Functions を使い始めるには、[Microsoft Azure portal で最初の Azure 関数を作成する](../azure-functions/functions-create-function-app-portal.md)を参照してください。

詳細については、次のドキュメントを参照してください。

* [Azure Functions とは](../azure-functions/functions-overview.md)
* [Azure Functions の概要](../azure-functions/functions-get-started.md)
* [Azure Functions でサポートされている言語](../azure-functions/supported-languages.md)
* [Azure Functions のホスティング オプション](../azure-functions/functions-scale.md)
* [Azure Functions の価格](../azure-functions/pricing.md)

## <a name="get-started-with-serverless-apps-in-azure"></a>Azure でサーバーレス アプリケーションを始める

Azure には、サーバーレス アプリの開発、デプロイ、管理を行うための豊富なツールが用意されています。 サーバーレス アプリは、Azure portal、Visual Studio、または [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md) で作成できます。 アプリをビルドしたら、[Azure Resource Manager テンプレートを使用してそのアプリをすばやくデプロイ](logic-apps-deploy-azure-resource-manager-templates.md)できます。 Azure では、Azure portal からのアクセス、API または SDK 経由のアクセス、または Azure Monitor ログおよび Application Insights に対する統合ツールでのアクセスが可能な監視が提供されています。

## <a name="next-steps"></a>次のステップ

* [Visual Studio で Azure Logic Apps と Azure Functions を使ったサーバーレスアプリの例を作成する](create-serverless-apps-visual-studio.md)
* [サーバーレスを使用して Customer Insights ダッシュボードを作成する](logic-apps-scenario-social-serverless.md)