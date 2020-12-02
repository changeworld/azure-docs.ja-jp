---
title: Azure Monitor Log Analytics でのカスタマー マネージド ストレージ アカウントの使用
description: 独自のストレージ アカウントを Log Analytics のシナリオに使用します
ms.subservice: logs
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: f424a2c3102f7b270a64c612a91d645ab71461fc
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184095"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Azure Monitor Log Analytics でのカスタマー マネージド ストレージ アカウントの使用

Log Analytics は、さまざまなシナリオで Azure Storage に依存しています。 この使用は、通常、自動的に管理されます。 ただし、場合によっては、独自のストレージ アカウント (カスタマー マネージド ストレージ アカウントとも呼ばれます) を提供して管理することが必要になります。 このドキュメントでは、WAD/LAD ログのインジェスト、Private Link 固有のシナリオ、およびカスタマー マネージド キー (CMK) 暗号化に対するカスタマー マネージド ストレージの使用について詳しく説明します。 

> [!NOTE]
> 書式と内容が変更される可能性があるため、Log Analytics によってカスタマー マネージド ストレージにアップロードされたコンテンツには依存しないことをお勧めします。

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Azure Diagnostics 拡張機能ログ (WAD/LAD) の取り込み
Azure Diagnostics 拡張機能エージェント (Windows エージェントの場合は WAD、Linux エージェントの場合は LAD とも呼ばれます) により、さまざまなオペレーティング システム ログが収集されて、カスタマー マネージド ストレージ アカウントに格納されます。 その後、これらのログを Log Analytics に取り込み、それらを確認したり分析したりすることができます。
ストレージ アカウントから Azure Diagnostics 拡張機能ログを収集するには、[Azure portal](./diagnostics-extension-logs.md#collect-logs-from-azure-storage) を使用するか、[Storage Insights API](/rest/api/loganalytics/connectedsources/storage%20insights/createorupdate) を呼び出して、ストレージ アカウントをストレージ データ ソースとして Log Analytics ワークスペースに接続します。

サポートされるデータ型:
* syslog
* Windows イベント
* Service Fabric
* ETW イベント
* IIS ログ

## <a name="using-private-links"></a>プライベート リンクの使用
Azure Monitor リソースへの接続にプライベート リンクを使用する場合、一部のユース ケースでは、カスタマー マネージド ストレージ アカウントが必要になります。 そのようなケースの 1 つは、カスタム ログまたは IIS ログのインジェストです。 これらのデータの種類は、最初に BLOB として中間 Azure ストレージ アカウントにアップロードされ、その後でのみワークスペースに取り込まれます。 同様に、一部の Azure Monitor ソリューションでは、ストレージ アカウントを使用して、Azure Security Center ソリューションで使用される Watson ダンプ ファイルなどの大きなファイルが格納される場合があります。 

##### <a name="private-link-scenarios-that-require-a-customer-managed-storage"></a>カスタマー マネージド ストレージを必要とするプライベート リンクのシナリオ
* カスタム ログと IIS ログのインジェスト
* ASC ソリューションで Watson ダンプ ファイルを収集できるようにする

### <a name="how-to-use-a-customer-managed-storage-account-over-a-private-link"></a>カスタマー マネージド ストレージ アカウントをプライベート リンクで使用する方法
##### <a name="workspace-requirements"></a>ワークスペースの要件
プライベート リンクを介して Azure Monitor に接続すると、Log Analytics エージェントでは、プライベート リンク経由でネットワークにリンクされたワークスペースにのみログを送信できます。 この規則を使用するには、Azure Monitor プライベート リンク スコープ (AMPLS) オブジェクトを適切に構成し、それをワークスペースに接続してから、AMPLS をプライベート リンク経由でネットワークに接続する必要があります。 AMPLS の構成手順の詳細については、「[Azure Private Link を使用して、ネットワークを Azure Monitor に安全に接続する](./private-link-security.md)」を参照してください。 
##### <a name="storage-account-requirements"></a>ストレージ アカウントの要件
プライベート リンクに正常に接続するには、ストレージ アカウントが次のようになっている必要があります。
* VNet 上またはピアリングされたネットワーク上に存在し、プライベート リンク経由で VNet に接続されていること。 これにより、VNet 上のエージェントはストレージ アカウントにログを送信できるようになります。
* リンク先のワークスペースと同じリージョンに存在していること。
* Azure Monitor によるストレージ アカウントへのアクセスが許可されていること。 ストレージ アカウントへのアクセスを選択したネットワークだけに許可する場合は、この例外 "信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します" も許可する必要があります。 これにより、このストレージ アカウントに取り込まれたログを Log Analytics で読み取ることができます。
* ワークスペースで他のネットワークからのトラフィックも処理する場合は、関連するネットワークまたはインターネットからの受信トラフィックを許可するように、ストレージ アカウントを構成する必要があります。

##### <a name="link-your-storage-account-to-a-log-analytics-workspace"></a>ストレージ アカウントを Log Analytics ワークスペースにリンクする
ストレージ アカウントをワークスペースにリンクするには、[Azure CLI](/cli/azure/monitor/log-analytics/workspace/linked-storage) または [REST API](/rest/api/loganalytics/linkedstorageaccounts) を使用します。 適用可能な dataSourceType の値:
* CustomLogs – インジェストの間にカスタム ログと IIS ログにストレージを使用します。
* AzureWatson – ASC (Azure Security Center) ソリューションによってアップロードされた Watson ダンプ ファイルにストレージを使用します。 データ保持の管理、リンクされたストレージ アカウントの置換、ストレージ アカウントのアクティビティの監視の詳細については、「[リンクされたストレージ アカウントの管理](#managing-linked-storage-accounts)」を参照してください。 

## <a name="encrypting-data-with-cmk"></a>CMK でのデータの暗号化
ストレージ アカウント内の保存データはすべて、Azure Storage によって暗号化されます。 既定では、Microsoft マネージド キー (MMK) がデータの暗号化に使用されます。 ただし、Azure Storage では代わりに、Azure Key Vault のカスタマー マネージド キー (CMK) を使用して、ストレージ データを暗号化することができます。 独自のキーを Azure Key Vault にインポートするか、または Azure Key Vault API を使用してキーを生成することができます。
##### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>カスタマー マネージド ストレージ アカウントを必要とする CMK のシナリオ
* CMK によるログ アラート クエリの暗号化
* CMK による保存されたクエリの暗号化

### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>カスタマー マネージド ストレージ アカウントに CMK を適用する方法
##### <a name="storage-account-requirements"></a>ストレージ アカウントの要件
ストレージ アカウントとキー コンテナーは同じリージョンに存在していることが必要です。ただし、サブスクリプションは異なっていてもかまいません。 Azure Storage ストレージの暗号化およびキー管理の詳細については、「[保存データに対する Azure Storage 暗号化](../../storage/common/storage-service-encryption.md)」を参照してください。

##### <a name="apply-cmk-to-your-storage-accounts"></a>ストレージ アカウントに CMK を適用する
Azure Key Vault でカスタマー マネージド キーを使用するように Azure ストレージ アカウントを構成するには、[Azure portal](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)、[PowerShell](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)、または [CLI](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) を使用します。 

## <a name="managing-linked-storage-accounts"></a>リンクされたストレージ アカウントの管理

ストレージ アカウントをワークスペースにリンクしたり、リンクを解除したりするには、[Azure CLI](/cli/azure/monitor/log-analytics/workspace/linked-storage) または [REST API](/rest/api/loganalytics/linkedstorageaccounts) を使用します。

##### <a name="create-or-modify-a-link"></a>リンクを作成または変更する
ストレージ アカウントをワークスペースにリンクすると、Log Analytics ではサービスによって所有されているストレージ アカウントの代わりにそのアカウントが使用されるようになります。 そのための方法は次のとおりです。 
* 複数のストレージ アカウントを登録して、ログの負荷を分散する
* 複数のワークスペースに同じストレージ アカウントを再利用する

##### <a name="unlink-a-storage-account"></a>ストレージ アカウントのリンクを解除する
ストレージ アカウントの使用を停止するには、ワークスペースからストレージのリンクを解除します。 ワークスペースからすべてのストレージ アカウントのリンクを解除すると、Log Analytics ではサービスで管理されたストレージ アカウントの利用が試みられます。 ネットワークでインターネットへのアクセスが制限されている場合、これらのストレージが使用できない可能性があり、ストレージに依存するシナリオはすべて失敗します。

##### <a name="replace-a-storage-account"></a>ストレージ アカウントを置換する
インジェストに使用されるストレージ アカウントを置き換えるには
1.  **新しいストレージ アカウントへのリンクを作成します。** ログ エージェントによって更新された構成が取得され、さらに、新しいストレージへのデータの送信を開始します。 このプロセスには数分かかることがあります。
2.  **次に、古いストレージ アカウントのリンクを解除して、エージェントから削除されたアカウントへの書き込みを停止します。** データがこのアカウントからすべて取り込まれるまで、取り込みプロセスでデータを読み取り続けます。 すべてのログが取り込まれるまでは、ストレージ アカウントを削除しないでください。

### <a name="maintaining-storage-accounts"></a>ストレージアカウントのメンテナンス
##### <a name="manage-log-retention"></a>ログの保持期間を管理する
独自のストレージ アカウントを使用する場合、保持期間はユーザーが設定します。 つまり、プライベート ストレージに格納されているログは、Log Analytics では削除されません。 代わりに、ユーザー設定に従って負荷を処理するポリシーを設定する必要があります。

##### <a name="consider-load"></a>負荷を考慮する
ストレージ アカウントは、要求の調整を開始する前に、読み取り要求と書き込み要求の特定の負荷を処理できます (詳細については、[BLOB ストレージのスケーラビリティとパフォーマンスのターゲット](../../storage/common/scalability-targets-standard-account.md)に関するページを参照してください)。 調整が行われると、ログの取り込みに要する時間に影響します。 ストレージ アカウントが過負荷になっている場合は、追加のストレージ アカウントを登録し、負荷をアカウント間で分散します。 ストレージ アカウントの容量とパフォーマンスを監視するには、[Azure portal でその分析情報]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview)を確認します。

### <a name="related-charges"></a>関連料金
ストレージ アカウントは、格納されているデータの量、ストレージの種類、冗長性の種類に応じて課金されます。 詳細については、「[ブロック BLOB の価格](https://azure.microsoft.com/pricing/details/storage/blobs)」と「[Table Storage の料金](https://azure.microsoft.com/pricing/details/storage/tables)」に関するページを参照してください。


## <a name="next-steps"></a>次の手順

- [Azure Private Link を使用して、ネットワークを Azure Monitor に安全に接続する](private-link-security.md)方法を確認する
- [Azure Monitor のカスタマー マネージド キー](customer-managed-keys.md)について確認する
