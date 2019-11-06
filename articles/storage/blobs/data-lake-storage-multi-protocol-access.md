---
title: Azure Data Lake Storage のマルチプロトコル アクセス (プレビュー) | Microsoft Docs
description: BLOB API と、BLOB API と Azure Data Lake Storage Gen2 を使用するアプリケーションを使用します。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 9767282b3dd764a45f25a14d62af70a13c80b0ac
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300257"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage-preview"></a>Azure Data Lake Storage のマルチプロトコル アクセス (プレビュー)

階層型名前空間があるアカウントで、BLOB API を使用できるようになりました。 これにより、階層型名前空間があるアカウントに対する BLOB ストレージ機能だけでなく、ツール、アプリケーション、およびサービスのエコシステム全体を使用できます。

最近まで、オブジェクト ストレージと分析ストレージ用に別々のストレージ ソリューションを管理する必要がありました。 このため、Azure Data Lake Storage Gen2 によるエコシステムのサポートが制限されていました。 さらに、診断ログなどの BLOB サービスの機能へのアクセスも制限されていました。 さまざまなシナリオを実現するにはアカウント間でデータを移動する必要があるため、寸断されたストレージ ソリューションは管理が容易ではありません。 もう、それを行う必要はありません。

> [!NOTE]
> Data Lake Storage のマルチプロトコル アクセスはパブリック プレビュー段階であり、すべてのリージョンで利用できます。 それは、階層型名前空間を持つすべてのアカウントで自動的に使用できるようになるため、パブリック プレビューに登録する必要はありません。 制限事項を確認するには、[既知の問題](data-lake-storage-known-issues.md)に関する記事を参照してください。

## <a name="use-the-entire-ecosystem-of-applications-tools-and-services"></a>アプリケーション、ツール、およびサービスのエコシステム全体を使用する

Data Lake Storage のマルチプロトコル アクセスでは、ツール、アプリケーション、およびサービスのエコシステム全体を使用して、すべてのデータを操作できます。 これには、[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction)、[IOT Hub](https://docs.microsoft.com/azure/iot-hub/)、[Power BI](https://docs.microsoft.com/power-bi/desktop-data-sources) やその他の多数の Azure が含まれています。 完全な一覧については、「[Integrate Azure Data Lake Storage with Azure services](data-lake-store-integrate-with-azure-services.md)」 (Azure Data Lake Storage と Azure サービスを統合する) を参照してください。

これには、サードパーティのツールとアプリケーションも含まれています。 それらを変更することなく、それらで階層型名前空間があるアカウントをポイントすることができます。 BLOB API で階層型名前空間があるアカウントのデータを操作できるようになったため、これらのアプリケーションで BLOB API を呼び出した場合でも、*現状のままで*動作します。

> [!NOTE]
> 制限事項を確認するには、[既知の問題](data-lake-storage-known-issues.md)に関する記事を参照してください。

## <a name="use-all-blob-storage-features"></a>すべての BLOB ストレージ機能を使用する

階層型名前空間があるアカウントで、[診断ログ](../common/storage-analytics-logging.md)、[アクセス レベル](storage-blob-storage-tiers.md)、[BLOB ストレージ ライフサイクル管理ポリシー](storage-lifecycle-management-concepts.md)などの BLOB ストレージの機能が動作するようになりました。 そのため、これらの重要な機能へのアクセスを失うことなく、BLOB ストレージ アカウントで階層型名前空間を有効にできます。 

> [!NOTE]
> 制限事項を確認するには、[既知の問題](data-lake-storage-known-issues.md)に関する記事を参照してください。

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Azure Data Lake Storage のマルチプロトコル アクセスの実行方法

階層型名前空間があるストレージ アカウントの同じデータに対して、BLOB API と Data Lake Storage Gen2 API を操作できます。 Data Lake Storage Gen2 では階層型名前空間を使用して BLOB API がルーティングされるため、第一級のディレクトリ操作と POSIX 準拠のアクセス制御リスト (ACL) のメリットを活用できます。 

![Azure Data Lake Storage のマルチプロトコル アクセスの概念](./media/data-lake-storage-interop/interop-concept.png) 

BLOB API を使用している既存のツールとアプリケーションでは、これらのメリットが自動的に活用されます。 開発者は、それらを変更する必要はありません。 Data Lake Storage Gen2 では、データにアクセスするためにツールとアプリケーションで使用されるプロトコルに関係なく、ディレクトリとファイルレベルの ACL が一貫して適用されます。 

## <a name="next-steps"></a>次の手順

[既知の問題](data-lake-storage-known-issues.md)を参照してください。




