---
title: 変更および削除された BLOB
titleSuffix: Azure Cognitive Search
description: Azure Blob Storage からインポートする最初の検索インデックスの構築後、後続のインデックス作成では、変更または削除された BLOB のみを取得できます。 この記事では詳細について説明します。
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 2e73039418233c97fc20242ed7af7df14c5b47ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91534779"
---
# <a name="how-to-set-up-change-and-deletion-detection-for-blobs-in-azure-cognitive-search-indexing"></a>Azure Cognitive Search のインデックス作成で BLOB の変更と削除の検出を設定する方法

最初の検索インデックスが作成された後、最初の実行以降に作成または削除されたドキュメントのみを取得するように、後続のインデクサー ジョブを構成することをお勧めします。 Azure Blob Storage のものである検索コンテンツの場合、スケジュールを使用してインデックス作成をトリガーすると、変更の検出が自動的に行われます。 既定では、サービスによって、BLOB の `LastModified` タイムスタンプの指定に従い、変更された BLOB のみのインデックスが再作成されます。 検索インデクサーでサポートされている他のデータ ソースとは対照的に、BLOB には常にタイムスタンプがあるため、変更検出ポリシーを手動で設定する必要がなくなります。

変更の検出は指定されていますが、削除検出は指定されていません。 削除されたドキュメントを検出する場合は、必ず "論理的な削除" アプローチを使用してください。 BLOB を完全に削除すると、対応するドキュメントが Search インデックスから削除されません。

論理的な削除方法を実装するには、2 つの方法があります。 以下では両方を説明します。

## <a name="native-blob-soft-delete-preview"></a>ネイティブ BLOB の論理的な削除 (プレビュー)

> [!IMPORTANT]
> ネイティブ BLOB の論理的な削除のサポートはプレビュー段階です。 プレビュー段階の機能はサービス レベル アグリーメントなしで提供しています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 [REST API バージョン 2020-06-30-Preview](./search-api-preview.md) で、この機能を提供しています。 現時点では、ポータルと .NET SDK によるサポートはありません。

> [!NOTE]
> ネイティブ BLOB の論理的な削除ポリシーを使用する場合、インデックス内のドキュメントのドキュメント キーは BLOB プロパティまたは BLOB メタデータである必要があります。

この方法では、Azure Blob Storage によって提供される[ネイティブ BLOB の論理的な削除](../storage/blobs/soft-delete-blob-overview.md)機能を使用します。 ストレージ アカウントでネイティブ BLOB の論理的な削除が有効になっていて、データソースにネイティブの論理的な削除ポリシーが設定されており、論理的に削除された状態に遷移した BLOB がインデクサーによって検出された場合、そのドキュメントはインデクサーによってインデックスから削除されます。 Azure Data Lake Storage Gen2 から BLOB のインデックスを作成する場合、ネイティブ BLOB の論理的な削除ポリシーはサポートされていません。

次の手順に従います。

1. [Azure Blob Storage に対してネイティブの論理的な削除](../storage/blobs/soft-delete-blob-overview.md)を有効にします。 保持ポリシーは、インデクサー間隔スケジュールよりも大幅に高い値に設定することをお勧めします。 このようにすると、インデクサーの実行で問題が発生した場合、またはインデックスを作成するドキュメントの数が多い場合に、インデクサーが論理的に削除された BLOB を最終的に処理するのに十分な時間があります。 Azure Cognitive Search インデクサーでは、論理的に削除された状態の BLOB を処理する場合にのみ、インデックスからドキュメントが削除されます。

1. データ ソースでネイティブ BLOB の論理的な削除の検出ポリシーを構成します。 次に例を示します。 この機能はプレビュー段階であるため、プレビュー REST API を使用する必要があります。

1. インデクサーを実行するか、またはスケジュールに基づいて実行するようにインデクサーを設定します。 インデクサーが実行されて BLOB が処理されると、ドキュメントはインデックスから削除されます。

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

### <a name="reindexing-un-deleted-blobs-using-native-soft-delete-policies"></a>削除されていない BLOB のインデックスの再作成 (ネイティブの論理的な削除ポリシーを使用)

ストレージ アカウントでネイティブの論理的な削除を有効にして Azure Blob Storage から BLOB を削除すると、BLOB は論理的に削除された状態に移行し、保持期間内にその BLOB の削除を取り消すことができます。 削除がインデクサーによって処理された後で元に戻した場合、復元された BLOB のインデックスがインデクサーによって常に作成されるとは限りません。 これは、インデクサーでは BLOB の `LastModified` タイムスタンプに基づいて、インデックスを作成する BLOB が決定されるためです。 論理的に削除された BLOB の削除が取り消されたとき、その `LastModified` タイムスタンプは更新されないため、インデクサーによって最新の `LastModified` タイムスタンプを持つ BLOB が既に処理されている場合、削除が取り消された BLOB のインデックスは再作成されません。 

削除が取り消された BLOB のインデックス再作成が確実に行われるようにするには、BLOB の `LastModified` タイムスタンプを更新する必要があります。 これを行う 1 つの方法は、その BLOB のメタデータを保存することです。 メタデータを変更する必要はありませんが、メタデータを保存すると BLOB の `LastModified` タイムスタンプが更新され、インデクサーでこの BLOB のインデックス再作成が必要であることが認識されます。

## <a name="soft-delete-using-custom-metadata"></a>カスタム メタデータを使用した論理的な削除

この方法では、BLOB のメタデータを使用して、ドキュメントを検索インデックスから削除するタイミングを指定します。 この方法には、2 つの個別のアクションが必要です。インデックスから検索ドキュメントを削除した後、Azure Storage で BLOB を削除します。

次の手順に従います。

1. カスタム メタデータのキーと値のペアを BLOB に追加して、これが論理的に削除されていることを Azure Cognitive Search に示します。

1. データ ソースで論理的な削除の列の検出ポリシーを構成します。 次に例を示します。

1. インデクサーによって BLOB が処理され、インデックスからドキュメントが削除されると、Azure Blob Storage の BLOB を削除できます。

たとえば、次のポリシーでは、BLOB のメタデータ プロパティ `IsDeleted` の値が `true` のときに、その BLOB が削除されるものと見なされます。

```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
```

### <a name="reindexing-un-deleted-blobs-using-custom-metadata"></a>削除が取り消された BLOB のインデックスの再作成 (カスタム メタデータを使用)

削除された BLOB がインデクサーによって処理され、対応する検索ドキュメントがインデックスから削除された後、BLOB の `LastModified` タイムスタンプが最後に実行されたインデクサーよりも古い場合、後でそれを復元しても、その BLOB に再度アクセスすることはありません。

そのドキュメントのインデックスを再作成する場合は、その BLOB の `"softDeleteMarkerValue" : "false"` を変更して、インデクサーを再実行します。

## <a name="help-us-make-azure-cognitive-search-better"></a>Azure Cognitive Search の品質向上にご協力ください

ご希望の機能や品質向上のアイデアがありましたら、[UserVoice](https://feedback.azure.com/forums/263029-azure-search/) までお寄せください。 既存の機能の使用方法でサポートが必要な場合、[Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870) に質問を投稿してください。

## <a name="next-steps"></a>次のステップ

* [Azure Cognitive Search のインデクサー](search-indexer-overview.md)
* [BLOB インデクサーを構成する方法](search-howto-indexing-azure-blob-storage.md)
* [BLOB のインデックス作成の概要](search-blob-storage-integration.md)