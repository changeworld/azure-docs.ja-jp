---
title: B2B エンタープライズ統合のワークフロー
description: Azure Logic Apps と Enterprise Integration Pack を使用して、エンタープライズ統合用の自動化された B2B ワークフローを構築する方法について説明します。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: overview
ms.date: 09/14/2021
ms.openlocfilehash: 91ea9eb37e7c4e2e97513a1496a52d2521e9f4b2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128652143"
---
# <a name="b2b-enterprise-integration-workflows-with-azure-logic-apps-and-enterprise-integration-pack"></a>Azure Logic Apps と Enterprise Integration Pack を使用した B2B エンタープライズ統合ワークフロー

組織の間に企業間 (B2B) ソリューションとシームレスな通信を実現するために、[Azure Logic Apps](logic-apps-overview.md) と Enterprise Integration Pack (EIP) を使用して、自動化されたスケーラブルなエンタープライズ統合ワークフローを構築することができます。

## <a name="what-is-the-enterprise-integration-pack"></a>Enterprise Integration Pack とは

Microsoft BizTalk Server または Azure BizTalk Services を使い慣れている場合、EIP は同様の概念に従っているため、B2B 機能を簡単に使用できます。 ただし、大きな違いの 1 つは、EIP のアーキテクチャは "*統合アカウント*" に基づいていることです。 これらのアカウントは、Azure のクラウドベースのコンテナーで、B2B のコミュニケーションのための B2B 成果物 ([パーティー](logic-apps-enterprise-integration-partners.md)、[契約](logic-apps-enterprise-integration-agreements.md)、[マップ](logic-apps-enterprise-integration-maps.md)、[スキーマ](logic-apps-enterprise-integration-schemas.md)、[証明書](logic-apps-enterprise-integration-certificates.md) など) の保管、管理、および使用方法を簡略化します。

これらの成果物を使用して、Azure、Microsoft、その他のサービスとしてのソフトウェア (SaaS) アプリなどのクラウド サービス、オンプレミスのシステム、[400 以上の組み込み操作とマネージド コネクタ](/connectors/connector-reference/connector-reference-logicapps-connectors)を持つ Azure Logic Apps を使用したカスタム アプリを含む、B2B ワークフローと統合ソリューションを構築できます。 たとえば、組み込みのコード実行操作と Azure Functions を使用して、ワークフローからカスタム コードを作成して実行できます。 次の業界標準をサポートする[エンタープライズ統合コネクタ](../connectors/managed.md#enterprise-connectors)を使用することもできます。

* 電子データ交換 (EDI)
* Enterprise Application Integration (EAI)

B2B 通信にはさまざまなプロトコルとフォーマットが使用されますが、他の組織とメッセージを電子的に交換することもできます。 これらのさまざまなフォーマットを、EIP を使用して、自分の組織のシステムで処理できるフォーマットに変換できます。EIP では、[AS2](logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md)、[EDIFACT](logic-apps-enterprise-integration-edifact.md)、[RosettaNet](logic-apps-enterprise-integration-rosettanet.md) などの業界標準プロトコルがサポートされます。 また、暗号化とデジタル署名の両方を使用してメッセージのセキュリティを向上させることもできます。

## <a name="what-do-i-need-to-get-started"></a>開始するにあたって必要なもの

* Azure アカウントとサブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* 定義して使用する B2B 成果物を格納するための[統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)。

* [パーティー](logic-apps-enterprise-integration-partners.md)、[契約](logic-apps-enterprise-integration-agreements.md)、[マップ](logic-apps-enterprise-integration-maps.md)、[スキーマ](logic-apps-enterprise-integration-schemas.md)、[証明書](logic-apps-enterprise-integration-certificates.md)などの B2B 成果物。

* マップとスキーマを作成するには、[Microsoft Azure Logic Apps Enterprise Integration Tools 拡張機能](https://aka.ms/vsenterpriseintegrationtools)と Visual Studio 2019 を使用できます。 Visual Studio 2015 を使用している場合は、[Microsoft Azure Logic Apps Enterprise Integration Tools for Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas) 拡張機能を使用できます。

   > [!IMPORTANT]
   > BizTalk Server 拡張機能とこの拡張機能を同時にインストールしないでください。 両方の拡張機能をインストールすると、予期しない動作が発生する可能性があります。 これらの拡張機能のいずれかのみがインストールされていることを確認してください。

   > [!NOTE]
   > 高解像度モニターでは、Visual Studio の[マップ デザイナーで表示の問題](/visualstudio/designers/disable-dpi-awareness)が発生することがあります。 この表示の問題を解決するには、[Visual Studio を DPI 非対応モードで再起動する](/visualstudio/designers/disable-dpi-awareness#restart-visual-studio-as-a-dpi-unaware-process)か、[DPIUNAWARE レジストリ値](/visualstudio/designers/disable-dpi-awareness#add-a-registry-entry)を追加します。

統合アカウントを作成し、成果物を追加したら、ロジック アプリ リソースを作成することにより、B2B ワークフローの構築を開始できます。 ロジック アプリを初めて使用する場合は、[サンプルの基本的なロジック アプリ ワークフローを作成](quickstart-create-first-logic-app-workflow.md)してみてください。 また、[Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)、[Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)、または [PowerShell](/powershell/module/az.logicapp) を使用して、ロジック アプリを作成、管理、デプロイすることもできます。

> [!IMPORTANT]
> **ロジック アプリ (従量課金)** のリソースの種類を使用する場合、ワークフローで B2B 成果物を使用するには、統合アカウントをロジック アプリ リソースにリンクする必要があります。 ただし、これらの成果物を定義して統合アカウントに追加するには、ロジック アプリ リソースはまだ必要ありません。
>
> **ロジック アプリ (Standard)** のリソースの種類を使用する場合、ロジック アプリ リソースにスキーマとマップを直接追加し、*同じロジック アプリ リソース* 内の複数のワークフローでそれらの成果物を使用できます。 
> パートナーや契約などの他の成果物を格納するための統合アカウントは引き続き必要ですが、リンクは不要になったため、この機能は存在しません。 これらのリソースの種類の詳細については、[Azure Logic Apps とは - リソースの種類とホスト環境](logic-apps-overview.md#resource-type-and-host-environment-differences)に関する記事を参照してください。

次の図は、B2B ロジック アプリのワークフローの構築を開始する手順の概要を示しています。

![B2B ロジック アプリのワークフローを作成するための前提手順を示す概念図。](media/logic-apps-enterprise-integration-overview/overview.png)

## <a name="try-now"></a>今すぐ試す

[AS2 メッセージの送受信を行う完全に動作するサンプル ロジック アプリをデプロイする](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.logic/logic-app-as2-send-receive)

## <a name="next-steps"></a>次のステップ

* [取引先を作成する](logic-apps-enterprise-integration-partners.md)
* [契約を作成する](logic-apps-enterprise-integration-agreements.md)
* [スキーマを追加する](logic-apps-enterprise-integration-schemas.md)
* [マップを追加する](logic-apps-enterprise-integration-maps.md)
* [BizTalk Services から移行する](logic-apps-move-from-mabs.md)
