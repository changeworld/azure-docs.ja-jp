---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 16647b6a13e64073ab570d36a8a380d0e36bd855
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "73799943"
---
- 事前に作成された共有にファイルを直接コピーしないでください。 共有の下にフォルダーを作成し、そのフォルダーにファイルをコピーする必要があります。
- *StorageAccount_BlockBlob*  と *StorageAccount_PageBlob* の下のフォルダーはコンテナーです。 コンテナーは、たとえば、*BlockBlob/container* や *PageBlob/container* として作成されます。
- *StorageAccount_AzureFiles* の直下に作成された各フォルダーは、Azure ファイル共有に変換されます。
- コピー対象のオブジェクトと同じ名前の既存の Azure オブジェクト (BLOB やファイルなど) がクラウド内にある場合、Data Box はクラウド内のファイルを上書きします。
- *StorageAccount_BlockBlob* 共有と *StorageAccount_PageBlob* 共有に書き込まれる各ファイルは、それぞれブロック BLOB およびページ BLOB としてアップロードされます。
- Azure blob ストレージは、ディレクトリをサポートしていません。 *StorageAccount_BlockBlob* フォルダーの下にフォルダーを作成する場合、 blob 名に仮想フォルダーが作成されます。 Azure Files では、実際のディレクトリ構造が維持されます。
- *StorageAccount_BlockBlob* フォルダーと *StorageAccount_PageBlob* フォルダーの下に作成される空のディレクトリ階層 (ファイルを含まない) はアップロードされません。
- Azure へのデータのアップロード時にエラーが発生した場合は、ターゲット ストレージ アカウントにエラー ログが作成されます。 このエラー ログへのパスは、アップロードが完了した時に使用可能になります。ログを確認して、是正措置を行うことができます。 アップロードしたデータを確認せずにソースからデータを削除しないでください。
- データが Azure Files にアップロードされた場合、ファイル メタデータと NTFS のアクセス許可は保持されません。 たとえば、ファイルの*最後に変更された属性*は、データがコピーされるときに保持されません。
