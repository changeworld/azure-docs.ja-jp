---
title: 共有キーによる承認を禁止する (プレビュー)
titleSuffix: Azure Storage
description: 要求の承認に Azure AD を使用するようクライアントに要求するため、共有キーで承認されるストレージ アカウントへの要求を許可しないようにすることができます (プレビュー)。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/20/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: 9bf656989dc331fdd4ce044126ea9d0be9414930
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2020
ms.locfileid: "90088801"
---
# <a name="prevent-shared-key-authorization-for-an-azure-storage-account-preview"></a>Azure ストレージ アカウントの共有キーによる承認を禁止する (プレビュー)

Azure ストレージ アカウントに対するセキュリティで保護されたすべての要求を承認する必要があります。 既定では、Azure Active Directory (Azure AD) の資格情報、または共有キーによる承認用のアカウント アクセス キーを使用して、要求を承認することができます。 これら 2 種類の承認では、Azure AD の方がセキュリティが優れ、共有キーより使いやすいので、Microsoft ではそちらをお勧めします。 要求の承認に Azure AD を使用するようクライアントに要求するため、共有キーで承認されるストレージ アカウントへの要求を許可しないようにすることができます (プレビュー)。

ストレージ アカウントの共有キーによる承認を禁止すると、それ以降、そのアカウントに対するアカウント アクセスキーによる承認の要求はすべて、Azure Storage によって拒否されます。 Azure AD によって承認されるセキュリティで保護された要求のみが成功します。 Azure AD の使用に関する詳細については、「[Azure Active Directory を使用して BLOB とキューへのアクセスを承認する](storage-auth-aad.md)」を参照してください。

