---
title: コンテナーと BLOB への匿名パブリック読み取りアクセスを防ぐ
titleSuffix: Azure Storage
description: ''
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/06/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: 90d7cd65bbc07524391f34fe0efce2b044664cef
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209158"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>コンテナーと BLOB への匿名パブリック読み取りアクセスを防ぐ

Azure Storage 内のコンテナーと BLOB への匿名パブリック読み取りアクセスは、データを共有するための便利な方法ですが、セキュリティ上のリスクが生じる場合もあります。 匿名アクセスを有効にするときは慎重を期すこと、また、データへの匿名アクセスを評価する方法について理解することが重要です。 運用の複雑さ、人為的なエラー、パブリックにアクセス可能なデータに対する悪意のある攻撃によって、データが侵害され、損失を被る可能性があります。 匿名アクセスは、アプリケーションのシナリオで必要な場合にのみ有効にすることをお勧めします。

既定では、ストレージ アカウントにおいて、適切なアクセス許可を持つユーザーが、コンテナーおよび BLOB へのパブリック アクセスを構成できます。 ストレージ アカウントのレベルでこの機能を無効にし、アカウントのコンテナーと BLOB へのパブリック アクセスを構成できないようにすることができます。

この記事では、ストレージ アカウントに対する匿名の要求を分析する方法と、ストレージ アカウント全体または個々のコンテナーに対する匿名アクセスを防ぐ方法について説明します。

## <a name="detect-anonymous-requests-from-client-applications"></a>クライアント アプリケーションからの匿名要求の検出

ストレージ アカウントでパブリック読み取りアクセスを無効にすると、現在パブリック アクセスが構成されているコンテナーと BLOB への要求が拒否されるリスクがあります。 ストレージ アカウントでパブリック アクセスを無効にすると、そのストレージ アカウント内のすべてのコンテナーのパブリック アクセス設定がオーバーライドされます。 ストレージ アカウントでパブリック アクセスを無効にすると、以後、そのアカウントに対する匿名要求は失敗します。

パブリック アクセスを無効にするとクライアント アプリケーションにどのような影響があるかを理解するために、そのアカウントでログ記録とメトリックを有効にし、一定の期間内の匿名要求のパターンを分析することをお勧めします。 ストレージ アカウントに対する匿名要求の数をメトリックを使って調べ、匿名でアクセスされているコンテナーをログから特定します。

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>メトリックス エクスプローラーを使用した匿名要求の監視

ストレージ アカウントに対する匿名要求を追跡するには、Azure portal で Azure メトリックス エクスプローラーを使用します。 メトリックス エクスプローラーの詳細については、「[Azure メトリックス エクスプローラーの概要](../../azure-monitor/platform/metrics-getting-started.md)」を参照してください。

匿名要求を追跡するメトリックを作成するには、次の手順に従います。

1. Azure Portal のストレージ アカウントに移動します。 **[監視]** セクションで、 **[メトリック]** を選択します。
1. **[メトリックの追加]** を選択します。 **[メトリック]** ダイアログで、次の値を指定します。
    1. [スコープ] フィールドは、ストレージ アカウントの名前に設定したままにします。
    1. **[Metric Namespace]\(メトリックの名前空間\)** を *[Blob]\(BLOB\)* に設定します。 このメトリックでは、BLOB ストレージに対する要求のみが報告されます。
    1. **[メトリック]** フィールドを *[トランザクション]* に設定します。
    1. **[集計]** フィールドを *[合計]* に設定します。

    新しいメトリックには、指定された期間中の BLOB ストレージに対するトランザクション数の合計が表示されます。 結果のメトリックは、次の図のように表示されます。

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="BLOB トランザクションを合計するようにメトリックを構成する方法を示すスクリーンショット":::

1. 次に、 **[フィルターの追加]** ボタンをクリックして、匿名要求のメトリックにフィルターを作成します。
1. **[フィルター]** ダイアログで、次の値を指定します。
    1. **[プロパティ]** の値を *[認証]* に設定します。
    1. **[演算子]** フィールドを等号 (=) に設定します。
    1. **[値]** フィールドを *[匿名]* に設定します。
