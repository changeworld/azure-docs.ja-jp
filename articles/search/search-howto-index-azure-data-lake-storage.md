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
ms.openlocfilehash: 628b8bb5c3cb83ae6038a7150420893d7abe61d5
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112290"
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

Data Lake Storage Gen2 でのコンテンツのインデックス作成は、Azure Blob Storage でのコンテンツのインデックス作成と同じです。 そのため、Data Lake Storage Gen2 のデータソース、インデックス、およびインデクサーを設定する方法を理解するには、「[Azure Blob Storage 内ドキュメントのインデックスを Azure コグニティブ検索で作成する方法](search-howto-indexing-azure-blob-storage.md)」を参照してください。 Blob Storage の記事では、サポートされているドキュメント形式、抽出される Blob メタデータ プロパティ、増分インデックス作成などに関する情報も提供されています。 この情報は、Data Lake Storage Gen2 についても同じです。

## <a name="access-control"></a>アクセス制御

Azure Data Lake Storage Gen2 では、Azure のロールベースのアクセス制御 (RBAC) と POSIX のようなアクセス制御リスト (ACL) の両方をサポートする[アクセス制御モデル](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)が実装されています。 Data Lake Storage Gen2 からコンテンツのインデックスを作成する場合、Azure Cognitive Search はコンテンツから RBAC と ACL の情報を抽出しません。 このため、この情報は Azure Cognitive Search インデックスには含められません。

インデックス内の各ドキュメントのアクセス制御を維持することが重要な場合は、アプリケーション開発者が[セキュリティによるトリミング](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search)を実装する必要があります。