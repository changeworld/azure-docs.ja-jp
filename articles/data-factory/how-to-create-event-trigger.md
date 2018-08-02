---
title: Azure Data Factory でイベントベースのトリガーを作成する | Microsoft Docs
description: イベントに応答してパイプラインを実行するトリガーを Azure Data Factory で作成する方法について説明します。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/23/2018
ms.author: douglasl
ms.openlocfilehash: 53ea7425f0497eca7c95ddefeaa09aa40259672b
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216271"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>イベントに応答してパイプラインを実行するトリガーを作成する

この記事では、Data Factory のパイプラインで作成できるイベントベースのトリガーについて説明します。

イベントドリブン アーキテクチャ (EDA) は、イベントの運用、検出、使用、および応答を含む一般的なデータ統合パターンです。 多くの場合、データ統合シナリオでは、Data Factory ユーザーがイベントに基づいてパイプラインをトリガーする必要があります。 Data Factory は [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) と統合され、イベントに対してパイプラインをトリガーすることができるようになりました。

この機能の概要とデモンストレーションについては、以下の 10 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> この記事で説明されている統合は、[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) に依存しています。 サブスクリプションが Event Grid リソース プロバイダーに登録されていることを確認してください。 詳細については、「[リソース プロバイダーと種類](../azure-resource-manager/resource-manager-supported-services.md#portal)」を参照してください。

## <a name="data-factory-ui"></a>Data Factory UI

### <a name="create-a-new-event-trigger"></a>新しいイベント トリガーを作成する

一般的なイベントは、Azure Storage アカウントでのファイルの到着、またはファイルの削除です。 Data Factory のパイプラインでこのイベントに応答するトリガーを作成できます。

> [!NOTE]
> この統合では、バージョン 2 のストレージ アカウント (汎用) のみがサポートされます。

![新しいイベント トリガーを作成する](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="configure-the-event-trigger"></a>イベント トリガーを構成する

**[Blob path begins with]\(Blob パスの先頭\)** と **[Blob path ends with]\(Blob パスの末尾\)** のプロパティでは、イベントを受け取るコンテナー、フォルダー、および BLOB の名前を指定できます。 この記事で後述する例に示すように、**[Blob path begins with]\(Blob パスの先頭\)** と **[Blob path ends with]\(Blob パスの末尾\)** のプロパティにはさまざまなパターンを使用できます。 これらのプロパティの少なくとも 1 つを指定する必要があります。

![イベント トリガーを構成する](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="select-the-event-trigger-type"></a>イベント トリガーの種類を選択する

ファイルがストレージの場所に到着し、対応する BLOB が作成されるとすぐに、このイベントがトリガーされ、Data Factory パイプラインが実行されます。 Data Factory のパイプラインで、BLOB 作成イベント、BLOB 削除イベント、または両方のイベントに応答するトリガーを作成できます。

![イベントとしてトリガーの種類を選択する](media/how-to-create-event-trigger/event-based-trigger-image3.png)

### <a name="map-trigger-properties-to-pipeline-parameters"></a>トリガー プロパティをパイプライン パラメーターにマップする

特定の BLOB に対してイベント トリガーが発生した場合、イベントはその BLOB のフォルダー パスとファイル名を、プロパティ `@triggerBody().folderPath` および `@triggerBody().fileName` にキャプチャします。 パイプラインでこれらのプロパティの値を使用するには、プロパティをパイプライン パラメーターにマップする必要があります。 プロパティをパラメーターにマップしたら、パイプライン全体で `@pipeline.parameters.parameterName` 式を使用して、トリガーでキャプチャされた値にアクセスできます。

![パイプライン パラメーターへのプロパティのマッピング](media/how-to-create-event-trigger/event-based-trigger-image4.png)

たとえば、前のスクリーンショットでは、 `.csv` で終わる BLOB パスがストレージ アカウントで作成されたときに発生するようにトリガーが構成されています。 その結果、`.csv` 拡張子を持つ BLOB がストレージ アカウント内の任意の場所で作成されたときに、`folderPath` および `fileName` プロパティでは新しい BLOB の場所がキャプチャされます。 たとえば、`@triggerBody().folderPath` が `/containername/foldername/nestedfoldername` などの値を持ち、`@triggerBody().fileName` が `filename.csv` などの値を持つとします。 この例では、これらの値はパイプライン パラメーターの `sourceFolder` および `sourceFile` にマップされます。 これらをそれぞれ `@pipeline.parameters.sourceFolder` および `@pipeline.parameters.sourceFile` として、パイプライン全体で使用することができます。

## <a name="json-schema"></a>JSON スキーマ

次の表に、イベントベースのトリガーに関連するスキーマ要素の概要を示します。

| **JSON 要素** | **説明** | **種類** | **使用できる値** | **必須** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | ストレージ アカウントの Azure Resource Manager リソース ID。 | String | Azure Resource Manager ID | [はい] |
| **events** | このトリガーを起動するイベントの種類。 | Array    | Microsoft.Storage.BlobCreated、Microsoft.Storage.BlobDeleted | はい (任意の組み合わせ)。 |
| **blobPathBeginsWith** | BLOB パスは、トリガーを起動するために指定されているパターンで始まる必要があります。 たとえば、'/records/blobs/december/' の場合、レコード コンテナーの december フォルダー内にある BLOB のトリガーのみが起動されます。 | String   | | blobPathBeginsWith と blobPathEndsWith のプロパティの少なくとも 1 つを指定する必要があります。 |
| **blobPathEndsWith** | BLOB パスは、トリガーを起動するために指定されているパターンで終わる必要があります。 たとえば、'december/boxes.csv' の場合、december フォルダー内の boxes という名前の BLOB のトリガーのみが起動されます。 | String   | | blobPathBeginsWith と blobPathEndsWith のプロパティの少なくとも 1 つを指定する必要があります。 |

## <a name="examples-of-event-based-triggers"></a>イベントベース トリガーの例

このセクションでは、イベントベースのトリガー設定の例を示します。

-   **[Blob path begins with]\(Blob パスの先頭\)**('/containername/'): コンテナー内のすべての BLOB のイベントを受け取ります。
-   **[Blob path begins with]\(Blob パスの先頭\)**('/containername/blobs/foldername'): containername コンテナーと foldername フォルダー内のすべての BLOB のイベントを受け取ります。
-   **[Blob path begins with]\(Blob パスの先頭\)**('/containername/blobs/foldername/file.txt'): containername コンテナー以下の foldername フォルダー内にある file.txt という BLOB のイベントを受け取ります。
-   **[Blob path ends with]\(Blob パスの末尾\)**('file.txt'): 任意のパスにある file.txt という BLOB のイベントを受け取ります。
-   **[Blob path ends with]\(Blob パスの末尾\)**('/containername/blobs/file.txt'): containername コンテナー以下の foldername フォルダー内にある file.txt という BLOB のイベントを受け取ります。
-   **[Blob path ends with]\(Blob パスの末尾\)**('foldername/file.txt'): 任意のコンテナー以下の foldername フォルダー内にある file.txt という BLOB のイベントを受け取ります。

> [!NOTE]
> コンテナーとフォルダー、コンテナーとファイル、またはコンテナー、フォルダー、およびファイルを指定するたびに、パスの `/blobs/` セグメントを含める必要があります。

## <a name="next-steps"></a>次の手順
トリガーについて詳しくは、「[Azure Data Factory でのパイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md#triggers)」をご覧ください。
