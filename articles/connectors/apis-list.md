---
title: Azure Logic Apps のコネクタ
description: Azure Logic Apps で、コネクタを使用して自動化されたワークフローを構築する方法の概要。 さまざまなトリガー、アクション、コネクタの動作について説明します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: d6a2b6c30d4ff6212a905e454ff56e844e423d22
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108124637"
---
# <a name="connectors-for-azure-logic-apps"></a>Azure Logic Apps のコネクタ

Azure Logic Apps では、"*コネクタ*" を使用して、他のアプリ、サービス、システム、プロトコル、プラットフォームからデータやイベントなどのリソースにすばやくアクセスできます。 コネクタを使用すると、クラウド ベース、オンプレミス、ハイブリッド環境にまたがる情報を使用、処理、統合するロジック アプリ ワークフローを、多くの場合はコードをまったく記述することなく構築できます。

実際のワークフローで使用する数百のコネクタから選択できます。 そのため、このドキュメントでは、Logic Apps 向けのよく使用されている一般的ないくつかのコネクタに焦点を絞ります。 Logic Apps、Microsoft Power Automate、Microsoft Power Apps 全体のコネクタに関する完全な情報については、[コネクタのドキュメント](/connectors)を確認してください。 価格については、[Logic Apps の価格モデル](../logic-apps/logic-apps-pricing.md)および [Logic Apps の価格の詳細](https://azure.microsoft.com/pricing/details/logic-apps/)に関するページを確認してください。

> [!NOTE]
> ワークフローをコネクタのないサービスまたは API と統合するには、HTTP などのプロトコル経由でそのサービスを呼び出すか、または[カスタム コネクタを作成する](#custom-apis-and-connectors)ことができます。

## <a name="what-are-connectors"></a>コネクタとは

コネクタは、ロジック アプリのワークフローでタスクを実行するために使用する "*トリガー*" と "*アクション*" を提供します。 各トリガーとアクションのプロパティを構成できます。 一部のトリガーとアクションでは、ご自分のワークフローで特定のサービスまたはシステムにアクセスできるように、[接続を作成して構成する](#connection-configuration)必要があります。

### <a name="triggers"></a>トリガー

"*トリガー*" は、ワークフローを開始するイベントを指定するものであり、どのワークフローでも常に最初のステップになります。 各トリガーはまた、トリガーがイベントを監視したり、それに応答したりする方法を制御する特定の起動パターンに従います。 通常、トリガーは "*ポーリング*" パターンまたは "*プッシュ*" パターンに従いますが、トリガーを両方のバージョンで使用できる場合もあります。

- "*ポーリング トリガー*" を使って、指定されたスケジュールで特定のサービスまたはシステムを定期的にチェックして、新しいデータまたは特定のイベントをチェックします。 新しいデータが利用可能な場合、または特定のイベントが発生した場合、ワークフローの新しいインスタンスが、このトリガーによって作成および実行されます。 この新しいインスタンスから、入力として渡されたデータを使用できます。
- "*プッシュ トリガー*" を使って、新しいデータまたはイベントの発生をリッスンし、ポーリングは行いません。 新しいデータが使用可能になった場合、またはイベントが発生した場合、これらのトリガーではワークフローの新しいインスタンスを作成して実行します。 この新しいインスタンスから、入力として渡されたデータを使用できます。

たとえば、ファイルが FTP サーバーにアップロードされたときに何か処理を行うワークフローを構築したいとします。 ワークフローの最初のステップとして、ポーリング パターンに従う **[ファイルの追加または変更時]** という名前の FTP トリガーを使用できます。 その後、アップロード イベントを定期的にチェックするようにスケジュールを指定できます。

トリガーを使って、入力やその他の必要なデータをワークフローに渡し、後のアクションでそのデータを参照し、ワークフロー全体で使用することもできます。 たとえば、新しい電子メールを受け取ったときにワークフローを開始するために、 **[新しい電子メールが届いたとき]** という名前の Office 365 Outlook トリガーを使用するとします。 このトリガーは、新しい電子メールの内容に沿って、送信者、件名、本文、添付ファイルなどを渡すことができるように構成できます。 ワークフローでは、その後、他のアクションを使用してそれらの情報を処理できます。

### <a name="actions"></a>アクション

"*アクション*" は、トリガーに従い、ワークフローで何らかのタスクを実行する操作です。 ワークフローでは、複数のアクションを使用できます。 たとえば、SQL データベース内の新しい顧客データを検出する SQL トリガーを使用してワークフローを開始するとします。 このトリガーに従って、ワークフローに、その顧客データを取得する SQL アクションを含めることができます。 この SQL アクションに続けて、ワークフローに、そのデータを処理する (必ずしも SQL ではない) 別のアクションを含めることができます。

## <a name="connector-categories"></a>コネクタのカテゴリ

Logic Apps では、ほとんどのトリガーとアクションが "*組み込み*" バージョンまたは "*マネージド コネクタ*" バージョンのどちらかで使用できます。 少数のトリガーとアクションが、両方のバージョンで利用できます。 使用できるバージョンは、マルチテナント ロジック アプリと (現在 [Logic Apps プレビュー](../logic-apps/logic-apps-overview-preview.md)でのみ使用可能な) シングルテナント ロジック アプリのどちらを作成するかによって異なります。

[組み込みのトリガーとアクション](built-in.md)は Logic Apps ランタイムでネイティブに実行され、接続を作成する必要はなく、次の種類のタスクを実行します。

- [ワークフローでコードを実行](built-in.md#run-code-from-workflows)します。
- [データを整理および制御](built-in.md#control-workflow)します。
- [データを管理または操作](built-in.md#manage-or-manipulate-data)します。

[マネージド コネクタ](managed.md)は、Microsoft によってデプロイ、ホスト、および管理されます。 これらのコネクタにより、クラウドサービス、オンプレミス システム、またはその両方に対してトリガーとアクションが提供されます。 マネージド コネクタは、次のカテゴリで使用できます。

- [オンプレミス コネクタ](managed.md#on-premises-connectors)。オンプレミス システムのデータとリソースにアクセスするのに役立ちます。
- [エンタープライズ コネクタ](managed.md#enterprise-connectors)。エンタープライズ システムへのアクセスを提供します。
- [統合アカウント コネクタ](managed.md#integration-account-connectors)。企業間 (B2B) 通信のシナリオをサポートします。
- [統合サービス環境 (ISE) コネクタ](managed.md#ise-connectors)。[ISE でのみ使用できるマネージド コネクタ](#ise-and-connectors)の小さなグループです。

## <a name="connection-configuration"></a>接続の構成

ほとんどのコネクタでは、ワークフローでそのトリガーまたはアクションを使用するには、まずターゲット サービスまたはシステムへの *接続* を作成しておく必要があります。 接続を作成するには、アカウントの資格情報と、場合によっては他の接続情報を使用してご自分の ID を認証する必要があります。 たとえば、ワークフローで Office 365 Outlook 電子メール アカウントにアクセスしてそれを操作するには、そのアカウントへの接続を認可しておく必要があります。

### <a name="connection-security-and-encryption"></a>接続のセキュリティと暗号化

Office 365、Salesforce、GitHub など、Azure Active Directory (Azure AD) OAuth を使用するコネクタの場合、アクセス トークンが[暗号化](../security/fundamentals/encryption-overview.md)され、Azure シークレットに安全に保存されるサービスにサインインする必要があります。 FTP や SQL などのその他のコネクタでは、サーバーのアドレス、ユーザー名、およびパスワードなどの構成の詳細を含む接続が必要です。 これらの接続構成の詳細も[暗号化され、Azure に安全に保存されます](../security/fundamentals/encryption-overview.md)。

サービスまたはシステムが許可する限り、確立された接続から、対象のサービスまたはシステムにアクセスできます。 Azure AD OAuth 接続を使用するサービス (Office 365 や Dynamics など) の場合、Logic Apps サービスではアクセス トークンを無期限に更新します。 その他のサービスでは、Logic Apps が更新なしでトークンを使用できる期間が制限されることがあります。 一部のアクション (パスワードの変更など) では、すべてのアクセス トークンが無効になります。

ワークフロー内から接続を作成しますが、接続は独自のリソース定義を持つ個別の Azure リソースです。 これらの接続リソース定義を確認するには、[Azure から Visual Studio にロジック アプリをダウンロード](../logic-apps/manage-logic-apps-with-visual-studio.md)します。 これは、デプロイの準備がほとんど整っている、パラメーター化された有効なロジック アプリ テンプレートを作成するための最も簡単な方法です。

> [!TIP]
> 組織が Logic Apps コネクタを介して特定のリソースにアクセスすることを許可していない場合は、[Azure Policy](../governance/policy/overview.md) を使用して、[そのような接続を作成する機能をブロックする](../logic-apps/block-connections-connectors.md)ことができます。

<a name="firewall-access"></a>

### <a name="firewall-access-for-connections"></a>接続のためのファイアウォール アクセス

トラフィックを制限するファイアウォールを使用しており、かつロジック アプリ ワークフローがそのファイアウォール経由で通信する必要がある場合は、そのロジック アプリ ワークフローが存在する Azure リージョン内の Logic Apps サービスまたはランタイムによって使用される[受信](../logic-apps/logic-apps-limits-and-config.md#inbound) IP アドレスと[送信](../logic-apps/logic-apps-limits-and-config.md#outbound) IP アドレスの両方のアクセスを許可するようにファイアウォールを設定する必要があります。 また、ワークフローでマネージド コネクタ (Office 365 Outlook コネクタや SQL コネクタなど) を使用する場合や、カスタム コネクタを使用する場合は、ファイアウォールで、ロジック アプリの Azure リージョン内の "*すべての*" [マネージド コネクタ送信 IP アドレス](../logic-apps/logic-apps-limits-and-config.md#outbound)のアクセスも許可する必要があります。 詳細については、[ファイアウォールの構成](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)に関するページを確認してください。

## <a name="recurrence-behavior"></a>繰り返しの動作

繰り返しの組み込みトリガー ([繰り返しトリガー](connectors-native-recurrence.md)など) は、Logic Apps ランタイムでネイティブに実行され、最初に接続を作成する必要がある繰り返しの接続ベースのトリガー (Office 365 Outlook コネクタ トリガーなど) とは異なります。

どちらの種類のトリガーでも、特定の開始日時が繰り返しに指定されていない場合は、トリガーの繰り返し設定にかかわらず、ロジック アプリを保存またはデプロイすると、直ちに最初の繰り返しが実行されます。 この動作を回避するには、最初の繰り返しを実行する開始日時を指定してください。

### <a name="recurrence-for-built-in-triggers"></a>組み込みトリガーの繰り返し

繰り返しの組み込みトリガーでは、指定されたタイム ゾーンを含め、設定したスケジュールに従います。 ただし、将来の繰り返しを実行する特定時刻など、その他の詳細なスケジュール オプションが繰り返しに指定されていない場合は、それらの繰り返しは最後のトリガー実行に基づいて行われます。 その結果、ストレージ呼び出し中の待機時間などの要因によって、それらの繰り返しの開始時刻がずれる可能性があります。

詳細については、次のドキュメントを確認してください。

* [Azure Logic Apps で自動化された定期的なタスク、プロセス、ワークフローのスケジュールを設定して実行する](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
* [繰り返しトリガーを使用して繰り返しタスクおよびワークフローを作成、スケジュール設定、および実行する](connectors-native-recurrence.md)
* [繰り返しの問題をトラブルシューティングする](#recurrence-issues)

### <a name="recurrence-for-connection-based-triggers"></a>接続ベース トリガーの繰り返し

Office 365 Outlook など、繰り返しの接続ベースのトリガーでは、実行を制御するドライバーはスケジュールだけではありません。 タイム ゾーンにより、最初の開始時刻のみが決定されます。 後続の実行は、繰り返しのスケジュール、最後のトリガーの実行、"および" 実行時間の移動や予期しない動作を引き起こす可能性があるその他の要因によって決まります。次に例を示します。

* より多くのデータがあるサーバーにトリガーがアクセスするかどうか。これは、トリガーによってすぐに取り込みが試行されます。
* トリガーによって発生するエラーまたは再試行。
* ストレージ呼び出し中の待機時間。
* 夏時間 (DST) の開始および終了時に、指定されたスケジュールが維持されない。
* 次回の実行時刻の発生に影響を与える可能性があるその他の要因。

詳細については、次のドキュメントを確認してください。

* [Azure Logic Apps で自動化された定期的なタスク、プロセス、ワークフローのスケジュールを設定して実行する](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
* [繰り返しの問題をトラブルシューティングする](#recurrence-issues)

<a name="recurrence-issues"></a>

### <a name="troubleshooting-recurrence-issues"></a>繰り返しの問題をトラブルシューティングする

ワークフローが指定した開始時刻に確実に実行され、特に頻度が数日以上の期間である場合、繰り返しが決して見逃されないようにするには、次のソリューションを試してください。

* DST が有効になったときに、ワークフローが引き続き予定時刻に実行されるように、繰り返しを手動で調整します。 そうしないと、開始時刻が DST の開始時には 1 時間先に、DST の終了時には 1 時間前にシフトされます。 詳細および例については、「[夏時間と標準時間での繰り返し](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time)」を確認してください。

* **繰り返し** トリガーを使用している場合は、タイム ゾーン、開始日時を指定します。 また、 **[設定時刻 (時間)]** および **[設定時刻 (分)]** プロパティ ( **[日]** と **[週]** の頻度でのみ使用可能) で、後続の繰り返しを実行する特定の時刻を構成します。 ただし、一部の時間枠では、時間がシフトするときに引き続き問題が発生することがあります。

* 繰り返しが必ず行われるように、**繰り返し** トリガーの代わりに [**スライディング ウィンドウ** トリガー](connectors-native-sliding-window.md)を使用することを検討してください。

## <a name="custom-apis-and-connectors"></a>カスタム API とコネクタ

カスタム コードを実行する API や、コネクタとして使用できない API を呼び出すには、[カスタム API Apps を作成](../logic-apps/logic-apps-create-api-app.md)して、Logic Apps プラットフォームを拡張します。 "任意の" REST または SOAP ベース API 用の[カスタム コネクタを作成](../logic-apps/custom-connector-overview.md)することもできます。その場合、これらの API は、Azure サブスクリプションの任意のロジック アプリで使用できるようになります。 Azure 内でカスタム API Apps またはコネクタを公開し、誰でも使用できるようにするには、[Microsoft の認定を受けるためにコネクタを提出](/connectors/custom-connectors/submit-certification)してください。

## <a name="ise-and-connectors"></a>ISE とコネクタ

Azure 仮想ネットワーク内のリソースに直接アクセスする必要があるワークフローの場合は、専用のリソースでワークフローをビルド、デプロイ、および実行できる、専用の[統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) を作成できます。 ISE の作成の詳細については、[Azure Logic Apps から Azure 仮想ネットワークへの接続](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)に関するページを確認してください。

ISE 内で作成されたカスタム コネクタは、オンプレミス データ ゲートウェイでは動作しません。 ただし、これらのコネクタでは、ISE がホストされている Azure 仮想ネットワークに接続されているオンプレミス データ ソースに直接アクセスできます。 そのため、ISE 内のロジック アプリでは、ほとんどの場合、それらのリソースと通信するときにデータ ゲートウェイは不要です。 ISE の外部で作成したカスタム コネクタがあり、オンプレミス データ ゲートウェイを必要とする場合、ISE 内のロジック アプリから、それらのコネクタを使用できます。

Logic Apps デザイナーでは、組み込みのトリガーとアクションまたは ISE でロジック アプリに使用するマネージド コネクタを参照すると、組み込みのトリガーとアクションでは **CORE** ラベルが表示されるに対して、特に ISE で動作するように設計されたマネージド コネクタでは **ISE** ラベルが表示されます。

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
        このラベルが付いたマネージド コネクタは、ロジック アプリと同じ ISE 内で動作します。 
        \
        \
        Azure 仮想ネットワークに接続されているオンプレミス システムがある場合は、ISE により、ワークフローは[オンプレミス データ ゲートウェイ](../logic-apps/logic-apps-gateway-connection.md)を使用しなくても、そのシステムに直接アクセスできます。 代わりに、そのシステムの **ISE** コネクタ (使用可能な場合)、HTTP アクション、または [カスタム コネクタ](#custom-apis-and-connectors)を使用できます。
        \
        \
        **ISE** コネクタのないオンプレミス システムの場合は、オンプレミス データ ゲートウェイを使用します。 使用できる ISE コネクタを見つけるには、[ISE コネクタ](#ise-and-connectors)を確認してください。
    :::column-end:::
    :::column:::
        ![ISE 以外のコネクタの例](./media/apis-list/example-multi-tenant-connector.png)
        \
        \
        ラベルなし    \
        \
        ラベルのないその他のすべてのコネクタ (これも引き続き使用できます) の場合は、グローバルなマルチテナント Logic Apps サービスで実行します。
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="known-issues"></a>既知の問題

次の表には、Logic Apps コネクタに関する既知の問題が含まれています。

| エラー メッセージ| 説明 | 解決方法 |
|--------------|-------------|------------|
| `Error: BadGateway. Client request id: '{GUID}'` | このエラーは、 1 つ以上の接続で SFTP や SQL などの Azure Active Directory (Azure AD) OAuth 認証がサポートされていないロジック アプリでタグを更新した場合に発生し、それらの接続が切断されます。 | この動作を回避するには、それらのタグを更新しないようにします。 |
||||

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Logic Apps から呼び出しできるカスタム API の作成](/logic-apps/logic-apps-create-api-app)