1. 右上隅で、メトリックを表示する期間を選択します。 1 分から 1 か月の範囲で間隔を指定して、要求の集計のきめ細かさを指示することもできます。

メトリックを構成すると、匿名要求がグラフに表示されるようになります。 次の図は、過去 30 分間に集計された匿名要求を示しています。

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="BLOB ストレージに対する匿名要求の集計を示すスクリーンショット":::

ストレージ アカウントに対して一定数の匿名要求が行われたら通知する警告ルールも構成できます。 詳細については、「[Azure Monitor を使用してメトリック アラートを作成、表示、管理する](../../azure-monitor/platform/alerts-metric.md)」を参照してください。

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>ログを分析して匿名要求を受信しているコンテナーを識別する

Azure Storage のログには、要求の承認方法など、ストレージ アカウントに対して行われた要求の詳細が記録されます。 ログを分析して、どのコンテナーが匿名要求を受信しているかを特定できます。

Azure Monitor の Azure Storage ログ記録 (プレビュー) を使用すると、Azure Storage アカウントに対する要求をログに記録して、匿名要求を評価することができます。 詳細については、「[Azure Storage を監視する](../common/monitor-storage.md)」を参照してください。

Azure Monitor の Azure Storage ログ記録では、ログ クエリを使用したログ データの分析がサポートされています。 ログをクエリするために、Azure Log Analytics ワークスペースを使用できます。 ログ クエリの詳細については、「[チュートリアル: Log Analytics クエリの使用方法](../../azure-monitor/log-query/get-started-portal.md)」を参照してください。

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Azure portal での診断設定の作成

Azure Monitor で Azure Storage のデータをログに記録し、Azure Log Analytics で分析するには、まず、データをログに記録する要求の種類とストレージ サービスを指示する診断設定を作成する必要があります。 Azure portal で診断設定を作成するには、次の手順に従います。

1. [Azure Monitor での Azure Storage ログ記録 (プレビュー)](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u) に申し込みます。
1. 対象の Azure Storage アカウントが含まれるサブスクリプションに、新しい Log Analytics ワークスペースを作成します。 ストレージ アカウントのログ記録を構成したら、Log Analytics ワークスペースでログを参照できるようになります。 詳細については、「[Azure ポータルで Log Analytics ワークスペースを作成する](../../azure-monitor/learn/quick-create-workspace.md)」を参照してください。
1. Azure Portal のストレージ アカウントに移動します。
1. [監視] セクションで、 **[Diagnostic settings (preview)]\(診断設定 (プレビュー)\)** を選択します。
1. BLOB ストレージに対して行われた要求をログに記録するには、 **[Blob]\(BLOB\)** を選択します。
1. **[診断設定の追加]** を選択します。
1. 診断設定の名前を指定します。
1. **[カテゴリの詳細]** の **[ログ]** セクションで、ログを記録する要求の種類を選択します。 すべての匿名要求は読み取り要求になるため、匿名要求をキャプチャするには **StorageRead** を選択します。
1. **[Destination details]\(宛先の詳細\)** で、 **[Send to Log Analytics]\(Log Analytics に送信\)** を選択します。 次の図に示すように、サブスクリプションと、先に作成した Log Analytics ワークスペースを選択します。

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="要求のログを記録するための診断設定の作成方法を示すスクリーンショット":::

診断設定を作成すると、ストレージ アカウントに対する要求が、その設定に従ってログに記録されるようになります。 詳細については、「[Azure でリソース ログとメトリックを収集するための診断設定を作成する](../../azure-monitor/platform/diagnostic-settings.md)」を参照してください。

