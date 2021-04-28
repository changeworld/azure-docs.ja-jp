---
title: Azure Logic Apps のコネクタ
description: Azure Logic Apps を使用して自動ワークフローを構築するためのコネクタの概要。 さまざまな種類のコネクタ、トリガー、およびアクションのしくみについて説明します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 9e138cb7fb591728e7bc6a02ff61b3167d13da30
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771355"
---
# <a name="connectors-for-azure-logic-apps"></a>Azure Logic Apps のコネクタ

Azure Logic Apps では、"*コネクタ*" を使用して、他のアプリ、サービス、システム、プロトコル、およびプラットフォームからイベント、データ、およびアクションにすばやくアクセスできます。 多くの場合、追加のコードなしでこれらのタスクを実行できます。 コネクタを使用して、クラウドベース、オンプレミス、またはハイブリッド環境でこの情報を使用する Logic Apps ワークフローを構築することもできます。

Logic Apps に使用できるコネクタは多数あります。 そのため、このドキュメントでは、Logic Apps によく使用される一般的なコネクタをいくつか取り上げます。 Logic Apps、Microsoft Power Automate、Microsoft Power Apps との間のコネクタの詳細については、[コネクタのドキュメント](/connectors)を参照してください。 

価格の情報については、[Logic Apps の価格モデル](../logic-apps/logic-apps-pricing.md)と [Logic Apps の価格の詳細](https://azure.microsoft.com/pricing/details/logic-apps/)に関するページをご覧ください。

ご自分のロジック アプリをコネクタがないサービスまたは API と統合するには、HTTP などのプロトコル経由でサービスを呼び出すか、[カスタム コネクタを作成](#custom-apis-and-connectors)します。

## <a name="what-are-connectors"></a>コネクタとは

Azure Logic Apps のコネクタには、ロジック アプリのワークフローでタスクを実行するために使用する "*トリガー*" と "*アクション*" が用意されています。 各トリガーとアクションのプロパティを構成できます。 一部のトリガーとアクションでは、ご自分のワークフローで特定のサービスまたはシステムにアクセスできるように、[接続を作成して構成する](#connection-configuration)必要があります。

### <a name="triggers"></a>トリガー

"*トリガー*" はどのワークフローでも常に最初のステップであり、ワークフローを開始するイベントを指定します。 トリガーには、複数の種類があります。

- "*ポーリング トリガー*" を使って、指定されたスケジュールで特定のサービスまたはシステムを定期的にチェックして、新しいデータまたは特定のイベントをチェックします。 新しいデータが利用可能な場合、または特定のイベントが発生した場合、ワークフローの新しいインスタンスが、このトリガーによって作成および実行されます。 この新しいインスタンスから、入力として渡されたデータを使用できます。
- "*プッシュ トリガー*" を使って、新しいデータまたはイベントの発生をリッスンし、ポーリングは行いません。 新しいデータが利用可能な場合、またはイベントが発生した場合、ロジック アプリの新しいインスタンスが、このトリガーによって作成および実行されます。 この新しいインスタンスから、入力として渡されたデータを使用できます。

たとえば、ファイルが FTP サーバーにアップロードされたときに何か処理を行うワークフローを構築したいとします。 ワークフローの最初の手順として、"**ファイルが追加または変更されたとき**" という名前の FTP コネクタ トリガーを追加できます。 その後、アップロード イベントを定期的にチェックするようにスケジュールを指定できます。

トリガーを使って、入力やその他の必要なデータをワークフローに渡し、後のアクションでそのデータを参照し、ワークフロー全体で使用することもできます。 たとえば、"**新しい電子メールが届いたとき**" という名前の Office 365 Outlook トリガーを使用したいとします。 このトリガーは、新しい電子メールの内容に沿って、送信者、件名、本文、添付ファイルなどを渡すことができるように構成できます。 その後、アクションを使用して、ロジック アプリでその情報を処理できます。

### <a name="actions"></a>Actions

"*アクション*" は、トリガーに従い、ワークフローで何らかのタスクを実行する操作です。 ロジック アプリで複数のアクションを使用できます。 たとえば、SQL データベースの新しい顧客データを検出する SQL トリガーがあるとします。 ワークフローには、顧客データを取得する最初の SQL アクションに続けて、データを処理する別のアクションを含めることができますが、これは、必ずしも SQL アクションとは限りません。

## <a name="connector-categories"></a>コネクタのカテゴリ

Logic Apps には、通常、トリガーとアクションの組み込みまたはマネージド コネクタのバージョンがあります。 少数のトリガーとアクションが、両方のバージョンで利用できます。 特定のバージョンは、マルチテナント ロジック アプリを作成するか、シングルテナント ロジック アプリを作成するかによって異なります。これは現在 [Logic Apps プレビュー](../logic-apps/logic-apps-overview-preview.md)でのみ使用できます。

[組み込みのトリガーとアクション](built-in.md)は Logic Apps ランタイムでネイティブに実行され、接続を作成する必要はなく、次の種類のタスクを実行します。

- [ワークフローでコードを実行](built-in.md#run-code-from-workflows)します。
- [データを整理および制御](built-in.md#control-workflow)します。
- [データを管理または操作](built-in.md#manage-or-manipulate-data)します。

[マネージド コネクタ](managed.md)は、Microsoft によってデプロイ、ホスト、および管理されます。 これらのコネクタにより、クラウドサービス、オンプレミス システム、またはその両方に対してトリガーとアクションが提供されます。 次の設定があります。

- [オンプレミス コネクタ](managed.md#on-premises-connectors)。オンプレミス システムのデータとリソースにアクセスするのに役立ちます。
- [エンタープライズ コネクタ](managed.md#enterprise-connectors)。エンタープライズ システムへのアクセスを提供するいくつかの Logic Apps コネクタのバージョンです。
- [統合アカウント コネクタ](managed.md#integration-account-connectors)。企業間 (B2B) 通信シナリオをサポートします。
- [統合サービス環境 (ISE) コネクタ](managed.md#ise-connectors)。[ISE にのみ利用可能なマネージド コネクタ](#ise-and-connectors)の小さなグループです。

## <a name="connection-configuration"></a>接続の構成

ほとんどのコネクタでは、そのトリガーやアクションをロジック アプリで使用する前に、まず対象のサービスまたはシステムへの "*接続*" を作成する必要があります。 接続を作成するには、アカウントの資格情報と、場合によっては他の接続情報を使用してご自分の ID を認証する必要があります。 たとえば、ワークフローから、ロジック アプリで Office 365 Outlook 電子メール アカウントにアクセスして操作するには、そのアカウントへの接続を承認する必要があります。

Office 365、Salesforce、GitHub など、Azure Active Directory (Azure AD) OAuth を使用するコネクタの場合、アクセス トークンが[暗号化](../security/fundamentals/encryption-overview.md)され、Azure シークレットに安全に保存されるサービスにサインインする必要があります。 FTP や SQL などのその他のコネクタでは、サーバーのアドレス、ユーザー名、およびパスワードなどの構成の詳細を含む接続が必要です。 これらの接続構成の詳細も[暗号化され、Azure に安全に保存されます](../security/fundamentals/encryption-overview.md)。

サービスまたはシステムが許可する限り、確立された接続から、対象のサービスまたはシステムにアクセスできます。 Office 365 や Dynamics など、Azure AD OAuth 接続を使用するサービスについては、アクセス トークンは Logic Apps によって無制限に更新されます。 他のサービスでは、Logic Apps サービスから、トークンを更新せずに使用できる期間に制限が設けられている場合があります。 一般に、パスワードの変更など、一部のアクションで、すべてのアクセス トークンが無効になります。

ワークフロー内から接続を作成しますが、接続は独自のリソース定義を持つ個別の Azure リソースです。 これらの接続リソース定義を確認するには、[Azure から Visual Studio にロジック アプリをダウンロード](../logic-apps/manage-logic-apps-with-visual-studio.md)します。 これは、デプロイの準備がほとんど整っている、パラメーター化された有効なロジック アプリ テンプレートを作成するための最も簡単な方法です。

> [!TIP]
> 組織が Logic Apps コネクタを介して特定のリソースにアクセスすることを許可していない場合は、[Azure Policy](../governance/policy/overview.md) を使用して、[そのような接続を作成する機能をブロックする](../logic-apps/block-connections-connectors.md)ことができます。

## <a name="recurrence-behavior"></a>繰り返しの動作

[繰り返しトリガー](../connectors/connectors-native-recurrence.md)など、繰り返しの組み込みトリガーは、Azure Logic Apps でネイティブに実行され、Office 365 Outlook コネクタ トリガーなど、繰り返しの接続ベースのトリガーとは異なり、最初に接続を作成する必要があります。

どちらの種類のトリガーでも、特定の開始日時が繰り返しに指定されていない場合は、トリガーの繰り返し設定にかかわらず、ロジック アプリを保存またはデプロイすると、直ちに最初の繰り返しが実行されます。 この動作を回避するには、最初の繰り返しを実行する開始日時を指定してください。

### <a name="recurrence-for-built-in-triggers"></a>組み込みトリガーの繰り返し

繰り返しの組み込みトリガーでは、指定されたタイム ゾーンを含め、設定したスケジュールに従います。 ただし、将来の繰り返しを実行する特定時刻など、その他の詳細なスケジュール オプションが繰り返しに指定されていない場合は、それらの繰り返しは最後のトリガー実行に基づいて行われます。 その結果、ストレージ呼び出し中の待機時間などの要因によって、それらの繰り返しの開始時刻がずれる可能性があります。 トラブルシューティングのヘルプについては、「[繰り返しの問題](#recurrence-issues)」セクションを参照してください。

### <a name="recurrence-for-connection-based-triggers"></a>接続ベース トリガーの繰り返し

Office 365 Outlook など、繰り返しの接続ベースのトリガーでは、実行を制御するドライバーはスケジュールだけではありません。 タイム ゾーンにより、最初の開始時刻のみが決定されます。 後続の実行は、繰り返しのスケジュール、最後のトリガーの実行、"および" 実行時間の移動や予期しない動作を引き起こす可能性があるその他の要因によって決まります。 次の設定があります。

- より多くのデータがあるサーバーにトリガーがアクセスするかどうか。これは、トリガーによってすぐに取り込みが試行されます。
- トリガーによって発生するエラーまたは再試行。
- ストレージ呼び出し中の待機時間。
- 夏時間 (DST) の開始および終了時に、指定されたスケジュールが維持されない。
- 次回の実行時刻の発生に影響を与える可能性があるその他の要因。

トラブルシューティングのヘルプについては、「[繰り返しの問題](#recurrence-issues)」セクションを参照してください。 

### <a name="recurrence-issues"></a>繰り返しの問題

指定した開始時刻にワークフローが実行され、(特に頻度が日単位以上の場合に) 繰り返しが必ず行われるようにするには、次のソリューションを試してください。

DST が有効になったときに、ワークフローが引き続き予定時刻に実行されるように、繰り返しを手動で調整します。 そうしないと、開始時刻が DST の開始時には 1 時間先に、DST の終了時には 1 時間前にシフトされます。 詳細と例については、[夏時間と標準時間での繰り返し](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time)に関する記事を参照してください。

**繰り返し** トリガーを使用している場合は、タイム ゾーン、開始日時を指定します。 また、 **[設定時刻 (時間)]** および **[設定時刻 (分)]** プロパティ ( **[日]** と **[週]** の頻度でのみ使用可能) で、後続の繰り返しを実行する特定の時刻を構成します。 ただし、一部の時間枠では、時間がシフトするときに引き続き問題が発生することがあります。 

繰り返しが必ず行われるように、**繰り返し** トリガーの代わりに [**スライディング ウィンドウ** トリガー](../connectors/connectors-native-sliding-window.md)を使用することを検討してください。

## <a name="custom-apis-and-connectors"></a>カスタム API とコネクタ

カスタム コードを実行する API や、コネクタとして使用できない API を呼び出すには、[カスタム API Apps を作成](../logic-apps/logic-apps-create-api-app.md)して、Logic Apps プラットフォームを拡張します。 

"任意の" REST または SOAP ベース API 用の[カスタム コネクタを作成](../logic-apps/custom-connector-overview.md)することもできます。その場合、これらの API は、Azure サブスクリプションの任意のロジック アプリで使用できるようになります。 

Azure 内でカスタム API Apps またはコネクタを公開し、誰でも使用できるようにするには、[Microsoft の認定を受けるためにコネクタを提出](/connectors/custom-connectors/submit-certification)してください。

## <a name="ise-and-connectors"></a>ISE とコネクタ

Azure 仮想ネットワーク内のリソースに直接アクセスする必要があるワークフローの場合は、専用のリソースでワークフローをビルド、デプロイ、および実行できる、専用の[統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) を作成できます。 ISE の作成について詳しくは、[Azure Logic Apps から Azure Virtual Network への接続](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)に関するページをご覧ください。

ISE 内で作成されたカスタム コネクタは、オンプレミス データ ゲートウェイでは動作しません。 ただし、これらのコネクタでは、ISE がホストされている Azure 仮想ネットワークに接続されているオンプレミス データ ソースに直接アクセスできます。 そのため、ISE 内のロジック アプリでは、ほとんどの場合、それらのリソースと通信するときにデータ ゲートウェイは不要です。 ISE の外部で作成したカスタム コネクタがあり、オンプレミス データ ゲートウェイを必要とする場合、ISE 内のロジック アプリから、それらのコネクタを使用できます。

Logic Apps デザイナーで、ISE 内でロジック アプリに使用するコネクタを参照する場合、組み込みトリガーおよびアクションに **CORE** ラベルが表示されますが、一部のコネクタには **ISE** ラベルが表示されます。

:::row:::
    :::column:::
        ![CORE コネクタの例](./media/apis-list/example-core-connector.png)
        \
        \
        **CORE**
        \
        \
        このラベルが付いた組み込みトリガーおよびアクションは、ロジック アプリと同じ ISE 内で動作します。
    :::column-end:::
    :::column:::
        ![ISE コネクタの例](./media/apis-list/example-ise-connector.png)
        \
        \
        **ISE**
        \
        \
        このラベルが付いたマネージド コネクタは、ロジック アプリと同じ ISE 内で動作します。 Azure 仮想ネットワークに接続されているオンプレミス システムがある場合、ISE を使用すると、ロジック アプリで[オンプレミス データ ゲートウェイ](../logic-apps/logic-apps-gateway-connection.md)を使用せずに、そのシステムに直接アクセスすることができます。 代わりに、そのシステムの **ISE** コネクタ (使用可能な場合)、HTTP アクション、または [カスタム コネクタ](connectors-overview.md#custom-apis-and-connectors)を使用できます。 **ISE** コネクタがないオンプレミス システムの場合は、オンプレミスのデータ ゲートウェイを使用します。 使用可能な ISE コネクタを確認するには、「[ISE コネクタ](#ise-and-connectors)」を参照してください。
    :::column-end:::
    :::column:::
        ![マルチテナント コネクタの例](./media/apis-list/example-multi-tenant-connector.png)
        \
        \
        ラベルなし    \
        \
        **CORE** または **ISE** ラベルのない他のすべてのコネクタは、引き続き使用でき、グローバルなマルチテナント Logic Apps サービスで実行できます。
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="known-issues"></a>既知の問題

Logic Apps コネクタに関する既知の問題を次に示します。

#### <a name="error-badgateway-client-request-id-guid"></a>エラー:BadGateway。 クライアント要求 ID: '{GUID}'

このエラーは、 1 つ以上の接続で SFTP や SQL などの Azure Active Directory (Azure AD) OAuth 認証がサポートされていないロジック アプリでタグを更新した場合に発生し、それらの接続が切断されます。 この動作を回避するには、それらのタグを更新しないようにします。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Logic Apps から呼び出しできるカスタム API の作成](/logic-apps/logic-apps-create-api-app)
