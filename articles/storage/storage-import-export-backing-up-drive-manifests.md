---
title: "Azure Import/Export ドライブ マニフェストのバックアップ | MicrosoftDocs"
description: "Microsoft Azure Import/Export サービスのドライブ マニフェストを自動的にバックアップする方法について説明します。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 594abd80-b834-4077-a474-d8a0f4b7928a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 22e37e26fec913a7638c71b2547c38f5efacd10b
ms.openlocfilehash: 3d3abfb93ba09aa9f09258743ef6d4c1fc932ae5


---

# <a name="backing-up-drive-manifests"></a>ドライブ マニフェストのバックアップ
ドライブ マニフェストは、[Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) または [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) 操作で `BackupDriveManifest` プロパティを `true` に設定することで、BLOB に自動的にバックアップできます。 既定では、ドライブ マニフェストはバックアップされません。 ドライブ マニフェストのバックアップは、ジョブに関連付けられたストレージ アカウント内のコンテナーに、ブロック BLOB として保存されます。 既定では、コンテナー名は `waimportexport` ですが、`Put Job` または `Update Job Properties` 操作を呼び出す際に、`DiagnosticsPath` プロパティで異なる名前を指定することもできます。 バックアップ マニフェスト BLOB は、次の形式で命名されます: `waies/jobname_driveid_timestamp_manifest.xml`。

 ジョブのバックアップ ドライブ マニフェストの URI は、[Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) 操作を呼び出すことで取得できます。 BLOB URI は、各ドライブの `ManifestUri` プロパティで返されます。

## <a name="see-also"></a>関連項目
 [Import/Export サービス REST API の使用](storage-import-export-using-the-rest-api.md)



<!--HONumber=Dec16_HO3-->