Azure Monitor の Azure Storage ログで使用できるフィールドのリファレンスについては、「[リソース ログ (プレビュー)](../common/monitor-storage-reference.md#resource-logs-preview)」を参照してください。

#### <a name="query-logs-for-anonymous-requests"></a>匿名要求のログのクエリ

Azure Monitor の Azure Storage ログには、ストレージ アカウントに要求を行うために使用された承認の種類が記録されます。 匿名要求を表示するには、ログ クエリで **AuthenticationType** プロパティをフィルターにします。

BLOB ストレージに対する匿名要求の過去 7 日間のログを取得するには、Log Analytics ワークスペースを開きます。 続いて、次のクエリを新しいログ クエリに貼り付けて実行します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

このクエリに基づいて警告ルールを構成し、匿名要求の通知を受けることもできます。 詳細については、「[Azure Monitor を使用してログ アラートを作成、表示、管理する](../../azure-monitor/platform/alerts-log.md)」を参照してください。

## <a name="remediate-anonymous-public-access"></a>匿名パブリック アクセスの修正

ストレージ アカウント内のコンテナーと BLOB に対する匿名要求を評価した後は、パブリック アクセスを制限または禁止するためのアクションを実行できます。 ストレージ アカウント内の一部のコンテナーをパブリック アクセス可能にする必要がある場合、ストレージ アカウント内のコンテナーごとにパブリック アクセス設定を構成できます。 このオプションを使用すると、最もきめ細かくパブリック アクセスを制御できます。 詳細については、[コンテナーのパブリック アクセス レベルの設定](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container)に関するページを参照してください。

セキュリティを強化するために、ストレージ アカウント全体でパブリック アクセスを無効にすることができます。 ストレージ アカウントに対するパブリック アクセス設定は、そのアカウント内のコンテナーに対する個別設定をオーバーライドします。 ストレージ アカウントに対してパブリック アクセスを無効にすると、パブリック アクセスを許可するように構成されているコンテナーに匿名でアクセスできなくなります。 詳細については、[ストレージ アカウントのパブリック読み取りアクセスの有効化または無効化](anonymous-read-access-configure.md#enable-or-disable-public-read-access-for-a-storage-account)に関するページを参照してください。

特定のコンテナーのパブリック アクセスを許可することが必要なシナリオでは、当該のコンテナーとその BLOB を、パブリック アクセス用に予約されているストレージ アカウントに移動することをお勧めします。 その後、他のストレージ アカウントのパブリック アクセスを無効化できます。

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>BLOB へのパブリック アクセスが許可されていないことの確認

特定の BLOB へのパブリック アクセスが拒否されていることを確認するために、その URL から BLOB のダウンロードを試みることができます。 ダウンロードが成功した場合、BLOB はパブリックにアクセス可能な状態のままです。 ストレージ アカウントでパブリック アクセスが無効なため BLOB にパブリックにアクセスできない場合、このストレージ アカウントではパブリック アクセスが許可されていないというエラー メッセージが表示されます。

次の例は、PowerShell を使用してその URL から BLOB のダウンロードを試みる方法を示しています。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>コンテナーのパブリック アクセス設定の変更が許可されていないことの確認

ストレージ アカウントでパブリック アクセスを無効にした後、コンテナーのパブリック アクセス設定を変更できないことを確認するために、設定の変更を試みることができます。 ストレージ アカウントでパブリック アクセスが無効になっている場合、コンテナーのパブリック アクセス設定の変更は失敗します。

次の例は、PowerShell を使用してコンテナーのパブリック アクセス設定の変更を試みる方法を示しています。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>パブリック アクセスを有効にしたコンテナーの作成が許可されていないことの確認

ストレージ アカウントでパブリック アクセスが無効になっている場合、パブリック アクセスを有効にして新しいコンテナーを作成することはできません。 確認するために、パブリック アクセスを有効にしてコンテナーの作成を試みることができます。

次の例は、PowerShell を使用して、パブリック アクセスが有効なコンテナーの作成を試みる方法を示しています。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。
 
```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

## <a name="next-steps"></a>次のステップ

- [コンテナーと BLOB の匿名パブリック読み取りアクセスを構成する](anonymous-read-access-configure.md)
- [.NET を使用してパブリックのコンテナーと BLOB に匿名でアクセスする](anonymous-read-access-client.md)