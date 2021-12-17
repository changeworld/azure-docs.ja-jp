---
title: Azure Logic Apps の概要
description: Azure Logic Apps は、アプリ、データ、サービス、システムを最小限のコーディングまたはノーコードで統合するワークフローを自動化するためのクラウド プラットフォームです。 ワークフローは、マルチテナント、シングルテナント、または専用環境で実行できます。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: overview
ms.custom: mvc, contperf-fy21q4
ms.date: 08/18/2021
ms.openlocfilehash: a5b1b0f6d4d51874dd330c2336e5f342aaedeec9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085869"
---
# <a name="what-is-azure-logic-apps"></a>Azure Logic Apps とは

[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps) は、アプリ、データ、サービス、およびシステムを統合する自動化された "[*ワークフロー*](#workflow)" を作成および実行するためのクラウドベースのプラットフォームです。 このプラットフォームを使用すると、エンタープライズおよび企業間 (B2B) シナリオ向けのスケーラビリティの高い統合ソリューションを迅速に開発できます。 [Azure Integration Services](https://azure.microsoft.com/product-categories/integration/) のメンバーである Azure Logic Apps を使用すると、クラウド、オンプレミス、ハイブリッド環境で、レガシ、最新、および最先端のシステムをより簡単に接続できます。

以下の一覧に挙げた例は、Azure Logic Apps サービスを使用して自動化できるタスク、ビジネス プロセス、ワークロードの一部にすぎません。

* Office 365 を使用して、特定のイベント (新しいファイルのアップロードなど) が発生したときの電子メール通知をスケジュールして送信する。

* オンプレミス システムとクラウド サービスの垣根を越えて顧客注文をルーティングし、処理する。

* アップロードされたファイルを SFTP サーバーまたは FTP サーバーから Azure Storage に移動する。

* ツイートを監視したり、そのセンチメントを分析したり、確認が必要な項目についてアラートやタスクを作成したりする。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Go-serverless-Enterprise-integration-with-Azure-Logic-Apps/player]

