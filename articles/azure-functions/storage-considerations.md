---
title: Azure Functions のストレージに関する考慮事項
description: Azure Functions のストレージ要件と、格納済みデータの暗号化について説明します。
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 0e53d2919d8af3f0e8162d4aca9f55f2ec0ab740
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335881"
---
# <a name="storage-considerations-for-azure-functions"></a>Azure Functions のストレージに関する考慮事項

Azure Functions では、Function App インスタンスを作成するときに Azure ストレージ アカウントが必要になります。 次のストレージ サービスは、お使いの Function App によって利用できます。

|ストレージ サービス  | 機能の使用法  |
|---------|---------|
| [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)     | バインドの状態と関数キーを管理します。  <br/>[Durable Functions 上のタスク ハブ](durable/durable-functions-task-hubs.md)からも使用されます。 |
| [Azure Files](../storage/files/storage-files-introduction.md)  | [従量課金プラン](consumption-plan.md)や [Premium プラン](functions-premium-plan.md)で、関数アプリ コードを格納して実行するために使用されるファイル共有。 <br/>Azure Files は既定で設定されていますが、特定の条件では、[Azure Files を使わずにアプリを作成](#create-an-app-without-azure-files)することもできます。 |
| [Azure Queue Storage](../storage/queues/storage-queues-introduction.md)     | [Durable Functions 上のタスク ハブ](durable/durable-functions-task-hubs.md)から使用されます。   |
| [Azure Table Storage](../storage/tables/table-storage-overview.md)  |  [Durable Functions 上のタスク ハブ](durable/durable-functions-task-hubs.md)から使用されます。       |

> [!IMPORTANT]
> 従量課金/Premium ホスティング プランを使用する場合、関数コード ファイルおよびバインディング構成ファイルは、メイン ストレージ アカウントの Azure Files に保存されます。 メイン ストレージ アカウントを削除すると、このコンテンツは削除され、復元できません。

## <a name="storage-account-requirements"></a>ストレージ アカウントの要件

Function App を作成するときは、BLOB、キュー、テーブル ストレージをサポートする汎用の Azure Storage アカウントを作成またはリンクする必要があります。 これは、Functions ではトリガーの管理や関数実行のログ記録などの操作に Azure Storage を使用しているためです。 一部のストレージ アカウントでは、キューとテーブルがサポートされません。 これらのアカウントには、BLOB 専用ストレージ アカウントと Azure Premium Storage が含まれています。

ストレージ アカウントの種類の詳細については、「[Azure Storage サービスの概要](../storage/common/storage-introduction.md#core-storage-services)」を参照してください。 

お使いの Function App で既存のストレージ アカウントを使用することは可能ですが、必ずこれらの要件を満たしている必要があります。 Azure portal で、Function App の作成フローの一部として作成されたストレージ アカウントでは、これらのストレージ アカウント要件を満たしていることが保証されます。 ポータルでは、関数アプリの作成中に既存のストレージ アカウントを選択すると、サポートされていないアカウントが除外されます。 このフローでは、作成している関数アプリと同じリージョンにある既存のストレージ アカウントのみを選択できます。 詳細については、「[ストレージ アカウントの場所](#storage-account-location)」を参照してください。

<!-- JH: Does using a Premium Storage account improve perf? -->

## <a name="storage-account-guidance"></a>ストレージ アカウントに関するガイダンス

すべての関数アプリには、操作するためのストレージ アカウントが必要です。 そのアカウントが削除されると、関数アプリは実行されません。 ストレージ関連の問題をトラブルシューティングするには、[ストレージ関連の問題をトラブルシューティングする方法](functions-recover-storage-account.md)に関する記事を参照してください。 関数アプリによって使用されるストレージ アカウントには、次の追加の考慮事項が適用されます。

### <a name="storage-account-location"></a>ストレージ アカウントの場所

最適なパフォーマンスを得るには、関数アプリで同じリージョンのストレージ アカウントを使用する必要があります。これにより、待ち時間が短縮されます。 Azure portal によって、このベスト プラクティスが適用されます。 何らかの理由で、関数アプリとは異なるリージョンでストレージ アカウントを使用する必要がある場合は、ポータルの外部で関数アプリを作成する必要があります。 

### <a name="storage-account-connection-setting"></a>ストレージ アカウント接続の設定

ストレージ アカウント接続は、[AzureWebJobsStorage アプリケーション設定](./functions-app-settings.md#azurewebjobsstorage)の中で管理されます。 

ストレージ キーを再生成する場合は、ストレージ アカウント接続文字列を更新する必要があります。 ストレージ キーの管理については、[こちら](../storage/common/storage-account-create.md)をご覧ください。

### <a name="shared-storage-accounts"></a>共有のストレージ アカウント

複数の Function App では、同じストレージ アカウントを問題なく共有できます。 たとえば、Visual Studio では、Azure ストレージ エミュレーターを使用して複数のアプリを開発できます。 この場合、エミュレーターは単一のストレージ アカウントのように動作します。 お使いの Function App で使用されているものと同じストレージ アカウントは、アプリケーション データを格納するためにも使用できます。 ただし、運用環境では、この手法が常に適切であるとは限りません。

### <a name="lifecycle-management-policy-considerations"></a>ライフサイクル管理ポリシーに関する考慮事項

Functions は、Blob ストレージを使用して、[関数アクセス キー](functions-bindings-http-webhook-trigger.md#authorization-keys)などの重要な情報を保持します。 Blob Storage アカウントに[ライフサイクル管理ポリシー](../storage/blobs/lifecycle-management-overview.md)を適用すると、ポリシーが Functions ホストが必要とする BLOB を削除する場合があります。 この理由から、Functions が使用するストレージ アカウントにこのようなポリシーを適用しないようにする必要があります。 このようなポリシーを適用する必要がある場合は、通常は `azure-webjobs` または `scm` のプレフィックスが付いている Functions が使用するコンテナーは除外することを忘れないでください。

### <a name="optimize-storage-performance"></a>ストレージ パフォーマンスの最適化

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>ストレージ データの暗号化

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

### <a name="in-region-data-residency"></a>リージョンのデータ所在地

すべての顧客データを 1 つのリージョン内に留める必要がある場合、関数アプリに関連付けられているストレージ アカウントは、[リージョン内冗長性](../storage/common/storage-redundancy.md)が与えられたアカウントにする必要があります。 リージョン内で冗長性を持つストレージ アカウントは、[Azure Durable Functions](./durable/durable-functions-perf-and-scale.md#storage-account-selection) でも使用される必要があります。

プラットフォームで管理されるその他の顧客データは、内部負荷分散型の App Service Environment (ASE) でホストしているとき、そのリージョン内にのみ格納されます。 詳細については、[ASE のゾーン冗長性](../app-service/environment/zone-redundancy.md#in-region-data-residency)に関するページを参照してください。

## <a name="create-an-app-without-azure-files"></a>Azure Files を使わずにアプリを作成する

Premium と Linux 以外の Consumption プランでは、高スケールの共有ファイル システムとして、Azure Files が既定で設定されています。 このファイル システムは、それらのプラットフォームでログ ストリーミングなどの機能を実現するためにも使用しますが、主な用途は、デプロイした機能のペイロードを安定的に送信することです。 [外部パッケージの URL を使用してアプリをデプロイするときは](./run-functions-from-deployment-package.md)、そのアプリのコンテンツを、読み取り専用の別のファイルシステムから提供します。その場合、必要なければ Azure Files を使用しなくてもかまいません。 この場合、書き込み可能なファイルシステムを用意することになりますが、すべての関数アプリのインスタンスでこれを共有することは保証できません。

Azure Files を使用しない場合は、次のことへの対応が必要です。

* 外部パッケージの URL からデプロイする必要があります。
* 書き込み可能な共有ファイル システムの使用を前提にした運用はできません。
* アプリで Functions ランタイム v1 を使用できません。
* Azure portal などのクライアントのログ ストリーミングで、既定のログがファイル システムのログになります。 これの代わりに、Application Insights のログを使用するべきです。

上の点に問題がなければ、Azure Files を使用せずにアプリを作成できます。 アプリケーションの設定 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` と `WEBSITE_CONTENTSHARE` を指定せずに、関数アプリを作成してください。 これを行うには、標準のデプロイ用の ARM テンプレートを生成し、これらの 2 つの設定を削除し、テンプレートをデプロイします。 

Functions では動的スケールアウトのプロセスの一部に Azure Files を使用するため、Consumption、Premium プランにおいて Azure Files なしで実行すると、スケーリングが制限される可能性があります。

## <a name="mount-file-shares"></a>ファイル共有をマウントする

"_この機能は現在、Linux で実行されている場合にのみ使用できます。_ " 

既存の Azure Files 共有を Linux 関数アプリにマウントすることができます。 Linux 関数アプリに共有をマウントすることにより、既存の機械学習モデルや、関数内のその他のデータを活用できます。 既存の共有を Linux 関数アプリにマウントするには、[`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) コマンドを使用できます。 

このコマンドで、`share-name` は既存の Azure Files 共有の名前で、`custom-id` は、Function App にマウントされたときに共有を一意に定義する任意の文字列にすることができます。 また、`mount-path` は、Function App で共有にアクセスするためのパスです。 `mount-path` は、`/dir-name` の形式にする必要があり、`/home` で開始することはできません。

完全な例については、[Python Function App の作成と Azure Files 共有のマウント](scripts/functions-cli-mount-files-storage-linux.md)に関する記事にあるスクリプトを参照してください。 

現時点では、`AzureFiles` の `storage-type` のみがサポートされています。 指定された Function App にマウントできるのは 5 つの共有のみです。 ファイル共有をマウントすると、コールド スタートの時間が少なくとも 200 ミリ秒から 300 ミリ秒長くなる可能性があり、ストレージ アカウントが別のリージョンにある場合はさらに長くなる可能性があります。

マウントされた共有は、指定された `mount-path` の関数コードで使用できます。 たとえば、`mount-path` が `/path/to/mount` の場合、次の Python の例に示すように、ファイル システム API でターゲット ディレクトリにアクセスできます。

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>次のステップ

Azure Functions ホスティングのオプションについて確認してください。

> [!div class="nextstepaction"]
> [Azure Functions のスケールとホスティング](functions-scale.md)
