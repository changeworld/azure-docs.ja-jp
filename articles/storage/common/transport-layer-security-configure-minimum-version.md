---
title: ストレージ アカウントに必要な最小バージョンのトランスポート層セキュリティ (TLS) を構成する
titleSuffix: Azure Storage
description: クライアントで Azure Storage に対して要求を行う場合に最小バージョンのトランスポート層セキュリティ (TLS) を要求するように、ストレージ アカウントを構成します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: ab83f0ee656dfc717284c1e26d10dcb814fe1c9e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209130"
---
# <a name="configure-minimum-required-version-of-transport-layer-security-tls-for-a-storage-account"></a>ストレージ アカウントに必要な最小バージョンのトランスポート層セキュリティ (TLS) を構成する

クライアント アプリケーションと Azure Storage アカウントの間の通信は、トランスポート層セキュリティ (TLS) を使用して暗号化されます。 TLS は、インターネットを介してクライアントとサービスの間のプライバシーおよびデータ整合性を確保する標準の暗号化プロトコルです。 TLS の詳細については、「[Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)」を参照してください。

現在、Azure Storage では、次の 3 つのバージョンの TLS プロトコルがサポートされています: 1.0、1.1、および 1.2。 TLS 1.2 は、TLS の最も安全なバージョンです。 Azure Storage によって、パブリック HTTPs エンドポイントで TLS 1.2 が使用されますが、下位互換性を確保するために TLS 1.0 と TLS 1.1 が引き続きサポートされます。

既定では、Azure Storage アカウントで、クライアントによる最も古いバージョンの TLS、TLS 1.0、およびそれ以降でのデータの送受信が許可されます。 より厳密なセキュリティ対策を実施するために、クライアントで新しいバージョンの TLS を使用してデータを送受信することを要求するように、ストレージ アカウントを構成することができます。 ストレージ アカウントで最小バージョンの TLS が要求される場合、以前のバージョンで行われた要求はすべて失敗します。

この記事では、クライアントで最小バージョンの TLS を使用して要求を送信することを要求するように、ストレージ アカウントを構成する方法について説明します。 クライアント アプリケーションから要求を送信するときに特定のバージョンの TLS を指定する方法については、「[クライアント アプリケーションのトランスポート層セキュリティ (TLS) を構成する](transport-layer-security-configure-client-version.md)」を参照してください。

## <a name="detect-the-tls-version-used-by-client-applications"></a>クライアント アプリケーションによって使用される TLS のバージョンを検出する

ストレージ アカウントに TLS の最小バージョンを適用すると、以前のバージョンの TLS を使用してデータを送信するクライアントからの要求を拒否する危険性があります。 TLS の最小バージョンの構成がクライアント アプリケーションにどのように影響する可能性があるかを理解するために、Microsoft では、Azure Storage アカウントのログ記録を有効にし、一定期間後にログを分析して、クライアント アプリケーションによって使用される TLS のバージョンを特定することをお勧めします。

Azure Storage アカウントに対する要求をログに記録し、クライアントによって使用される TLS のバージョンを特定するために、Azure Monitor の Azure Storage ログ記録 (プレビュー) を使用することができます。 詳細については、「[Azure Storage を監視する](monitor-storage.md)」を参照してください。

Azure Monitor の Azure Storage ログ記録では、ログ クエリを使用したログ データの分析がサポートされています。 ログに対してクエリを実行するために、Azure Log Analytics ワークスペースを使用できます。 ログ クエリの詳細については、「[チュートリアル: Log Analytics クエリの使用方法](../../azure-monitor/log-query/get-started-portal.md)」を参照してください。

Azure Monitor で Azure Storage のデータをログに記録し、Azure Log Analytics で分析するには、まず、データをログに記録する要求の種類とストレージ サービスを示す診断設定を作成する必要があります。 Azure portal で診断設定を作成するには、これらの手順に従います。

