---
title: Azure Functions のストレージに関する考慮事項
description: Azure Functions のストレージ要件と、格納済みデータの暗号化について説明します。
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: f094996ca44ec36d46330e54eac56b28794ef22e
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190303"
---
# <a name="storage-considerations-for-azure-functions"></a>Azure Functions のストレージに関する考慮事項

Azure Functions では、関数アプリ インスタンスを作成するときに Azure ストレージ アカウントが必要になります。 次のストレージ サービスは、お使いの関数アプリによって利用できます。


|ストレージ サービス  | 機能の使用法  |
|---------|---------|
| [Azure BLOB Storage](../storage/blobs/storage-blobs-introduction.md)     | バインドの状態と関数キーを管理します。  <br/>[Durable Functions 上のタスク ハブ](durable/durable-functions-task-hubs.md)からも使用されます。 |
| [Azure Files](../storage/files/storage-files-introduction.md)  | 関数アプリ コードを[従量課金プラン](functions-scale.md#consumption-plan)に格納して実行するために使用されるファイル共有。 |
| [Azure Queue Storage](../storage/queues/storage-queues-introduction.md)     | [Durable Functions 上のタスク ハブ](durable/durable-functions-task-hubs.md)から使用されます。   |
| [Azure Table Storage](../storage/tables/table-storage-overview.md)  |  [Durable Functions 上のタスク ハブ](durable/durable-functions-task-hubs.md)から使用されます。       |

> [!IMPORTANT]
> 従量課金ホスティング プランを使用する場合、関数コード ファイルおよびバインディング構成ファイルは、メイン ストレージ アカウントの Azure File Storage に保存されます。 メイン ストレージ アカウントを削除すると、このコンテンツは削除され、復元できません。

## <a name="storage-account-requirements"></a>ストレージ アカウントの要件

関数アプリを作成するときは、BLOB、キュー、テーブル ストレージをサポートする汎用の Azure Storage アカウントを作成またはリンクする必要があります。 これは、Functions ではトリガーの管理や関数実行のログ記録などの操作に Azure Storage を使用しているためです。 一部のストレージ アカウントでは、キューとテーブルがサポートされません。 これらのアカウントには、BLOB 専用のストレージ アカウント、Azure Premium Storage、ZRS レプリケーションを使用する汎用ストレージ アカウントが含まれます。 これらのサポートされていないアカウントは、関数アプリの作成時に [ストレージ アカウント] ブレードから除外されます。

ストレージ アカウントの種類の詳細については、「[Azure Storage サービスの概要](../storage/common/storage-introduction.md#azure-storage-services)」を参照してください。 

お使いの関数アプリで既存のストレージ アカウントを使用することは可能ですが、必ずこれらの要件を満たしている必要があります。 関数アプリの作成フローの一部として作成されたストレージ アカウントでは、これらのストレージ アカウント要件を満たしていることが保証されます。  

## <a name="storage-account-guidance"></a>ストレージ アカウントに関するガイダンス

すべての関数アプリには、操作するためのストレージ アカウントが必要です。 そのアカウントが削除されると、関数アプリは実行されません。 ストレージ関連の問題をトラブルシューティングするには、[ストレージ関連の問題をトラブルシューティングする方法](functions-recover-storage-account.md)に関する記事を参照してください。 関数アプリによって使用されるストレージ アカウントには、次の追加の考慮事項が適用されます。

### <a name="storage-account-connection-setting"></a>ストレージ アカウント接続の設定

ストレージ アカウント接続は、[AzureWebJobsStorage アプリケーション設定](./functions-app-settings.md#azurewebjobsstorage)の中で管理されます。 

ストレージ キーを再生成する場合は、ストレージ アカウント接続文字列を更新する必要があります。 ストレージ キーの管理については、[こちら](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)をご覧ください。

### <a name="shared-storage-accounts"></a>共有のストレージ アカウント

複数の関数アプリでは、同じストレージ アカウントを問題なく共有できます。 たとえば、Visual Studio では、Azure ストレージ エミュレーターを使用して複数のアプリを開発できます。 この場合、エミュレーターは単一のストレージ アカウントのように動作します。 お使いの関数アプリで使用されているものと同じストレージ アカウントは、アプリケーション データを格納するためにも使用できます。 ただし、運用環境では、この手法が常に適切であるとは限りません。

### <a name="optimize-storage-performance"></a>ストレージ パフォーマンスの最適化

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>ストレージ データの暗号化

Azure Storage は、保存されているストレージ アカウント内のすべてのデータを暗号化します。 詳細については、「[保存データ向け Azure ストレージの暗号化](../storage/common/storage-service-encryption.md)」をご覧ください。

規定では、データは Microsoft のマネージド キーで暗号化されます。 暗号化キーをさらに制御するために、BLOB とファイル データの暗号化に使用する目的で、顧客が管理するキーを提供できます。 Functions からストレージ アカウントにアクセスできるように、これらのキーは Azure Key Vault 内に置かれている必要があります。 詳細については、「[Azure portal を使用して Azure Key Vault での顧客管理キーを構成する](../storage/common/storage-encryption-keys-portal.md)」を参照してください。  

## <a name="next-steps"></a>次のステップ

Azure Functions ホスティングのオプションについて確認してください。

> [!div class="nextstepaction"]
> [Azure Functions のスケールとホスティング](functions-scale.md)


