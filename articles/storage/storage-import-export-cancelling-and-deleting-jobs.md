---
title: "Azure Import/Export サービス ジョブの取り消しと削除 | Microsoft Docs"
description: "Microsoft Azure Import/Export サービスのジョブを取り消したり削除する方法について説明します"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: fd3d66f0-1dbb-4c75-9223-307d5abaeefc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 22e37e26fec913a7638c71b2547c38f5efacd10b
ms.openlocfilehash: 640b814e4280f5cce029bea7324881bbfef4b1a4


---

# <a name="cancelling-and-deleting-jobs"></a>ジョブの取り消しと削除
ジョブが `Packaging` 状態になる前に、[Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) 操作を呼び出して `CancelRequested` 要素を `true` に設定することで、ジョブの取り消しを要求できます。 ジョブの取り消しはベストエフォートで実行されます。 ドライブでデータ転送が実行中である場合は、取り消しが要求された後でも、データの転送が継続されることがあります。

 取り消されたジョブは `Completed` 状態に移行され、90 日間維持されます (その後、削除されます)。

 ジョブを削除するには、ジョブが発送される前 (*つまり*、ジョブが `Creating` 状態である間) に、[Delete Job](/rest/api/storageimportexport/jobs#Jobs_Delete) 操作を呼び出します。 ジョブの削除は、ジョブが `Completed` 状態のときにも実行できます。 ジョブが削除されると、REST API や Azure ポータルを通じてそのジョブの情報と状態にアクセスすることはできなくなります。

## <a name="see-also"></a>関連項目
 [Import/Export サービス REST API の使用](storage-import-export-using-the-rest-api.md)



<!--HONumber=Dec16_HO3-->