選択および作成するロジック アプリのリソースの種類に応じて、Azure の仮想ネットワークにアクセスするときに、ロジック アプリは、マルチテナント型の Azure Logic Apps、[シングルテナント型の Azure Logic Apps](single-tenant-overview-compare.md)、または専用の[統合サービス環境](connect-virtual-network-vnet-isolated-environment-overview.md)のいずれかで実行されます。 ロジック アプリをコンテナー内で実行する場合は、[Azure Arc 対応 Logic Apps を使用してシングルテナント ベースのロジック アプリを作成します](azure-arc-enabled-logic-apps-create-deploy-workflows.md)。 詳細については、「[Azure Arc 対応 Logic Apps とは](azure-arc-enabled-logic-apps-overview.md)」および[ロジック アプリのリソースの種類とホスト環境の違い](#resource-environment-differences)に関するセクションを参照してください。

さまざまなデータ ソースにリアルタイムかつ安全にアクセスして操作を実行するには、[今も増え続けている 400 以上の Azure コネクタ エコシステム](/connectors/connector-reference/connector-reference-logicapps-connectors)から "[*マネージド コネクタ*](#managed-connector)" を選択し、ワークフローで使用することができます。以下に例を示します。

* Azure サービス (Blob Storage、Service Bus など)

* Office 365 サービス (Outlook、Excel、SharePoint など)

* データベース サーバー (SQL、Oracle など)

* エンタープライズ システム (SAP、IBM MQ など)

* ファイル共有 (FTP、SFTP など)

サービス エンドポイントとの通信、独自コードの実行、ワークフローの編成、データの操作のために、Azure Logic Apps サービス内でネイティブに実行される "[*組み込みの*](#built-in-operations)" トリガーとアクションを使用できます。 たとえば、組み込みトリガーとしては、要求トリガー、HTTP トリガー、繰り返しトリガーなどがあります。 組み込みアクションには、条件、For each、JavaScript コードの実行に加えて、Azure でホストされる Azure Functions、Web アプリ、API アプリのほか、他の Azure Logic Apps ワークフローを呼び出す操作などがあります。

Azure Logic Apps には、B2B 統合シナリオ向けに [BizTalk Server](/biztalk/core/introducing-biztalk-server) の機能が含まれています。 企業間 (B2B) の成果物を定義するには、これらのアーティファクトを格納する "[*統合アカウント*](#integration-account)" を作成します。 このアカウントをロジック アプリにリンクすると、ワークフローでこれらの B2B の成果物を使用し、電子データ交換 (EDI) および Enterprise Application Integration (EAI) 標準に準拠するメッセージを交換できます。

ワークフローからアプリやデータ、サービス、システムにアクセスしてそれらと連携する方法について詳しくは、次のドキュメントを参照してください。

* [Azure Logic Apps のコネクタ](../connectors/apis-list.md)

* [Azure Logic Apps のマネージド コネクタ](../connectors/built-in.md)

* [Azure Logic Apps の組み込みのトリガーとアクション](../connectors/managed.md)

* [Azure Logic Apps を使用した B2B エンタープライズ統合ソリューション](logic-apps-enterprise-integration-overview.md)

<a name="logic-app-concepts"></a>

## <a name="key-terms"></a>キーワード

次の用語は、Azure Logic Apps サービスの重要な概念です。

### <a name="logic-app"></a>ロジック アプリ

"*ロジック アプリ*" は、ワークフローを開発するときに作成する Azure リソースです。 [さまざまな環境で動作する複数のロジック アプリ リソースの種類](#resource-environment-differences)があります。

### <a name="workflow"></a>ワークフロー

"*ワークフロー*" は、タスクまたはプロセスを定義する一連のステップです。 各ワークフローは 1 つのトリガーで始まり、その後に 1 つ以上のアクションを追加する必要があります。

### <a name="trigger"></a>トリガー

"*トリガー*" は、常にワークフローの最初のステップであり、そのワークフローでそれ以降のステップを実行するための条件が指定されます。 たとえば、受信トレイにメールが届いた、ストレージ アカウントで新しいファイルを検出した、といったトリガー イベントが考えられます。

### <a name="action"></a>操作

"*アクション*" は、トリガーの後のワークフローの各ステップです。 どのアクションでも、ワークフロー内で何らかの操作が実行されます。

### <a name="built-in-operations"></a>組み込み操作

"*組み込み*" のトリガーまたはアクションは、Azure Logic Apps でネイティブに実行される操作です。 たとえば、組み込みの操作を使用すると、ワークフローのスケジュールや構造の制御、独自のコードの実行、データの管理と操作、エンドポイントへの要求の送信または受信、ワークフロー内のその他のタスクを実行することができます。

ほとんどの組み込み操作は、サービスやシステムに関連付けられていませんが、一部の組み込み操作は、Azure Functions や Azure App Service などの特定のサービスで使用できます。 また、その多くは、ワークフローから接続を作成したり、ID の認証を行ったりする必要がありません。 詳細と例については、[Azure Logic Apps の組み込み操作](../connectors/built-in.md)に関するページを参照してください。

たとえば、繰り返しトリガーを使用すると、ほぼどのようなワークフローでもスケジュールに従って開始できます。 また、要求トリガーを使用すると、呼び出すまでワークフローを待機させることもできます。

### <a name="managed-connector"></a>マネージド コネクタ

"*マネージド コネクタ*" は、特定のアプリ、データ、サービス、またはシステムにアクセスするために使用できる REST API の事前構築済みプロキシまたはラッパーです。 ほとんどのマネージド コネクタは、その使用前に、ワークフローから接続を作成し、ID の認証を行う必要があります。 マネージド コネクタは、Microsoft によって公開、ホスト、および保守されています。 詳細については、[Azure Logic Apps のマネージド コネクタ](../connectors/managed.md)に関するページを参照してください。

たとえば、トリガーを使用してワークフローを開始することや、Office 365、Salesforce、ファイル サーバーなどのサービスと連携するアクションを実行することができます。

### <a name="integration-account"></a>統合アカウント

"*統合アカウント*" は、ワークフローで使用する B2B 成果物を定義して格納する場合に作成する Azure リソースです。 [統合アカウントを作成してロジック アプリにリンクする](logic-apps-enterprise-integration-create-integration-account.md)と、ワークフローにこれらの B2B の成果物を使用できるようになります。 また、ワークフローから Electronic Data Interchange (EDI) と Enterprise Application Integration (EAI) の標準に準拠したメッセージを交換することもできます。

たとえば、取引先、契約、スキーマ、マップ、その他の B2B 成果物を定義できます。 これらの成果物を使用し、AS2、EDIFACT、X12、RosettaNet などのプロトコルを使用してメッセージを交換するワークフローを作成できます。

<a name="how-do-logic-apps-work"></a>

## <a name="how-logic-apps-work"></a>ロジック アプリが機能するしくみ

ロジック アプリでは、各ワークフローが必ず単一の[トリガー](#trigger)で始まります。 トリガーは、条件が満たされたとき、たとえば特定のイベントが発生したときや特定の条件をデータが満たしたときに起動します。 多くのトリガーには、ワークフローの実行頻度を制御する[スケジューリング機能](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)が含まれています。 トリガーに続けて、1 つまたは複数の[アクション](#action)によって各種操作 (ワークフロー内を通過するデータを加工、処理、変換する操作や、ワークフローを次のステップに進める操作など) が実行されます。

次のスクリーンショットは、エンタープライズ ワークフローの例の一部を示しています。 このワークフローでは、条件とスイッチを使用して次のアクションを決定します。 たとえば、注文システムがあり、受信した注文をワークフローで処理しているとします。 あなたは、一定のコストを超える注文を手動で確認したいと考えています。 このワークフローの前には、受注のコストを決定するステップが既にあります。 そこで、そのコスト値に基づいて初期条件を作成します。 次に例を示します。

* 注文が一定額を下回る場合、条件は false です。 そこで、ワークフローによって注文が処理されます。

* 条件が true であれば、手動レビューのためにワークフローからメールが送信されます。 スイッチで次の手順が決定されます。

  * レビュー担当者が承認した場合、ワークフローによる注文の処理は続行されます。

  * レビュー担当者がエスカレーションを行った場合、注文に関する詳細情報を取得するために、ワークフローからエスカレーションのメールが送信されます。

    * エスカレーション要件が満たされている場合、応答条件は true です。 そのため、注文は処理されます。

    * 応答条件が false の場合、問題に関するメールが送信されます。

:::image type="content" source="./media/logic-apps-overview/example-enterprise-workflow.png" alt-text="ワークフロー デザイナーと、スイッチと条件を使用するエンタープライズ ワークフローのサンプルを示すスクリーンショット。" lightbox="./media/logic-apps-overview/example-enterprise-workflow.png":::

Azure portal、Visual Studio Code、または Visual Studio で Azure Logic Apps ワークフロー デザイナーを使用して、ワークフローを視覚的に作成できます。 各ワークフローには、JavaScript Object Notation (JSON) を使用して記述された、基になる定義も含まれています。 必要に応じて、この JSON 定義を変更してワークフローを編集できます。 Azure Logic Apps では、一部の作成タスクと管理タスクについて、Azure PowerShell と Azure CLI コマンドがサポートされます。 Azure Logic Apps では、デプロイの自動化を目的として、Azure Resource Manager テンプレートがサポートされます。

<a name="resource-environment-differences"></a>

## <a name="resource-type-and-host-environment-differences"></a>リソースの種類とホスト環境の違い

ロジック アプリ ワークフローを作成するには、シナリオ、ソリューションの要件、必要な機能、およびワークフローを実行する環境を踏まえて、**ロジック アプリ** のリソースの種類を選択します。

次の表は、元の **ロジック アプリ (従量課金)** と **ロジック アプリ (Standard)** のリソースの種類の違いを簡単にまとめたものです。 ロジック アプリ ワークフローを展開、ホスト、実行する際の、"*シングルテナント環境*"、"*マルチテナント環境*"、"*統合サービス環境 (ISE)* "、*App Service Environment v3 (ASEv3)* の違いも分かります。

[!INCLUDE [Logic app resource type and environment differences](../../includes/logic-apps-resource-environment-differences-table.md)]

## <a name="why-use-azure-logic-apps"></a>Azure Logic Apps を使用する理由

Azure Logic Apps 統合プラットフォームでは、Microsoft が管理するあらかじめ構築された API コネクタと組み込み操作が提供されるので、アプリ、データ、サービス、システムをより簡単かつ迅速に接続し、統合できます。 リソースへのアクセス方法の理解ではなく、ソリューションのビジネス ロジックや機能の設計と実装に集中できます。

通常、コードを記述する必要はありません。 ただし、コードを記述する必要がある場合は、[Azure Functions](../azure-functions/functions-overview.md) を使用してコード スニペットを作成し、ワークフローからそのコードを実行できます。 また、[**インライン コード** アクション](logic-apps-add-run-inline-code.md)を使用して、ワークフローで実行するコード スニペットを作成することもできます。 ワークフローで、Azure サービス、カスタム アプリ、または他のソリューションのイベントを操作する必要がある場合は、[Azure Event Grid](../event-grid/overview.md) を使用してイベントを監視、ルーティング、発行できます。

Azure Logic Apps は Microsoft Azure によって完全に管理されているので、これらのサービスを使用して構築されたソリューションのホスティング、スケーリング、管理、監視、メンテナンスについて心配する必要はありません。 これらの機能を使用して、["サーバーレス" のアプリやソリューション](../logic-apps/logic-apps-serverless-overview.md) を作成すると、ビジネス ロジックと機能に集中できます。 これらのサービスは、ニーズに合わせて自動的にスケーリングします。また、統合を迅速化するほか、コードをほとんどまたはまったく使用せずに堅牢なクラウド アプリを構築するのに役立ちます。

Azure Logic Apps を他の Azure サービスや Microsoft 製品と組み合わせることによって、機敏性を高め、中核となるビジネスに集中できるようになった他社の事例については、これらの[お客様事例](https://aka.ms/logic-apps-customer-stories)をご覧ください。

以降のセクションでは、Azure Logic Apps の機能と利点について詳しく説明します。

#### <a name="visually-create-and-edit-workflows-with-easy-to-use-tools"></a>使いやすいツールでワークフローを視覚的に作成、編集

Azure Logic Apps のビジュアル デザイン ツールを使用して、時間を節約し、複雑なプロセスを簡素化します。 ワークフローは、Azure portal、Visual Studio Code、または Visual Studio で Azure Logic Apps ワークフローを使用して最初から最後まで作成できます。 トリガーを使用してワークフローを開始し、[コネクタ ギャラリー](/connectors/connector-reference/connector-reference-logicapps-connectors)から任意の数のアクションを追加するだけです。

マルチテナント ベースのロジック アプリを作成する場合は、[テンプレート ギャラリーからワークフローを作成](../logic-apps/logic-apps-create-logic-apps-from-templates.md)すると、作業を速やかに開始できます。 これらのテンプレートは、サービスとしてのソフトウェア (SaaS) アプリの単純な接続から、高度な B2B ソリューション、"遊び感覚" のテンプレートまで、一般的なワークフロー パターンで利用できます。

#### <a name="connect-different-systems-across-various-environments"></a>さまざまな環境でさまざまなシステムを接続

パターンやプロセスによっては、説明は簡単でもコードで実装するのは困難なこともあります。 Azure Logic Apps プラットフォームは、クラウド、オンプレミス、ハイブリッド環境の異種システムをシームレスに接続するのに役立ちます。 たとえば、クラウド マーケティング ソリューションをオンプレミスの課金システムに接続したり、Azure Service Bus を使用して、複数の API やシステムのメッセージングを一元化したりできます。 Azure Logic Apps は、これらのシナリオに対応する再利用可能かつ再構成可能なソリューションを提供するための、高速で信頼性の高い一貫した方法を提供します。

#### <a name="write-once-reuse-often"></a>1 回の作成で何度も再利用

複数の環境やリージョンにわたって[デプロイを設定して自動化](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)できるように、Azure Resource Manager テンプレートとしてロジック アプリを作成します。

#### <a name="first-class-support-for-enterprise-integration-and-b2b-scenarios"></a>エンタープライズ統合と B2B のシナリオに最上級のサポートを実現

企業や組織は、業界標準であっても異なるメッセージ プロトコルや形式 (EDIFACT、AS2、X12、RosettaNet など) を使用して、電子的に相互に通信しています。 Azure Logic Apps でサポートされている[エンタープライズ統合機能](../logic-apps/logic-apps-enterprise-integration-overview.md)を使用すると、パーティーで使用されるメッセージ形式を、自分の組織のシステムが解釈して処理できる形式に変換するワークフローを作成できます。 Azure Logic Apps はこのようなやり取りを円滑に処理し、暗号化とデジタル署名を使ってその安全を確保します。

現在のシステムとサービスを使用して小規模から始め、自分のペースで段階的に拡大していくことができます。 準備ができたら、Azure Logic Apps プラットフォームによって提供されるこれらの機能を使用して、より成熟した統合シナリオを実装し、スケールアップすることができます。

* [Microsoft BizTalk Server](/biztalk/core/introducing-biztalk-server)、[Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)、[Azure Functions](../azure-functions/functions-overview.md)、[Azure API Management](../api-management/api-management-key-concepts.md) などを統合して構築する。

* [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md)、[AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)、[X12](../logic-apps/logic-apps-enterprise-integration-x12.md)、[RosettaNet](logic-apps-enterprise-integration-rosettanet.md) の各プロトコルを使用してメッセージを交換する。

* [XML メッセージ](../logic-apps/logic-apps-enterprise-integration-xml.md)と[フラット ファイル](../logic-apps/logic-apps-enterprise-integration-flatfile.md)を処理する。

* [取引先](../logic-apps/logic-apps-enterprise-integration-partners.md)、[契約](../logic-apps/logic-apps-enterprise-integration-agreements.md)、[変換マップ](../logic-apps/logic-apps-enterprise-integration-maps.md)、[検証スキーマ](../logic-apps/logic-apps-enterprise-integration-schemas.md)などの B2B アーティファクトを保存および管理するための[統合アカウント](./logic-apps-enterprise-integration-create-integration-account.md)を作成する。

たとえば、Microsoft BizTalk Server を使用している場合は、ワークフローで [BizTalk Server コネクタ](../connectors/managed.md#on-premises-connectors)を使用して、BizTalk Server と通信できます。 その後、[統合アカウント コネクタ](../connectors/managed.md#integration-account-connectors)を使用して、ワークフローで BizTalk のような操作を実行または拡張できます。 逆に、BizTalk Server で [Microsoft BizTalk Server Adapter for Azure Logic Apps](https://www.microsoft.com/download/details.aspx?id=54287) を使用して、ワークフローと通信することもできます。 BizTalk Server で [BizTalk Server Adapter を設定して使用する](/biztalk/core/logic-app-adapter)方法をご覧ください。

#### <a name="built-in-extensibility"></a>組み込みの拡張機能

必要なコードを実行するための適切なコネクタがない場合は、[Azure Functions](../azure-functions/functions-overview.md) を使用して、独自のコード スニペットを作成し、ワークフローから呼び出すことができます。 または、ワークフローから呼び出すことができる独自の [API](../logic-apps/logic-apps-create-api-app.md) や[カスタム コネクタ](../logic-apps/custom-connector-overview.md)を作成します。

#### <a name="access-resources-inside-azure-virtual-networks"></a>Azure 仮想ネットワーク内のリソースにアクセスする

"[*統合サービス環境*" (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) を作成するときに、ロジック アプリ ワークフローでは [Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)内にあるセキュリティで保護されたリソース (仮想マシン (VM) や他のシステムまたはサービスなど) にアクセスできます。 ISE は、専用リソースを使用し、グローバルなマルチテナント Azure Logic Apps サービスとは別に実行される、Azure Logic Apps サービスの専用インスタンスです。

独自の専用インスタンスでロジック アプリを実行することで、他の Azure テナントがアプリのパフォーマンスに与える可能性がある影響 (["うるさい隣人" エフェクト](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)とも呼ばれます) を低減できます。 ISE には、次の利点があります。

* 自分専用の静的 IP アドレス。これらの IP アドレスは、マルチテナント サービスのロジック アプリによって共有される静的 IP アドレスとは区別されています。 また、送信先システムとの通信のために、単一の予測可能な静的パブリック アウトバウンド IP アドレスを自分で設定することもできます。 このようにすると、それらの送信先システムで ISE ごとに追加のファイアウォールを設定する必要はありません。

* 実行継続時間、ストレージのリテンション期間、スループット、HTTP の要求と応答のタイムアウト、メッセージのサイズ、カスタム コネクタの要求の上限が引き上げられました。 詳細については、[Azure Logic Apps の制限と構成](../logic-apps/logic-apps-limits-and-config.md)に関する記事をご覧ください。

ISE を作成すると、Azure によってその ISE が Azure 仮想ネットワークに "*挿入*"(デプロイ) されます。 アクセスを必要とする統合アカウントやロジック アプリの場所としてその ISE を使用することができます。 ISE の作成方法の詳細については、[Azure Logic Apps から Azure 仮想ネットワークへの接続](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)に関する記事をご覧ください。

#### <a name="pricing-options"></a>価格オプション

ロジック アプリの種類は、機能と実行される場所 (マルチテナント、シングルテナント、統合サービス環境) によって異なり、それぞれ[価格モデル](../logic-apps/logic-apps-pricing.md)が異なります。 たとえば、マルチテナント ベースのロジック アプリでは従量課金の価格が使用され、統合サービス環境のロジック アプリでは固定価格が使用されます。 Azure Logic Apps の[価格と使用状況測定](../logic-apps/logic-apps-pricing.md)の詳細をご覧ください。

## <a name="how-does-azure-logic-apps-differ-from-functions-webjobs-and-power-automate"></a>Azure Logic Apps と Functions、WebJobs、Power Automate の違い

これらのサービスはいずれも、異種システムを接続し、統合するのに役立ちます。 どのサービスにもそれぞれ長所とメリットが存在するため、十分な機能を備えたスケーラブルな統合システムを短期間で構築するためには、各サービスの機能を組み合わせるのが一番です。 詳細については、[Logic Apps、Functions、WebJobs、Power Automate の比較](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)に関する記事をご覧ください。

## <a name="get-started"></a>はじめに

Azure Logic Apps の利用を開始するには、Azure サブスクリプションが必要です。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

準備ができたら、次の Azure Logic Apps のクイックスタート ガイドの 1 つ以上を試してみてください。 新しいコンテンツについて RSS フィードを監視し、メールを送信する基本的なワークフローを作成する方法について説明します。

* [Azure portal でマルチテナント ベースのロジック アプリを作成する](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* [Visual Studio でマルチテナント ベースのロジック アプリを作成する](quickstart-create-logic-apps-with-visual-studio.md)

* [Visual Studio Code でマルチテナント ベースのロジック アプリを作成する](quickstart-create-logic-apps-visual-studio-code.md)

Azure Logic Apps の他のクイックスタート ガイドもお勧めです。

* [ARM テンプレートを使用してマルチテナント ベースのロジック アプリを作成する](quickstart-create-deploy-azure-resource-manager-template.md)

* [Azure CLI を使用してマルチテナント ベースのロジック アプリを作成する](quickstart-create-deploy-azure-resource-manager-template.md)

## <a name="other-resources"></a>その他のリソース

Azure Logic Apps プラットフォームの詳細については、これらの紹介ビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Connect-and-extend-your-mainframe-to-the-cloud-with-Logic-Apps/player]

## <a name="next-steps"></a>次のステップ

* [クイックスタート: 初めてのロジック アプリ ワークフローを作成する](../logic-apps/quickstart-create-first-logic-app-workflow.md)
