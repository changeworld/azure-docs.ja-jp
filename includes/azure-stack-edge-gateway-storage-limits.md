---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 1c6a3af7263cb4f1363425aa137fff04a6ad5120
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465678"
---
このセクションでは、Azure Stack Edge サービスに適用される Azure Storage サービスの制限および Azure Files、Azure ブロック BLOB、Azure ページ BLOB に必要な名前付け規則について説明します。 ストレージの制限を十分に確認し、すべての推奨事項に従ってください。

Azure Storage サービスの制限と共有、コンテナー、およびファイルの名前付けのベスト プラクティスに関する最新情報については、以下のページをご覧ください。

- [Naming and referencing containers](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (コンテナーの名前付けと参照)
- [Naming and referencing shares](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (共有の名前付けと参照)
- [Block blobs and page blob conventions](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) (ブロック BLOB とページ BLOB に関する規則)

> [!IMPORTANT]
> Azure Storage サービスの制限を超えているファイルまたはディレクトリ、または Azure Files/BLOB の名前付け規則に準拠していないファイルまたはディレクトリは、Azure Stack Edge サービスを通じて Azure Storage に取り込まれません。