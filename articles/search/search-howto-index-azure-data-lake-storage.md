---
title: Azure Data Lake Storage Gen2 (プレビュー) の検索
titleSuffix: Azure Cognitive Search
description: Azure Data Lake Storage Gen2 でコンテンツとメタデータのインデックスを作成する方法について説明します。 現在、この機能はパブリック プレビュー段階にあります。
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4b725c8a1bf0649a640c02a9a1828ec9014d36d6
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905661"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 のドキュメントのインデックス作成

> [!IMPORTANT] 
> Azure Data Lake Storage Gen2 のサポートは、現在パブリック プレビュー段階です。 プレビュー段階の機能はサービス レベル アグリーメントなしで提供しています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 プレビュー機能に対するアクセスの要求は、[こちらのフォーム](https://aka.ms/azure-cognitive-search/indexer-preview)に必要事項を入力して行うことができます。 [REST API バージョン 2019-05-06-Preview](search-api-preview.md) でこの機能を提供します。 現時点では、ポータルと .NET SDK によるサポートはありません。


Azure ストレージ アカウントを設定するときに、[階層型名前空間](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)を有効にするオプションがあります。 これにより、アカウント内のコンテンツのコレクションを、ディレクトリと入れ子になったサブディレクトリの階層にまとめることができます。 階層型名前空間を有効にすることによって、[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) を有効にすることができます。

この記事では、Azure Data Lake Storage Gen2 に含まれるドキュメントのインデックス作成を開始する方法について説明します。

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Azure Data Lake Storage Gen2 インデクサーの設定

Data Lake Storage Gen2 のコンテンツにインデックスを作成するには、いくつかの手順を実行する必要があります。

### <a name="step-1-sign-up-for-the-preview"></a>手順 1:プレビューのサインアップ

[このフォーム](https://aka.ms/azure-cognitive-search/indexer-preview)を入力して、Data Lake Storage Gen2 インデクサーのプレビューにサインアップしてください。 プレビューへの参加が承認されると、確認メールが届きます。

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>手順 2:Azure Blob ストレージのインデックス作成セットアップ手順の実行

プレビューのサインアップが正常に完了したことを示すメッセージが表示されたら、インデックス作成パイプラインを作成する準備ができています。

[REST API バージョン 2019-05-06-Preview](search-api-preview.md)を使用して、Data Lake Storage Gen2 のコンテンツとメタデータのインデックスを作成できます。 現時点で、ポータルまたは .NET SDK はサポートされていません。

Data Lake Storage Gen2 でのコンテンツのインデックス作成は、Azure Blob Storage でのコンテンツのインデックス作成と同じです。 そのため、Data Lake Storage Gen2 のデータソース、インデックス、およびインデクサーを設定する方法を理解するには、「[Azure Blob Storage 内ドキュメントのインデックスを Azure Cognitive Search で作成する方法](search-howto-indexing-azure-blob-storage.md)」を参照してください。 Blob Storage の記事では、サポートされているドキュメント形式、抽出される Blob メタデータ プロパティ、増分インデックス作成などに関する情報も提供されています。 この情報は、Data Lake Storage Gen2 についても同じです。

## <a name="access-control"></a>アクセス制御

Azure Data Lake Storage Gen2 では、Azure のロールベースのアクセス制御 (RBAC) と POSIX のようなアクセス制御リスト (ACL) の両方をサポートする[アクセス制御モデル](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)が実装されています。 Data Lake Storage Gen2 からコンテンツのインデックスを作成する場合、Azure Cognitive Search はコンテンツから RBAC と ACL の情報を抽出しません。 このため、この情報は Azure Cognitive Search インデックスには含められません。

インデックス内の各ドキュメントのアクセス制御を維持することが重要な場合は、アプリケーション開発者が[セキュリティによるトリミング](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search)を実装する必要があります。

## <a name="change-detection"></a>変更の検出

Data Lake Storage Gen2 インデクサーは、変更の検出をサポートしています。 これは、インデクサーの実行時に、BLOB の `LastModified` タイムスタンプによって判断された変更済みの BLOB のみ再インデックス作成を行うことを意味します。

> [!NOTE] 
> Data Lake Storage Gen2 では、ディレクトリの名前を変更できます。 ディレクトリの名前を変更しても、そのディレクトリ内の BLOB のタイムスタンプは更新されません。 その結果、インデクサーはこれらの BLOB の再インデックス作成を行いません。 ディレクトリの名前を変更した後、ディレクトリの BLOB が新しい URL を持つことから再インデックス作成を行う必要がある場合は、ディレクトリ内のすべての BLOB の `LastModified` タイムスタンプを更新する必要があります。これにより、インデクサーが将来の実行中に再インデックス作成の必要性を認識できるようになります。