1. [Azure Monitor の Azure Storage ログ記録 (プレビュー)](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u) に登録します。
1. ご利用の Azure Storage アカウントが含まれるサブスクリプションに、新しい Log Analytics ワークスペースを作成します。 ストレージ アカウントのログ記録を構成した後、Log Analytics ワークスペースでログを使用できるようになります。 詳細については、「[Azure ポータルで Log Analytics ワークスペースを作成する](../../azure-monitor/learn/quick-create-workspace.md)」を参照してください。
1. Azure Portal のストレージ アカウントに移動します。
1. [監視] セクションで、 **[診断設定 (プレビュー)]** を選択します。
1. 要求をログに記録する Azure Storage サービスを選択します。 たとえば、Blob Storage に対する要求をログに記録するには、 **[Blob]** を選択します。
1. **[診断設定の追加]** を選択します。
1. 診断設定の名前を指定します。
1. **[カテゴリの詳細]** の **[ログ]** セクションで、ログを記録する要求の種類を選択します。 読み取り、書き込み、および削除要求をログに記録できます。 たとえば、**StorageRead** と **StorageWrite** を選択すると、選択されたサービスへの読み取りおよび書き込み要求がログに記録されます。
1. **[宛先の詳細]** で、 **[Log Analytics への送信]** を選択します。 以下の図に示すように、ご利用のサブスクリプションと、先ほど作成した Log Analytics ワークスペースを選択します。

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="要求のログを記録するための診断設定の作成方法を示すスクリーンショット":::

診断設定を作成した後、ストレージ アカウントに対する要求が、その設定に従ってログに記録されるようになります。 詳細については、[Azure でリソース ログとメトリックを収集するための診断設定の作成](../../azure-monitor/platform/diagnostic-settings.md)に関するページを参照してください。

