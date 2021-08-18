---
title: Azure Virtual Desktop (クラシック) の診断ログ分析 - Azure
description: Azure Virtual Desktop (クラシック) の診断機能でログ分析を使用する方法について説明します。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 0be29603225361b2f275f081c1ad52c5c6c394db
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736168"
---
# <a name="use-log-analytics-for-the-diagnostics-feature-in-azure-virtual-desktop-classic"></a>Azure Virtual Desktop (クラシック) の診断機能でログ分析を使用する

>[!IMPORTANT]
>この内容は、Azure Resource Manager Azure Virtual Desktop オブジェクトをサポートしていない Azure Virtual Desktop (クラシック) に適用されます。 Azure Resource Manager Azure Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../diagnostics-log-analytics.md)を参照してください。

Azure Virtual Desktop では、管理者が単一のインターフェイスを使用して問題を特定できる診断機能が提供されます。 この機能により、Azure Virtual Desktop ロールが割り当てられているユーザーがサービスを使用するたびに、診断情報がログに記録されます。 各ログには、そのアクティビティに関与した Azure Virtual Desktop ロールに関する情報、そのセッション中に表示されるすべてのエラー メッセージ、テナント情報、ユーザー情報が格納されます。 診断機能は、ユーザーと管理者の両方のアクションのアクティビティ ログを作成します。 各アクティビティ ログは、次の 3 つの主なカテゴリに分類されます。

- フィード サブスクリプション アクティビティ: ユーザーが Microsoft リモート デスクトップ アプリケーションを通じてフィードに接続しようとする場合。
- 接続アクティビティ: ユーザーが Microsoft リモート デスクトップ アプリケーションを通じてデスクトップまたは RemoteApp に接続しようとする場合。
- 管理アクティビティ: 管理者がホスト プールの作成、アプリ グループへのユーザーの割り当て、ロール割り当ての作成などの管理操作をシステムに対して実行する場合。

診断ロール サービス自体が Azure Virtual Desktop の一部であるため、Azure Virtual Desktop に到達しない接続は診断結果に表示されません。 Azure Virtual Desktop 接続の問題は、ユーザーにネットワーク接続の問題が発生しているときに発生する場合があります。

## <a name="why-you-should-use-log-analytics"></a>Log Analytics を使用する理由

シングルユーザーのトラブルシューティングを超える、Azure クライアントの診断データ分析のために、Log Analytics を使用することをお勧めします。 VM パフォーマンス カウンターを Log Analytics に取り込むことができるので、1 つのツールでデプロイに関する情報を収集できます。

## <a name="before-you-get-started"></a>開始する前に

診断機能で Log Analytics を使用するには、まず[ワークスペースを作成する](../../azure-monitor/logs/quick-create-workspace.md)必要があります。

ワークスペースを作成したら、「[Windows コンピューターを Azure Monitor に接続する](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)」の指示に従って、次の情報を取得します。

- ワークスペース ID
- ワークスペースの主キー

この情報は後のセットアップ プロセスで必要になります。

## <a name="push-diagnostics-data-to-your-workspace"></a>診断データをワークスペースにプッシュする

Azure Virtual Desktop テナントからワークスペースの Log Analytics に診断データをプッシュできます。 この機能は、最初にテナントを作成する時点で、ワークスペースをテナントにリンクすることにより設定できます。または、既存のテナントを使用して後で設定することもできます。

新しいテナントの設定中にテナントを Log Analytics ワークスペースにリンクするには、次のコマンドレットを実行して、TenantCreator ユーザー アカウントで Azure Virtual Desktop にサインインします。

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

新しいテナントではなく、既存のテナントをリンクする場合は、代わりに次のコマンドレットを実行します。

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

これらのコマンドレットは、Log Analytics にリンクする各テナントに対して実行する必要があります。

>[!NOTE]
>テナントの作成時に Log Analytics ワークスペースをリンクしたくない場合は、代わりに `New-RdsTenant` コマンドレットを実行します。

## <a name="cadence-for-sending-diagnostic-events"></a>診断イベントの送信頻度

診断イベントは、完了時に Log Analytics に送信されます。

## <a name="example-queries"></a>クエリの例

次のクエリの例は、システムで最も頻繁に発生するアクティビティのレポートを診断機能がどのように生成するかを示しています。

最初の例は、サポートされているリモート デスクトップ クライアントを使用してユーザーが開始した接続アクティビティを示しています。

```powershell
WVDActivityV1_CL

| where Type_s == "Connection"

| join kind=leftouter (

    WVDErrorV1_CL

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g 

| join  kind=leftouter (

    WVDCheckpointV1_CL

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g

|project-away ActivityId_g, ActivityId_g1
```

次のクエリの例は、管理者によるテナントに対する管理アクティビティを示しています。

```powershell
WVDActivityV1_CL

| where Type_s == "Management"

| join kind=leftouter (

    WVDErrorV1_CL

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g 

| join  kind=leftouter (

    WVDCheckpointV1_CL

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g

|project-away ActivityId_g, ActivityId_g1
```

## <a name="stop-sending-data-to-log-analytics"></a>Log Analytics へのデータ送信を停止する

既存のテナントから Log Analytics へのデータ送信を停止するには、次のコマンドレットを実行して、空の文字列を設定します。

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

このコマンドレットは、データ送信を停止する各テナントに対して実行する必要があります。

## <a name="next-steps"></a>次のステップ

診断機能で特定できる一般的なエラー シナリオについては、「[問題の特定と診断](diagnostics-role-service-2019.md#common-error-scenarios)」をご確認ください。
