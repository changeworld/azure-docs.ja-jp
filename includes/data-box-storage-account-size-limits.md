---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 7b46360886387bc4ab48f764edb5d01dc52dc812
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244655"
---
ここでは、ストレージ アカウントにコピーするデータのサイズ制限を示します。 アップロードするデータがこれらの制限に準拠していることを確認してください。 これらの制限の最新情報については、「[Azure Blob Storage のスケール ターゲット](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets)」および「[Azure Files のスケール ターゲット](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets)」をご覧ください。

| Azure ストレージ アカウントにコピーするデータのサイズ                      | 既定の制限          |
|---------------------------------------------------------------------|------------------------|
| ブロック BLOB とページ BLOB                                            | ストレージ アカウントあたり 500 TiB。 <br> これには、Data Box を含むすべてのソースのデータが含まれます。|
| Azure ファイル                                                          | 共有あたり 5 TiB です。<br> *StorageAccount_AzureFiles* の下のすべてのフォルダーはこの制限に従う必要があります。       |