Azure Monitor の Azure Storage ログで使用できるフィールドのリファレンスについては、「[リソース ログ (プレビュー)](monitor-storage-reference.md#resource-logs-preview)」を参照してください。

### <a name="query-logged-requests-by-tls-version"></a>TLS バージョンでログに記録された要求に対してクエリを実行する

Azure Monitor の Azure Storage ログには、ストレージ アカウントに要求を送信するために使用される TLS のバージョンが含まれます。 **TlsVersion** プロパティを使用して、ログに記録された要求の TLS のバージョンを確認します。

過去 7 日間のログを取得し、各バージョンの TLS で Blob Storage に対して行われた要求の数を特定するには、Log Analytics ワークスペースを開きます。 次に、以下のクエリを新しいログ クエリに貼り付けて実行します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

結果には、各バージョンの TLS で行われた要求の数が表示されます。

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="TLS のバージョンを返す Log Analytics クエリの結果を示すスクリーンショット":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>呼び出し元の IP アドレスとユーザー エージェント ヘッダーでログに記録された要求に対してクエリを実行する

Azure Monitor の Azure Storage ログには、ストレージ アカウントにアクセスしたクライアント アプリケーションを評価するのに役立つ、呼び出し元の IP アドレスとユーザー エージェント ヘッダーも含まれます。 これらの値を分析して、新しいバージョンの TLS を使用するようにクライアント アプリケーションを更新する必要があるかどうか、または、TLS の最小バージョンで送信されない場合にクライアントの要求を失敗させることを受け入れられるかどうかを判断できます。

過去 7 日間のログを取得し、TLS 1.2 より前のバージョンの TLS で要求を行ったクライアントを特定するには、新しいログ クエリに次のクエリを貼り付けて実行します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="configure-the-minimum-tls-version-for-an-account"></a>アカウントの TLS の最小バージョンを構成する

ストレージ アカウントの TLS の最小バージョンを構成するには、Azure portal または Azure CLI を使用して、アカウントの **minimumTlsVersion** バージョンを設定します。 このプロパティは、Azure Resource Manager デプロイ モデルで作成されたすべてのストレージ アカウントで使用できます。 詳細については、「[ストレージ アカウントの概要](storage-account-overview.md)」を参照してください。

# <a name="portal"></a>[ポータル](#tab/portal)

Azure portal を使用してストレージ アカウントの TLS の最小バージョンを構成するには、これらの手順に従います。

1. Azure Portal のストレージ アカウントに移動します。
1. **[構成]** 設定を選択します。
1. 次の図に示すように、 **[TLS の最小バージョン]** で、ドロップダウンを使用して、このストレージ アカウントのデータにアクセスするために必要な TLS の最小バージョンを選択します。

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Azure portal で TLS の最小バージョンを構成する方法を示すスクリーンショット":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してストレージ アカウントの TLS の最小バージョンを構成するには、まず、[az resource show](/cli/azure/resource#az-resource-show) コマンドを呼び出して、ストレージ アカウントのリソース ID を取得します。 次に、[az resource update](/cli/azure/resource#az-resource-update) コマンドを呼び出して、ストレージ アカウントの **minimumTlsVersion** プロパティを設定します。 **minimumTlsVersion** の有効な値は、`TLS1_0`、`TLS1_1` および `TLS1_2` です。

次の例では、TLS の最小バージョンを 1.2 に設定します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli-interactive
storage_account_id=$(az resource show \
  --name <storage-account> \
  --resource-group <resource-group> \
  --resource-type Microsoft.Storage/storageAccounts \
  --query id \
  --output tsv)

az resource update \
  --ids $storage_account_id \
  --set properties.minimumTlsVersion="TLS1_2"
```

---

> [!NOTE]
> ストレージ アカウントの TLS の最小バージョンを更新した後、変更が完全に反映されるまでに最大で 30 秒かかることがあります。

## <a name="check-the-minimum-required-tls-version-for-an-account"></a>アカウントに必要な TLS の最小バージョンを確認する

ストレージ アカウントに対して構成されている必要な TLS の最小バージョンを特定するには、Azure Resource Manager の **minimumTlsVersion** プロパティを確認します。 一度に数多くのストレージ アカウントのこのプロパティを確認するには、Azure Resource Graph エクスプローラーを使用します。

**minimumTlsVersion** プロパティは既定では設定されず、明示的に設定するまで値は返されません。 ストレージ アカウントでは、プロパティ値が null の場合に TLS バージョン 1.0 以降で送信される要求を許可するように既定で設定されます。

### <a name="check-the-minimum-required-tls-version-for-a-single-storage-account"></a>1 つのストレージ アカウントに必要な TLS の最小バージョンを確認する

Azure CLI を使用して 1 つのストレージ アカウントに必要な TLS の最小バージョンを確認するには、[az resource show](/cli/azure/resource#az-resource-show) コマンドを呼び出して、**minimumTlsVersion** プロパティのクエリを実行します。

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.minimumTlsVersion \
    --output tsv
```

### <a name="check-the-minimum-required-tls-version-for-a-set-of-storage-accounts"></a>一連のストレージ アカウントに必要な TLS の最小バージョンを確認する

最適なパフォーマンスの一連のストレージ アカウント全体で必要な TLS の最小バージョンを確認するために、Azure portal の Azure Resource Graph エクスプローラーを使用できます。 Resource Graph エクスプローラーの使用の詳細については、「[クイック スタート:Azure Resource Graph エクスプローラーを使用して初めての Resource Graph クエリを実行する](/azure/governance/resource-graph/first-query-portal)」を参照してください。

Resource Graph エクスプローラーで次のクエリを実行すると、ストレージ アカウントの一覧が返され、各アカウントの TLS の最小バージョンが表示されます。

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

---

## <a name="test-the-minimum-tls-version-from-a-client"></a>クライアントから TLS の最小バージョンをテストする

ストレージ アカウントに必要な TLS の最小バージョンで、以前のバージョンで行われた呼び出しが禁止されることをテストするために、以前のバージョンの TLS を使用するようにクライアントを構成できます。 特定のバージョンの TLS を使用するようにクライアントを構成する方法の詳細については、「[クライアント アプリケーションのトランスポート層セキュリティ (TLS) を構成する](transport-layer-security-configure-client-version.md)」を参照してください。

アカウントに構成されている TLS の最小バージョンを満たしていない TLS バージョンを使用して、クライアントによってストレージ アカウントにアクセスされた場合、Azure Storage からエラー コード 400 エラー (無効な要求) と、使用された TLS バージョンにはこのストレージ アカウントに対する要求を行うことが許可されていないことを示すメッセージが返されます。

## <a name="next-steps"></a>次のステップ

- [クライアント アプリケーションのトランスポート層セキュリティ (TLS) を構成する](transport-layer-security-configure-client-version.md)
- [BLOB ストレージのセキュリティに関する推奨事項](../blobs/security-recommendations.md)
