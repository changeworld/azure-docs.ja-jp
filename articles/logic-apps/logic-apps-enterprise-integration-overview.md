---
title: B2B エンタープライズ統合
description: Azure Logic Apps と Enterprise Integration Pack を使用したエンタープライズ統合の自動化された B2B ワークフローの構築について説明します。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 05/11/2021
ms.openlocfilehash: 08de00415229b410e7b88bc4cae26754c466baef
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2021
ms.locfileid: "109789085"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>Azure Logic Apps と Enterprise Integration Pack を使用した B2B エンタープライズ統合ソリューション

組織の間に企業間 (B2B) ソリューションとシームレスな通信を実現するために、[Azure Logic Apps](../logic-apps/logic-apps-overview.md) で Enterprise Integration Pack (EIP) を使用して、自動化されたスケーラブルなエンタープライズ統合ワークフローを構築することができます。 組織どうしは、異なるプロトコルと形式を使用していても、メッセージを電子的に交換することができます。 異なる形式は、EIP によって、組織のシステムで処理できる、[AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md)、[EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md) などの業界標準プロトコルをサポートする形式に変換されます。 また、暗号化とデジタル署名の両方を使用してメッセージのセキュリティを向上させることもできます。 EIP では、これらの[エンタープライズ統合コネクタ](../connectors/managed.md#enterprise-connectors)と次の業界標準がサポートされています。

* 電子データ交換 (EDI)
* Enterprise Application Integration (EAI)

Microsoft BizTalk Server または Azure BizTalk Services を使い慣れている場合、EIP は同様の概念に従っており、機能を簡単に使用できます。 ただし、1 つの大きな違いとして、EIP はアーキテクチャ上、B2B 通信で使用されるアーティファクトの格納と管理を簡単にするために、"統合アカウント" に基づいています。 これらのアカウントは、パートナー、契約、スキーマ、マップ、証明書などのアーティファクトをすべて格納するクラウド ベースのコンテナーです。

## <a name="why-use-the-enterprise-integration-pack"></a>Enterprise Integration Pack を使用する理由

* EIP を使用すれば、対象のアーティファクトをすべて 1 か所 (統合アカウント) に格納できます。

* Azure Logic Apps とコネクタを使用することで、B2B ワークフローを構築し、サード パーティの SaaS (サービスとしてのソフトウェア) アプリ、オンプレミスのアプリ、カスタム アプリと統合できます。

* Azure Functions を使用して、ロジック アプリのカスタム コードを作成できます。

## <a name="how-do-i-get-started"></a>開始するには?

EIP を使用した B2B ロジック アプリ ワークフローの構築を開始する前に、次の項目が必要です。

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* 使用するアーティファクトを含む[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* マップとスキーマを作成するには、[Microsoft Azure Logic Apps Enterprise Integration Tools 拡張機能](https://aka.ms/vsenterpriseintegrationtools)と Visual Studio 2019 を使用できます。 

   > [!IMPORTANT]
   > BizTalk Server 拡張機能とこの拡張機能を同時にインストールしないでください。 両方の拡張機能をインストールすると、予期しない動作が発生する可能性があります。 これらの拡張機能のいずれかのみがインストールされていることを確認してください。

   > [!NOTE]
   > 高解像度モニターでは、Visual Studio の[マップ デザイナーで表示の問題](/visualstudio/designers/disable-dpi-awareness)が発生することがあります。 この表示の問題を解決するには、[Visual Studio を DPI 非対応モードで再起動する](/visualstudio/designers/disable-dpi-awareness#restart-visual-studio-as-a-dpi-unaware-process)か、[DPIUNAWARE レジストリ値](/visualstudio/designers/disable-dpi-awareness#add-a-registry-entry)を追加します。


* Visual Studio 2015 を使用している場合は、[Microsoft Azure Logic Apps Enterprise Integration Tools for Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas) 拡張機能を使用できます。

統合アカウントを作成し、アーティファクトを追加したら、Azure portal でロジック アプリを作成することにより、これらのアーティファクトを使用して B2B ワークフローの構築を開始できます。 ロジック アプリを初めて使用する場合は、[基本的なロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)してみてください。 ただし、これらのアーティファクトを使用するには、統合アカウントとロジック アプリの関連付けを行っておく必要があります。 その後、ロジック アプリから統合アカウントにアクセスできます。 また、Visual Studio または [PowerShell](/powershell/module/az.logicapp) を使用して、ロジック アプリを作成、管理、デプロイすることもできます。

B2B ロジック アプリの構築を開始する手順の概要を次に示します。

![B2B ロジック アプリを作成するための前提条件](./media/logic-apps-enterprise-integration-overview/overview.png)

## <a name="try-now"></a>今すぐ試す

[AS2 メッセージの送受信を行う完全に動作するサンプル ロジック アプリをデプロイする](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.logic/logic-app-as2-send-receive)

## <a name="next-steps"></a>次のステップ

* [取引先を作成する](logic-apps-enterprise-integration-partners.md)
* [契約を作成する](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [スキーマを追加する](logic-apps-enterprise-integration-schemas.md)
* [マップを追加する](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [BizTalk Services から移行する](../logic-apps/logic-apps-move-from-mabs.md)
