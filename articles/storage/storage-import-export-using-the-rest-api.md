---
title: "Azure Import/Export サービス REST API の使用 | Microsoft Docs"
description: "Azure Import/Export サービス REST API を使用する方法について説明します"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 233f80e9-2e7f-48e0-9639-5c7785e7d743
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 22e37e26fec913a7638c71b2547c38f5efacd10b
ms.openlocfilehash: 675aef8ec0f9344d2b3073021e83856c2fb34b7c


---
# <a name="using-the-azure-importexport-service-rest-api"></a>Azure Import/Export サービス REST API の使用

Microsoft Azure Import/Export サービスでは、インポート/エクスポート ジョブのプログラムによる制御を有効にする REST API を公開します。 REST API を使用すると、すべてのインポート/エクスポート操作を [Azure Portal](https://portal.azure.com/) で実行できます。 また、REST API を使用して、現時点でクラシック ポータルでは利用できない詳細な操作 (ジョブの完了率の照会など) を実行することもできます。

Import/Export サービスの概要、およびクラシック ポータルを使用してインポート/エクスポート ジョブを作成および管理する方法を示すチュートリアルについては、「[Microsoft Azure Import/Export サービスを使用した Blob Storage へのデータの転送](storage-import-export-service.md)」を参照してください。

## <a name="service-endpoints"></a>サービス エンドポイント

Azure Import/Export サービスは、Azure Resource Manager のリソース プロバイダーであり、インポート/エクスポート ジョブを管理するために次の HTTPS エンドポイントで REST API のセットを提供します。

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>バージョン管理

Import/Export サービスに対する要求では、`api-version` パラメーターを指定し、その値を `2016-11-01` に設定する必要があります。

## <a name="in-this-section"></a>このセクションの内容

[インポート ジョブの作成](storage-import-export-creating-an-import-job.md)

[エクスポート ジョブの作成](storage-import-export-creating-an-export-job.md)

[ジョブの状態情報の取得](storage-import-export-retrieving-state-info-for-a-job.md)

[ジョブの列挙](storage-import-export-enumerating-jobs.md)

[ジョブのキャンセルと削除](storage-import-export-cancelling-and-deleting-jobs.md)

[ドライブ マニフェストのバックアップ](storage-import-export-backing-up-drive-manifests.md)

[インポート/エクスポート ジョブの診断とエラーからの回復](storage-import-export-diagnostics-and-error-recovery.md)

## <a name="see-also"></a>関連項目
 [ストレージのインポート/エクスポート REST](/rest/api/storageimportexport)



<!--HONumber=Dec16_HO3-->


