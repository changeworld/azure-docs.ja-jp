---
title: Azure Data Lake Storage の Premium サービス レベル
description: Azure Data Lake Storage Gen2 で Premium パフォーマンス レベルを使用する
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: normesta
ms.openlocfilehash: e9cab4b65da62bddd47cdab97c6f586b07f379d4
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130044453"
---
# <a name="premium-tier-for-azure-data-lake-storage"></a>Azure Data Lake Storage の Premium サービス レベル

Azure Data Lake Storage Gen2 では、[Premium ブロック BLOB ストレージ アカウント](storage-blob-block-blob-premium.md)がサポートされるようになりました。 Premium ブロック BLOB ストレージ アカウントは、ビッグ データ分析のアプリケーションやワークロードでトランザクション数が多く、常に低遅延が必要とされる場合に最適です。 ワークロードの例として、対話型ワークロード、IoT、ストリーミング分析、人工知能、機械学習などがあります。 

>[!TIP]
> Premium ブロック BLOB ストレージ アカウントを使用する場合のパフォーマンスとコスト面の利点に関する詳細について、また、Data Lake Storage Gen2 をご利用いただいている他のお客様がこの種類のアカウントをどのように使用しているかについては、「[Premium ブロック BLOB ストレージ アカウント](storage-blob-block-blob-premium.md)」を参照してください。

## <a name="getting-started-with-premium"></a>Premium の概要

まず、お気に入りの BLOB ストレージ機能が Premium ブロック BLOB ストレージ アカウントと互換性があることを確認し、アカウントを作成します。 

>[!NOTE]
> 既存の Standard 汎用 v2 ストレージ アカウントを Premium ブロック BLOB ストレージ アカウントに変換することはできません。 Premium ブロック BLOB ストレージ アカウントに移行するには、Premium ブロック BLOB ストレージ アカウントを作成し、データを新しいアカウントに移行する必要があります。 

### <a name="check-for-blob-storage-feature-compatibility"></a>Blob ストレージ機能の互換性を確認する

一部の BLOB ストレージ機能はまだサポートされていないか、Premium ブロック BLOB ストレージ アカウントでは部分的にしかサポートされていません。 Premium を選択する前に、「[Azure ストレージ アカウントにおける Blob Storage 機能のサポート](storage-feature-support-in-storage-accounts.md)」という記事を確認し、使用する予定の機能がお使いのアカウントで完全にサポートされているかどうかを判断してください。 機能のサポートは常に拡張されているため、この記事の更新について定期的に確認してください。

### <a name="create-a-new-storage-account"></a>新しいストレージ アカウントを作成する

新しい Azure Storage アカウントを作成します。 詳細なガイダンスについては、「[ストレージ アカウントを作成する](../common/storage-account-create.md)」を参照してください。 

アカウントを作成するときは、パフォーマンス オプションとして **[Premium]** を、アカウントの種類として **[ブロック BLOB]** を選択します。 

> [!div class="mx-imgBorder"]
> ![ブロック BLOB ストレージ アカウントを作成する](./media/storage-blob-block-blob-premium/create-block-blob-storage-account.png)

Azure Data Lake Storage Gen2 の機能をロック解除するには、 **[ストレージ アカウントの作成]** ページの **[詳細設定]** タブで **[階層型名前空間]** 設定を有効にします。 

次の図は、 **[ストレージ アカウントの作成]** ページのこの設定を示しています。

> [!div class="mx-imgBorder"]
> ![階層型名前空間の設定](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

## <a name="next-steps"></a>次のステップ

Azure Data Lake Storage の Premium サービス レベルを、Azure Databricks、Azure HDInsight、Azure Synapse Analytics などの任意の分析サービスで使用します。 「[Azure Data Lake Storage Gen2 で Azure サービスを使用するチュートリアル](data-lake-storage-integrate-with-services-tutorials.md)」を参照してください。
