---
title: Azure Logic Apps の概要
description: Logic Apps クラウド プラットフォームは、コードをほとんどまたはまったく使用せずに、エンタープライズ統合シナリオ向けの自動化されたワークフローの作成と実行を支援します。
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: overview
ms.custom: mvc
ms.date: 04/26/2021
ms.openlocfilehash: 78a1d577be21334a14008b18bb777f3d79c2a5e1
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108287196"
---
# <a name="what-is-azure-logic-apps"></a>Azure Logic Apps とは

[Logic Apps](https://azure.microsoft.com/services/logic-apps) は、アプリ、データ、サービス、システムを統合するための自動化された[ワークフロー](#logic-app-concepts)の作成と実行を支援するクラウドベースのプラットフォームです。 このプラットフォームを使用すると、エンタープライズおよび企業間 (B2B) シナリオ向けのスケーラビリティの高い統合ソリューションをより簡単かつ迅速に構築できます。 [Azure Integration Services](https://azure.microsoft.com/product-categories/integration/) のメンバーである Logic Apps は、クラウド、オンプレミス、ハイブリッド環境で、レガシ、最新、および最先端システムをより簡単に接続する方法を提供します。

この一覧に例として挙げたのは、Logic Apps サービスを使用して自動化できるタスク、ビジネス プロセス、ワークロードのうちのごく一部です。

* Office 365 を使用して、特定のイベント (新しいファイルのアップロードなど) が発生したときの電子メール通知をスケジュールして送信する。
* オンプレミス システムとクラウド サービスの垣根を越えて顧客注文をルーティングし、処理する。
* アップロードされたファイルを SFTP サーバーまたは FTP サーバーから Azure Storage に移動する。
* ツイートを監視したり、そのセンチメントを分析したり、確認が必要な項目についてアラートやタスクを作成したりする。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Introducing-Azure-Logic-Apps/player]