> [!WARNING]
> Azure Storage では、Azure AD の承認は、Blob Storage と Queue storage に対する要求についてだけサポートされています。 ストレージ アカウントの共有キーによる承認を禁止した場合、共有キーによる承認を使用する Azure Files Storage または Table Storage への要求は失敗します。
>
> プレビュー期間中、アカウント アクセス キーを使用して生成された Shared Access Signature (SAS) トークンを使用する、Azure Files Storage または Table Storage への要求は、共有キーによる承認が許可されていない場合に成功します。 詳細については、「[プレビューについて](#about-the-preview)」を参照してください。
>
> ストレージ アカウントに対する共有キー アクセスを禁止しても、Azure Files への SMB 接続には影響しません。
>
> 共有キーによるアカウントへのアクセスを禁止する前に、Azure Files Storage または Table Storage のデータを別のストレージ アカウントに移行すること、または Azure Files Storage または Table Storage のワークロードをサポートするストレージ アカウントに対しては、この設定を適用しないことをお勧めします。

この記事では、共有キーによる承認を使用して送信された要求を検出する方法と、ストレージ アカウントの共有キーによる承認を修正する方法について説明します。 プレビューに登録する方法については、「[プレビューについて](#about-the-preview)」を参照してください。

## <a name="detect-the-type-of-authorization-used-by-client-applications"></a>クライアント アプリケーションによって使用されている承認の種類を検出する

ストレージ アカウントの共有キーによる承認を禁止すると、共有キーによる承認にアカウント アクセス キーを使用しているクライアントからの要求は失敗します。 この変更を行う前に、共有キーによる承認を禁止したときのクライアント アプリケーションに対する影響を理解するには、ストレージ アカウントのログとメトリックを有効にします。 その後、一定期間にわたってアカウントに対する要求のパターンを分析することで、要求がどのように承認されているかを判断できます。

ストレージ アカウントが受信している要求のうち、共有キーまたは Shared Access Signature (SAS) で承認されているものの数を確認するには、メトリックを使用します。 それらの要求を送信しているクライアントを特定するには、ログを使用します。

プレビュー期間中に Shared Access Signature を使用して行われた要求を解釈する方法の詳細については、「[プレビューについて](#about-the-preview)」を参照してください。

### <a name="monitor-how-many-requests-are-authorized-with-shared-key"></a>共有キーを使用して承認された要求の数を監視する

ストレージ アカウントへの要求がどのように承認されているかを追跡するには、Azure portal で Azure メトリックス エクスプローラーを使用します。 メトリックス エクスプローラーの詳細については、「[Azure メトリックス エクスプローラーの概要](../../azure-monitor/platform/metrics-getting-started.md)」を参照してください。

共有キーまたは SAS を使用して行われた要求を追跡するメトリックを作成するには、次の手順のようにします。

1. Azure Portal のストレージ アカウントに移動します。 **[監視]** セクションで、 **[メトリック]** を選択します。
1. **[メトリックの追加]** を選択します。 **[メトリック]** ダイアログで、次の値を指定します。
    1. **[スコープ]** フィールドは、ストレージ アカウントの名前が設定されたままにします。
    1. **[メトリック名前空間]** を *Account* に設定します。 このメトリックでは、ストレージ アカウントに対するすべての要求が報告されます。
    1. **[メトリック]** フィールドを *[トランザクション]* に設定します。
    1. **[集計]** フィールドを *[合計]* に設定します。

    新しいメトリックでは、指定した期間内のストレージ アカウントに対するトランザクション数の合計が表示されます。 結果のメトリックは、次の図のように表示されます。

    :::image type="content" source="media/shared-key-authorization-prevent/configure-metric-account-transactions.png" alt-text="共有キーまたは SAS を使用して行われたトランザクション数を合計するようにメトリックを構成する方法を示すスクリーンショット":::

1. 次に、 **[フィルターの追加]** ボタンを選択して、要求の種類に関するメトリックのフィルターを作成します。
1. **[フィルター]** ダイアログで、次の値を指定します。
    1. **[プロパティ]** の値を *[認証]* に設定します。
    1. **[演算子]** フィールドを等号 (=) に設定します。
    1. **[値]** フィールドで、 *[アカウント キー]* と *[SAS]* を選択します。
1. 右上隅で、メトリックを表示する期間を選択します。 1 分から 1 か月の範囲で間隔を指定して、要求の集計のきめ細かさを指示することもできます。 たとえば、 **[期間]** を 30 日に設定し、 **[時間の粒度]** を 1 日に設定すると、過去 30 日間について 1 日ごとに集計された要求が表示されます。

メトリックを構成すると、ストレージ アカウントに対する要求がグラフに表示されるようになります。 次の図では、共有キーを使用して承認されたか、SAS トークンを使用して行われた要求が示されています。 過去 30 日間の要求が、1 日ごとに集計されています。

:::image type="content" source="media/shared-key-authorization-prevent/metric-shared-key-requests.png" alt-text="共有キーまたは SAS を使用して行われたトランザクション数を合計するようにメトリックを構成する方法を示すスクリーンショット":::

ストレージ アカウントで共有キーを使用して承認された要求が一定数に達したら通知する警告ルールを構成することもできます。 詳細については、「[Azure Monitor を使用してメトリック アラートを作成、表示、管理する](../../azure-monitor/platform/alerts-metric.md)」を参照してください。

### <a name="analyze-logs-to-identify-clients-that-are-authorizing-requests-with-shared-key-or-sas"></a>ログを分析し、共有キーまたは SAS による要求を承認しているクライアントを特定する

Azure Storage のログには、要求の承認方法など、ストレージ アカウントに対して行われた要求の詳細が記録されます。 ログを分析することで、共有キーまたは SAS トークンによる要求を承認しているクライアントを特定できます。

承認方法を評価するために、Azure ストレージ アカウントに対する要求をログに記録するには、Azure Monitor の Azure Storage ログ (プレビュー) を使用できます。 詳細については、「[Azure Storage を監視する](../common/monitor-storage.md)」を参照してください。

Azure Monitor の Azure Storage ログ記録では、ログ クエリを使用したログ データの分析がサポートされています。 ログに対してクエリを実行するために、Azure Log Analytics ワークスペースを使用できます。 ログ クエリの詳細については、「[チュートリアル: Log Analytics クエリの使用方法](../../azure-monitor/log-query/get-started-portal.md)」を参照してください。

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Azure portal での診断設定の作成

Azure Monitor で Azure Storage のデータをログに記録し、Azure Log Analytics で分析するには、まず、データをログに記録する要求の種類とストレージ サービスを指示する診断設定を作成する必要があります。 Azure portal で診断設定を作成するには、これらの手順に従います。

1. [Azure Monitor の Azure Storage ログ記録 (プレビュー)](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u) に登録します。
1. Azure ストレージ アカウントが含まれるサブスクリプションに新しい Log Analytics ワークスペースを作成するか、既存の Log Analytics ワークスペースを使用します。 ストレージ アカウントのログ記録を構成した後、Log Analytics ワークスペースでログを使用できるようになります。 詳細については、「[Azure ポータルで Log Analytics ワークスペースを作成する](../../azure-monitor/learn/quick-create-workspace.md)」を参照してください。
1. Azure Portal のストレージ アカウントに移動します。
1. [監視] セクションで、 **[診断設定 (プレビュー)]** を選択します。
1. 要求をログに記録する Azure Storage サービスを選択します。 たとえば、Blob Storage に対する要求をログに記録するには、 **[Blob]** を選択します。
1. **[診断設定の追加]** を選択します。
1. 診断設定の名前を指定します。
1. **[カテゴリの詳細]** の **[ログ]** セクションで、**StorageRead**、**StorageWrite**、**StorageDelete** を選択して、選択したサービスに対するすべてのデータ要求をログに記録します。
1. **[宛先の詳細]** で、 **[Log Analytics への送信]** を選択します。 以下の図に示すように、ご利用のサブスクリプションと、先ほど作成した Log Analytics ワークスペースを選択します。

    :::image type="content" source="media/shared-key-authorization-prevent/create-diagnostic-setting-logs.png" alt-text="共有キーまたは SAS を使用して行われたトランザクション数を合計するようにメトリックを構成する方法を示すスクリーンショット":::

ストレージ アカウント内の Azure Storage リソースの種類ごとに、診断設定を作成できます。

診断設定を作成した後、ストレージ アカウントに対する要求が、その設定に従ってログに記録されるようになります。 詳細については、[Azure でリソース ログとメトリックを収集するための診断設定の作成](../../azure-monitor/platform/diagnostic-settings.md)に関するページを参照してください。

Azure Monitor の Azure Storage ログで使用できるフィールドのリファレンスについては、「[リソース ログ (プレビュー)](../common/monitor-storage-reference.md#resource-logs-preview)」を参照してください。

#### <a name="query-logs-for-requests-made-with-shared-key-or-sas"></a>共有キーまたは SAS を使用して行われた要求のログのクエリを実行する

Azure Monitor の Azure Storage ログには、ストレージ アカウントに要求を行うために使用された承認の種類が記録されます。 過去 7 日間に共有キーまたは SAS を使用して行われて承認された要求のログを取得するには、Log Analytics ワークスペースを開きます。 次に、以下のクエリを新しいログ クエリに貼り付けて実行します。 このクエリでは、共有キーまたは SAS で承認された要求を最も頻繁に送信した 10 個の IP アドレスが表示されます。

```kusto
StorageBlobLogs
| where AuthenticationType in ("AccountKey", "SAS") and TimeGenerated > ago(7d)
| summarize count() by CallerIpAddress, UserAgentHeader, AccountName
| top 10 by count_ desc
```

このクエリに基づいて警告ルールを構成し、共有キーまたは SAS で承認された要求に関する通知を受け取ることもできます。 詳細については、「[Azure Monitor を使用してログ アラートを作成、表示、管理する](../../azure-monitor/platform/alerts-log.md)」を参照してください。

## <a name="remediate-authorization-via-shared-key"></a>共有キーによる承認を修正する

ストレージ アカウントへの要求がどのように承認されているかを分析したら、共有キーによるアクセスを禁止するアクションを実行できます。 ただし、まず、共有キーによる承認を使用しているアプリケーションを、代わりに Azure AD を使用するように更新する必要があります。 「[クライアント アプリケーションによって使用されている承認の種類を検出する](#detect-the-type-of-authorization-used-by-client-applications)」の説明に従って、ログとメトリックを監視し、移行を追跡することができます。 BLOB とキューのデータでの Azure AD の使用に関する詳細については、「[Azure Active Directory を使用して BLOB とキューへのアクセスを承認する](storage-auth-aad.md)」を参照してください。

共有キーを使用して承認される要求を安全に拒否できることを確信したら、ストレージ アカウントの **AllowSharedKeyAccess** プロパティを **false** に設定できます。

**AllowSharedKeyAccess** プロパティは既定では未設定で、明示的に設定されるまで値を返しません。 このプロパティの値が **null** または **true** の場合、ストレージ アカウントでは共有キーによって承認される要求が許可されます。

> [!WARNING]
> 現在、共有キーを使用してストレージ アカウントのデータにアクセスしているクライアントがいる場合、ストレージ アカウントへの共有キーによるアクセスを禁止する前に、それらのクライアントを Azure AD に移行することをお勧めします。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal でストレージ アカウントに対する共有キーによる承認を禁止するには、次の手順のようにします。

1. Azure Portal のストレージ アカウントに移動します。
1. **[設定]** から **[構成]** 設定を探します。
1. **[Allow shared key access]\(共有キーによるアクセスを許可する\)** を **[無効]** に設定します。

    :::image type="content" source="media/shared-key-authorization-prevent/shared-key-access-portal.png" alt-text="共有キーまたは SAS を使用して行われたトランザクション数を合計するようにメトリックを構成する方法を示すスクリーンショット":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して、ストレージ アカウントに対する共有キーによる承認を禁止するには、Azure CLI バージョン 2.9.1 以降をインストールします。 詳細については、「 [Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。 次に、新規または既存のストレージ アカウントに対して **allowSharedKeyAccess** プロパティを構成します。

次の例では、Azure CLI で **allowSharedKeyAccess** プロパティを設定する方法を示します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli-interactive
$storage_account_id=$(az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowSharedKeyAccess=false

az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowSharedKeyAccess \
    --output tsv
```

---

共有キーによる承認を無効にした後は、共有キーによる承認を使用してストレージ アカウントに要求を行うと、エラー コード 403 (許可されていません) で失敗します。 そのストレージ アカウントではキーに基づく承認が許可されていないことを示すエラーが、Azure Storage から返されます。

### <a name="verify-that-shared-key-access-is-not-allowed"></a>共有キーによるアクセスが許可されないことを確認する

共有キーによる承認が許可されないことを確認するには、アカウント アクセス キーを使用してデータ操作の呼び出しを試みます。 次の例では、アクセス キーを使用してコンテナーの作成を試みています。 ストレージ アカウントで共有キーによる承認が許可されていない場合、この呼び出しは失敗します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

> [!NOTE]
> 匿名の要求は承認されず、ストレージ アカウントとコンテナーが匿名パブリック読み取りアクセス用に構成されている場合は続行されます。 詳細については、[コンテナーと BLOB の匿名パブリック読み取りアクセスの構成](../blobs/anonymous-read-access-configure.md)に関するページを参照してください。

### <a name="check-the-shared-key-access-setting-for-multiple-accounts"></a>複数のアカウントに対する共有キーによるアクセスの設定を確認する

ストレージ アカウントのセットについて共有キーによるアクセスの設定を最もすばやく確認するには、Azure portal の Azure Resource Graph エクスプローラーを使用します。 Resource Graph エクスプローラーの使用の詳細については、「[クイック スタート:Azure Resource Graph エクスプローラーを使用して初めての Resource Graph クエリを実行する](/azure/governance/resource-graph/first-query-portal)」を参照してください。

Resource Graph エクスプローラーで次のクエリを実行すると、ストレージ アカウントの一覧が返され、各アカウントに対する共有キーによるアクセスの設定が表示されます。

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowSharedKeyAccess = parse_json(properties).allowSharedKeyAccess
| project subscriptionId, resourceGroup, name, allowSharedKeyAccess
```

## <a name="understand-how-disallowing-shared-key-affects-sas-tokens"></a>共有キーを禁止したときの SAS トークンに対する影響を理解する

ストレージ アカウントに対して共有キーを禁止すると、Azure Storage では、SAS の種類と、要求の対象のサービスに基づいて、SAS トークンが処理されます。 次の表では、ストレージ アカウントの **AllowSharedKeyAccess** プロパティが **false** のときの、SAS の各種類の承認方法と、Azure Storage によるその SAS の処理方法を示します。

| SAS の種類 | 承認の種類 | AllowSharedKeyAccess が false の場合の動作 |
|-|-|-|
| ユーザー委任 SAS(BLOB ストレージのみ) | Azure AD | 要求は許可されます。 Microsoft では、セキュリティ向上のため、可能な限りユーザー委任 SAS を使用することを推奨しています。 |
| サービス SAS | 共有キー | Blob Storage の場合、要求は拒否されます。 Queue storage、Table Storage、Azure Files の場合、要求は許可されます。 詳細については、「**プレビューについて**」セクションの「[AllowSharedKeyAccess が false のとき、キュー、テーブル、ファイルについては、SAS トークンを使用する要求は許可される](#requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false)」を参照してください。 |
| アカウント SAS | 共有キー | Blob Storage の場合、要求は拒否されます。 Queue storage、Table Storage、Azure Files の場合、要求は許可されます。 詳細については、「**プレビューについて**」セクションの「[AllowSharedKeyAccess が false のとき、キュー、テーブル、ファイルについては、SAS トークンを使用する要求は許可される](#requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false)」を参照してください。 |

Shared Access Signature の詳細については、「[Shared Access Signatures (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](storage-sas-overview.md)」を参照してください。

## <a name="consider-compatibility-with-other-azure-tools-and-services"></a>Azure の他のツールおよびサービスとの互換性を検討する

多くの Azure サービスは、Azure Storage との通信に共有キーによる承認が使用されています。 ストレージ アカウントに対する共有キーによる承認を禁止すると、これらのサービスはそのアカウントのデータにアクセスできなくなり、アプリケーションに悪影響が及ぶ可能性があります。

一部の Azure ツールでは、Azure AD 承認を使用して Azure Storage にアクセスするオプションが提供されています。 次の表では、一般的な Azure ツールと、Azure AD を使用して Azure Storage への要求を承認できるかどうかに関する注意事項を示します。

| Azure ツール | Azure Storage に対する Azure AD 承認 |
|-|-|
| Azure portal | サポートされています。 Azure portal からの Azure AD アカウントを使用した承認の詳細については、「[Azure portal で BLOB データへのアクセスの承認方法を選択する](../blobs/authorize-blob-access-portal.md)」を参照してください。 |
| AzCopy | Blob Storage の場合、サポートされています。 AzCopy の操作の承認については、AzCopy のドキュメントの「[認証資格情報の提供方法を選択する](storage-use-azcopy-v10.md#choose-how-youll-provide-authorization-credentials)」を参照してください。 |
| Azure ストレージ エクスプローラー | Blob Storage と Azure Data Lake Storage Gen2 の場合にのみ、サポートされています。 Queue storage に対する Azure AD のアクセスはサポートされていません。 正しい Azure AD テナントを選択してください。 詳細については、「[Storage Explorer の概要](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows#sign-in-to-azure)」を参照してください |
| Azure PowerShell | サポートされています。 Azure AD で BLOB またはキューの操作の PowerShell コマンドを承認する方法の詳細については、「[Azure AD の資格情報を使用して PowerShell コマンドを実行して BLOB データにアクセスする](../blobs/authorize-active-directory-powershell.md)」または「[Azure AD 資格情報を使用して PowerShell コマンドを実行してキューのデータにアクセスする](../queues/authorize-active-directory-powershell.md)」を参照してください。 |
| Azure CLI | サポートされています。 BLOB およびキューのデータにアクセスするために、Azure AD で Azure CLI コマンドを承認する方法については、[Azure AD 資格情報を使用して Azure CLI コマンドを実行して BLOB またはキューのデータにアクセスする方法](authorize-data-operations-cli.md)に関する記事を参照してください。 |
| Azure IoT Hub | サポートされています。 詳細については、[IoT Hub による仮想ネットワークのサポート](../../iot-hub/virtual-network-support.md)に関する記事を参照してください。 |
| Azure Cloud Shell | Azure Cloud Shell は、Azure portal の統合シェルです。 Azure Cloud Shell では、ストレージ アカウントの Azure ファイル共有で永続化するためのファイルがホストされています。 そのストレージ アカウントで共有キーによる承認が許可されていない場合、これらのファイルはアクセスできなくなります。 詳細については、「[Microsoft Azure Files ストレージの接続](/azure/cloud-shell/overview#connect-your-microsoft-azure-files-storage)」を参照してください。 <br /><br /> Azure Cloud Shell でコマンドを実行して、共有キーによるアクセスが禁止されているストレージ アカウントを管理するには、最初に、ロールベースのアクセス制御 (RBAC) で、これらのアカウントに必要なアクセス許可が付与されていることを確認します。 詳細については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](../../role-based-access-control/overview.md)」を参照してください。 |

## <a name="about-the-preview"></a>プレビューについて

共有キーによる認証の禁止に関するプレビューは、Azure パブリック クラウドで利用できます。 Azure Resource Manager デプロイ モデルを使用するストレージ アカウントのみでサポートされています。 Azure Resource Manager デプロイ モデルを使用しているストレージ アカウントの詳細については、「[ストレージ アカウントの種類](storage-account-overview.md#types-of-storage-accounts)」を参照してください。

プレビューに登録するには、「[Azure Storage での共有キーによるアクセスの許可の制限付きパブリック プレビュー](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUN1o4TUtUUzZBV0JYVlhKQ1FITDlVUUU0Ui4u)」を参照してください。

> [!IMPORTANT]
> このプレビューは、非運用環境でのみの使用を意図しています。

プレビューには、次のセクションで説明する制限が含まれています。

### <a name="metrics-and-logging-report-all-requests-made-with-a-sas-regardless-of-how-they-are-authorized"></a>承認方法に関係なく、SAS を使用して行われたすべての要求をメトリックとログで報告する

Azure Monitor での Azure のメトリックとログでは、プレビュー段階のさまざまな種類の共有アクセス署名は区別されません。 Azure メトリックス エクスプローラーの **[SAS]** フィルター、および Azure Monitor の Azure Storage ログでの **[SAS]** フィールドのどちらでも、すべての種類の SAS で承認された要求が報告されます。 ただし、共有アクセス署名の種類が異なると、承認方法が異なり、共有キー アクセスが禁止されたときの動作が異なります。

- サービス SAS トークンまたはアカウント SAS トークンは、共有キーを使用して承認されるので、**AllowSharedKeyAccess** プロパティが **false** に設定されていると、Blob Storage への要求では許可されません。
- ユーザー委任 SAS は、Azure AD を使用して承認されるので、**AllowSharedKeyAccess** プロパティが **false** に設定されていても、Blob Storage への要求で許可されます。

ストレージ アカウントへのトラフィックを評価するときは、「[クライアント アプリケーションによって使用されている承認の種類を検出する](#detect-the-type-of-authorization-used-by-client-applications)」で説明されているメトリックとログに、ユーザー委任 SAS を使用して行われた要求が含まれている可能性があることに注意してください。 **AllowSharedKeyAccess** プロパティが **false** に設定されているときの、SAS に対する Azure Storage の応答方法の詳細については、「[共有キーを禁止したときの SAS トークンに対する影響を理解する](#understand-how-disallowing-shared-key-affects-sas-tokens)」を参照してください。

### <a name="requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false"></a>AllowSharedKeyAccess が false のとき、キュー、テーブル、ファイルについては、SAS トークンを使用する要求は許可される

プレビュー期間中にストレージ アカウントに対する共有キー アクセスを禁止すると、キュー、テーブル、または Azure Files リソースを対象とする共有アクセス署名は、引き続き許可されます。 この制限は、サービス SAS トークンとアカウント SAS トークンの両方に適用されます。 どちらの種類の SAS も、共有キーで承認されます。

## <a name="next-steps"></a>次のステップ

- [Azure Storage 内のデータへのアクセスの承認](storage-auth.md)
- [Azure Active Directory を使用して BLOB とキューへのアクセスを承認する](storage-auth-aad.md)
- [共有キーを使用して承認する](/rest/api/storageservices/authorize-with-shared-key)