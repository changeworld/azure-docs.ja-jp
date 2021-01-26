---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: b871291a7645c43ff3c5c382905958d35331ce2a
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581197"
---
ここでは、Azure Stack Edge/Data Box Gateway サービスに適用される Azure Storage サービスの制限および Azure Files、Azure ブロック BLOB、Azure ページ BLOB に必要な名前付け規則について説明します。 ストレージの制限を十分に確認し、すべての推奨事項に従ってください。

Azure Storage サービスの制限と共有、コンテナー、およびファイルの名前付けのベスト プラクティスに関する最新情報については、以下のページをご覧ください。

- [Naming and referencing containers](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (コンテナーの名前付けと参照)
- [Naming and referencing shares](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (共有の名前付けと参照)
- [Block blobs and page blob conventions](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) (ブロック BLOB とページ BLOB に関する規則)

> [!IMPORTANT]
> Azure Storage サービスの制限を超えているファイル/ディレクトリ、または Azure Files/BLOB の名前付け規則に準拠していないファイル/ディレクトリは、Azure Stack Edge/Data Box Gateway サービスを通じて Azure Storage に取り込まれません。