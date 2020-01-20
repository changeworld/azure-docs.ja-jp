---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 6a9c31eb46a457433834d5940b3f7e190ebe1476
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467306"
---
ここでは、ストレージ アカウントにコピーするデータのサイズ制限を示します。 アップロードするデータがこれらの制限に準拠していることを確認してください。 これらの制限に関する最新情報については、「[BLOB ストレージのスケーラビリティとパフォーマンスのターゲット](../articles/storage/blobs/scalability-targets.md)」と「[Azure Files のスケーラビリティとパフォーマンスのターゲット](../articles/storage/files/storage-files-scale-targets.md)」を参照してください。

| Azure ストレージ アカウントにコピーするデータのサイズ                      | 既定の制限          |
|---------------------------------------------------------------------|------------------------|
| ブロック BLOB とページ BLOB                                            | 米国およびヨーロッパでは 2 PB。<br>英国を含む他のすべてのリージョンでは 500 TB。  <br> これには、Data Box を含むすべてのソースのデータが含まれます。|
| Azure Files                                                          | 共有あたり 5 TB。<br> *StorageAccount_AzureFiles* の下のすべてのフォルダーはこの制限に従う必要があります。       |