さまざまなデータ ソースに対し、リアルタイムかつ安全にアクセスして操作を実行できるよう、日々拡充される [Microsoft マネージド コネクタ](#logic-app-concepts)の[ギャラリー](/connectors/connector-reference/connector-reference-logicapps-connectors)をご利用ください。たとえば次のようなものがあります。

* Azure サービス (Blob Storage、Service Bus など)
* Office サービス (Outlook、Excel、SharePoint など)
* データベース サーバー (SQL、Oracle など)
* エンタープライズ システム (SAP、IBM MQ など)
* ファイル共有 (FTP、SFTP など)

サービス エンドポイントとの通信、独自コードの実行、ワークフローの編成、データの操作のために、Logic Apps サービス内でネイティブに実行される[組み込みのトリガーとアクション](#logic-app-concepts)を使用できます。 たとえば、組み込みトリガーとしては、要求トリガー、HTTP トリガー、繰り返しトリガーなどがあります。 組み込みアクションには、条件、For each、JavaScript コードに加えて、Azure でホストされる Azure 関数、Web アプリ、API アプリのほか、他の Logic Apps ワークフローを呼び出す操作などがあります。

Logic Apps には、B2B 統合シナリオ向けに [BizTalk Server](/biztalk/core/introducing-biztalk-server) の機能が含まれています。 [統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)を作成して、そこに取引先、契約、スキーマ、マップなどの B2B アーティファクトを定義することができます。 このアカウントをロジック アプリにリンクすると、これらのアーティファクトと連携し、AS2、EDIFACT、X12、RosettaNet などのプロトコルを使用してメッセージを交換するワークフローを構築できます。

ワークフローからアプリやデータ、サービス、システムにアクセスしてそれらと連携する方法について詳しくは、次のドキュメントを参照してください。

* [Azure Logic Apps のコネクタ](../connectors/apis-list.md)
* [Azure Logic Apps のマネージド コネクタ](../connectors/built-in.md)
* [Azure Logic Apps の組み込みのトリガーとアクション](../connectors/managed.md)
* [Azure Logic Apps を使用した B2B エンタープライズ統合ソリューション](logic-apps-enterprise-integration-overview.md)

<a name="logic-app-concepts"></a>

## <a name="key-terms"></a>キーワード

* **ワークフロー**: タスクまたはプロセスを定義する一連のステップ。単一のトリガーを起点として 1 つまたは複数のアクションを実行します

* **トリガー**: 各ワークフローの起点となると共に、ワークフローのアクションを実行するにあたって満たすべき条件を指定する最初のステップ。 たとえば、受信トレイにメールが届いた、ストレージ アカウントで新しいファイルを検出した、といったトリガー イベントが考えられます。

* **アクション**: トリガーの後、ワークフロー内のなんらかの操作を実行する後続の各ステップ。

* **マネージド コネクタ**: 特定のアプリ、データ、サービス、またはシステムへのアクセスを提供する、Microsoft が管理する REST API。 ほとんどのマネージド コネクタは、その使用前に、ワークフローから接続を作成し、ID の認証を行う必要があります。

  たとえば、トリガーを使用してワークフローを開始したり、Azure Blob Storage、Office 365、Salesforce、SFTP サーバーと連携するアクションを追加したりすることができます。 詳細については、[Azure Logic Apps のマネージド コネクタ](../connectors/managed.md)に関するページを参照してください。

* **組み込みのトリガーまたはアクション**: ネイティブで実行される Logic Apps 操作。ワークフローのスケジュールや構造の制御、独自のコードの実行、データの管理または操作、ワークフロー内の他のタスクの実行などを可能にする手段となります。 ほとんどの組み込み操作は、いずれのサービスやシステムにも関連付けられていません。 また、その多くは、ワークフローから接続を作成したり、ID の認証を行ったりする必要がありません。 Azure Functions、Azure API Management、Azure App Service など、いくつかのサービス、システム、プロトコルでも、組み込み操作を利用できます。

  たとえば、繰り返しトリガーを使用すると、ほぼどのようなワークフローでもスケジュールに従って開始できます。 また、要求トリガーを使用すると、呼び出すまでワークフローを待機させることもできます。 詳細については、[Azure Logic Apps の組み込みのトリガーとアクション](../connectors/built-in.md)に関するページを参照してください。

* **ロジック アプリ**: ワークフローを構築するために作成する Azure リソース。 シナリオのニーズとソリューションの要件に基づいて、マルチテナントまたはシングルテナント Logic Apps サービス環境で動作するロジック アプリ、または統合サービス環境で動作するロジック アプリを作成できます。 詳細については、[ロジック アプリのホスト環境](#host-environments)に関するセクションをご覧ください。

<a name="how-do-logic-apps-work"></a>

## <a name="how-logic-apps-work"></a>ロジック アプリが機能するしくみ

ロジック アプリでは、各ワークフローが必ず単一の[トリガー](#logic-app-concepts)で始まります。 トリガーは、条件が満たされたとき、たとえば特定のイベントが発生したときや特定の条件をデータが満たしたときに起動します。 多くのトリガーには、ワークフローの実行頻度を制御する[スケジューリング機能](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)が含まれています。 トリガーに続けて、1 つまたは複数の[アクション](#logic-app-concepts)によって各種操作 (ワークフロー内を通過するデータを加工、処理、変換する操作や、ワークフローを次のステップに進める操作など) が実行されます。

たとえば、次のワークフローは、"**When a record is updated (レコードが更新されたとき)** " という名前の組み込みの条件が指定された Dynamics トリガーで始まります。 そのアクションとして、XML の変換、データを更新する Web アプリの呼び出し、実行するアクションを制御する条件の評価、結果を含んだメール通知の送信などがあります。 条件を満たしたイベントがトリガーによって検出されると、トリガーが起動し、ワークフローに含まれているアクションの実行が開始されます。 トリガーが起動するたびに、そのアクションを実行するワークフロー インスタンスが Logic Apps サービスによって作成されます。

![Logic Apps デザイナー - ワークフローの例](./media/logic-apps-overview/azure-logic-apps-designer.png)

Azure portal、Visual Studio Code、または Visual Studio で Logic Apps デザイナーを使用して、ワークフローを視覚的に作成できます。 各ワークフローには、JavaScript Object Notation (JSON) を使用して記述された、基になる定義も含まれています。 必要に応じて、この JSON 定義を変更してワークフローを編集できます。 Logic Apps では、一部の作成タスクと管理タスクについて、Azure PowerShell と Azure CLI コマンドがサポートされます。 Logic Apps では、デプロイの自動化を目的として、Azure Resource Manager テンプレートがサポートされます。

<a name="host-environments"></a>

## <a name="host-environments"></a>ホスト環境

シナリオとソリューションの要件に基づいて、実行される Logic Apps サービス環境とワークフローでのリソースの使用方法が異なるロジック アプリを作成できます。 次の表は、それらの違いを簡単にまとめたものです。

| 環境 | [価格モデル](logic-apps-pricing.md) | 説明 |
|-------------|----------------------------------------|-------------|
| Azure Logic Apps (マルチテナント) | 従量課金 | ロジック アプリでワークフローを 1 つだけ使用できます。 <p><p>"*複数のテナント*" のさまざまなロジック アプリのワークフローで、同じ処理 (コンピューティング)、ストレージ、ネットワークなどが共有されます。 |
| Azure Logic Apps ([シングルテナント (プレビュー)](logic-apps-overview-preview.md)) | [プレビュー](logic-apps-overview-preview.md#pricing-model) | ロジック アプリで複数のワークフローを使用できます。 <p><p>"*単一のテナントの同じロジック アプリ*" のワークフローで、同じ処理 (コンピューティング)、ストレージ、ネットワークなどが共有されます。 |
| [統合サービス環境 (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) | 固定 | ロジック アプリでワークフローを 1 つだけ使用できます。 <p><p>"*同じ環境*" 内のさまざまなロジック アプリのワークフローで、同じ処理 (コンピューティング)、ストレージ、ネットワークなどが共有されます。 |
||||

Logic Apps サービス環境でホストされるロジック アプリにもさまざまな制限があります。 詳細については、[Logic Apps の制限](logic-apps-limits-and-config.md)に関する記事および [Logic Apps (プレビュー) の制限](logic-apps-overview-preview.md#limits)に関する記事をご覧ください。

## <a name="why-use-logic-apps"></a>Logic Apps を使用する理由

Logic Apps 統合プラットフォームでは、Microsoft が管理するあらかじめ構築された API コネクタと組み込み操作が提供されるので、アプリ、データ、サービス、システムをより簡単かつ迅速に接続し、統合できます。 リソースへのアクセス方法の理解ではなく、ソリューションのビジネス ロジックや機能の設計と実装に集中できます。

通常、コードを記述する必要はありません。 ただし、コードを記述する必要がある場合は、[Azure Functions](../azure-functions/functions-overview.md) を使用してコード スニペットを作成し、ワークフローからそのコードを実行できます。 また、[**インライン コード** アクション](logic-apps-add-run-inline-code.md)を使用して、ワークフローで実行するコード スニペットを作成することもできます。 ワークフローで、Azure サービス、カスタム アプリ、または他のソリューションのイベントを操作する必要がある場合は、[Azure Event Grid](../event-grid/overview.md) を使用してイベントを監視、ルーティング、発行できます。

Logic Apps は Microsoft Azure によって完全に管理されているので、これらのサービスを使用して構築されたソリューションのホスティング、スケーリング、管理、監視、メンテナンスについて心配する必要はありません。 これらの機能を使用して、["サーバーレス" のアプリやソリューション](../logic-apps/logic-apps-serverless-overview.md) を作成すると、ビジネス ロジックと機能に集中できます。 これらのサービスは、ニーズに合わせて自動的にスケーリングします。また、統合を迅速化するほか、コードをほとんどまたはまったく使用せずに堅牢なクラウド アプリを構築するのに役立ちます。

Logic Apps を他の Azure サービスや Microsoft 製品と組み合わせることによって、機敏性を高め、中核となるビジネスに集中できるようになった他社の事例については、これらの[お客様事例](https://aka.ms/logic-apps-customer-stories)をご覧ください。

以降のセクションでは、Logic Apps の機能と利点について詳しく説明します。

#### <a name="visually-create-and-edit-workflows-with-easy-to-use-tools"></a>使いやすいツールでワークフローを視覚的に作成、編集

Logic Apps のビジュアル デザイン ツールを使用して、時間を節約し、複雑なプロセスを簡素化します。 ワークフローは、Azure portal、Visual Studio Code、または Visual Studio で Logic Apps デザイナーを使用して最初から最後まで作成できます。 トリガーを使用してワークフローを開始し、[コネクタ ギャラリー](/connectors/connector-reference/connector-reference-logicapps-connectors)から任意の数のアクションを追加するだけです。

マルチテナント ロジック アプリを作成する場合は、[テンプレート ギャラリーからワークフローを作成](../logic-apps/logic-apps-create-logic-apps-from-templates.md)すると、作業を速やかに開始できます。 これらのテンプレートは、サービスとしてのソフトウェア (SaaS) アプリの単純な接続から、高度な B2B ソリューション、"遊び感覚" のテンプレートまで、一般的なワークフロー パターンで利用できます。

#### <a name="connect-different-systems-across-various-environments"></a>さまざまな環境でさまざまなシステムを接続

パターンやプロセスによっては、説明は簡単でもコードで実装するのは困難なこともあります。 Logic Apps プラットフォームは、クラウド、オンプレミス、ハイブリッド環境の異種システムをシームレスに接続するのに役立ちます。 たとえば、クラウド マーケティング ソリューションをオンプレミスの課金システムに接続したり、Azure Service Bus を使用して、複数の API やシステムのメッセージングを一元化したりできます。 Logic Apps は、これらのシナリオに対応する再利用可能かつ再構成可能なソリューションを提供するための、高速で信頼性の高い一貫した方法を提供します。

#### <a name="write-once-reuse-often"></a>1 回の作成で何度も再利用

複数の環境やリージョンにわたって[デプロイを設定して自動化](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)できるように、Azure Resource Manager テンプレートとしてロジック アプリを作成します。

#### <a name="first-class-support-for-enterprise-integration-and-b2b-scenarios"></a>エンタープライズ統合と B2B のシナリオに最上級のサポートを実現

企業や組織は、業界標準であっても異なるメッセージ プロトコルや形式 (EDIFACT、AS2、X12、RosettaNet など) を使用して、電子的に相互に通信しています。 Logic Apps でサポートされている[エンタープライズ統合機能](../logic-apps/logic-apps-enterprise-integration-overview.md)を使用すると、取引先が使用しているメッセージ形式を、自分の組織のシステムが解釈して処理できる形式に変換するワークフローを作成できます。 Logic Apps はこのようなやり取りを円滑に処理し、暗号化とデジタル署名を使ってその安全を確保します。

現在のシステムとサービスを使用して小規模から始め、自分のペースで段階的に拡大していくことができます。 準備ができたら、Logic Apps プラットフォームによって提供されるこれらの機能を使用して、より成熟した統合シナリオを実装し、スケールアップすることができます。

* [Microsoft BizTalk Server](/biztalk/core/introducing-biztalk-server)、[Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)、[Azure Functions](../azure-functions/functions-overview.md)、[Azure API Management](../api-management/api-management-key-concepts.md) などを統合して構築する。
* [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md)、[AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)、[X12](../logic-apps/logic-apps-enterprise-integration-x12.md)、[RosettaNet](logic-apps-enterprise-integration-rosettanet.md) の各プロトコルを使用してメッセージを交換する。
* [XML メッセージ](../logic-apps/logic-apps-enterprise-integration-xml.md)と[フラット ファイル](../logic-apps/logic-apps-enterprise-integration-flatfile.md)を処理する。
* [取引先](../logic-apps/logic-apps-enterprise-integration-partners.md)、[契約](../logic-apps/logic-apps-enterprise-integration-agreements.md)、[変換マップ](../logic-apps/logic-apps-enterprise-integration-maps.md)、[検証スキーマ](../logic-apps/logic-apps-enterprise-integration-schemas.md)などの B2B アーティファクトを保存および管理するための[統合アカウント](./logic-apps-enterprise-integration-create-integration-account.md)を作成する。

たとえば、Microsoft BizTalk Server を使用している場合は、ワークフローで [BizTalk Server コネクタ](../connectors/managed.md#on-premises-connectors)を使用して、BizTalk Server と通信できます。 その後、[統合アカウント コネクタ](../connectors/managed.md#integration-account-connectors)を使用して、ワークフローで BizTalk のような操作を実行または拡張できます。 逆に、BizTalk Server で [Microsoft BizTalk Server Adapter for Logic Apps](https://www.microsoft.com/download/details.aspx?id=54287) を使用して、ワークフローと通信することもできます。 BizTalk Server で [BizTalk Server Adapter を設定して使用する](/biztalk/core/logic-app-adapter)方法をご覧ください。

#### <a name="built-in-extensibility"></a>組み込みの拡張機能

必要なコードを実行するための適切なコネクタがない場合は、[Azure Functions](../azure-functions/functions-overview.md) を使用して、独自のコード スニペットを作成し、ワークフローから呼び出すことができます。 または、ワークフローから呼び出すことができる独自の [API](../logic-apps/logic-apps-create-api-app.md) や[カスタム コネクタ](../logic-apps/custom-connector-overview.md)を作成します。

#### <a name="access-resources-inside-azure-virtual-networks"></a>Azure 仮想ネットワーク内のリソースにアクセスする

"[*統合サービス環境*" (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) を作成するときに、ロジック アプリ ワークフローでは [Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)内にあるセキュリティで保護されたリソース (仮想マシン (VM) や他のシステムまたはサービスなど) にアクセスできます。 ISE は、専用のリソースを使用し、グローバルなマルチテナント Logic Apps サービスとは別に実行される、Logic Apps サービスの専用インスタンスです。

独自の専用インスタンスでロジック アプリを実行することで、他の Azure テナントがアプリのパフォーマンスに与える可能性がある影響 (["うるさい隣人" エフェクト](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)とも呼ばれます) を低減できます。 ISE には、次の利点があります。

* 自分専用の静的 IP アドレス。これらの IP アドレスは、マルチテナント サービスのロジック アプリによって共有される静的 IP アドレスとは区別されています。 また、送信先システムとの通信のために、単一の予測可能な静的パブリック アウトバウンド IP アドレスを自分で設定することもできます。 このようにすると、それらの送信先システムで ISE ごとに追加のファイアウォールを設定する必要はありません。

* 実行継続時間、ストレージのリテンション期間、スループット、HTTP の要求と応答のタイムアウト、メッセージのサイズ、カスタム コネクタの要求の上限が引き上げられました。 詳細については、[Azure Logic Apps の制限と構成](../logic-apps/logic-apps-limits-and-config.md)に関する記事をご覧ください。

ISE を作成すると、Azure によってその ISE が Azure 仮想ネットワークに "*挿入*"(デプロイ) されます。 アクセスを必要とする統合アカウントやロジック アプリの場所としてその ISE を使用することができます。 ISE の作成方法の詳細については、[Azure Logic Apps から Azure 仮想ネットワークへの接続](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)に関する記事をご覧ください。

#### <a name="pricing-options"></a>価格オプション

ロジック アプリの種類は、機能と実行される場所 (マルチテナント、シングルテナント、統合サービス環境) によって異なり、それぞれ[価格モデル](../logic-apps/logic-apps-pricing.md)が異なります。 たとえば、マルチテナント ロジック アプリでは従量課金ベースの価格が使用され、統合サービス環境のロジック アプリでは固定価格が使用されます。 Logic Apps の[価格と使用状況測定](../logic-apps/logic-apps-pricing.md)の詳細をご覧ください。

## <a name="how-does-logic-apps-differ-from-functions-webjobs-and-power-automate"></a>Logic Apps と Functions、WebJobs、Power Automate の違い

これらのサービスはいずれも、異種システムを接続し、統合するのに役立ちます。 どのサービスにもそれぞれ長所とメリットが存在するため、十分な機能を備えたスケーラブルな統合システムを短期間で構築するためには、各サービスの機能を組み合わせるのが一番です。 詳細については、[Logic Apps、Functions、WebJobs、Power Automate の比較](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)に関する記事をご覧ください。

## <a name="get-started"></a>はじめに

Azure Logic Apps の利用を開始するには、Azure サブスクリプションが必要です。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 それ以外の場合は、[初めてのロジック アプリの作成に関するクイックスタート](../logic-apps/quickstart-create-first-logic-app-workflow.md)をお試しください。そこでは、RSS フィードを使って Web サイトの新しいコンテンツを監視し、新しいコンテンツが公開された時点でメールを送信します。

## <a name="other-resources"></a>その他のリソース

Logic Apps プラットフォームの詳細については、これらの紹介ビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Go-serverless-Enterprise-integration-with-Azure-Logic-Apps/player]
>
> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Connect-and-extend-your-mainframe-to-the-cloud-with-Logic-Apps/player]

## <a name="next-steps"></a>次のステップ

* [クイックスタート: 初めてのロジック アプリ ワークフローを作成する](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Azure を使用したサーバーレス ソリューション](../logic-apps/logic-apps-serverless-overview.md)について確認する
* [Enterprise Integration Pack による B2B 統合](../logic-apps/logic-apps-enterprise-integration-overview.md)について確認する
