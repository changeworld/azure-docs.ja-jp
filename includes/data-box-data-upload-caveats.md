---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 09/28/2021
ms.author: alkohli
ms.openlocfilehash: 3a0a63d68c5a63eeb4ac7855b3541471814a734b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129220806"
---
- コンテナー、共有、フォルダー:
  - 事前に作成された共有にファイルを直接コピーしないでください。 共有の下にフォルダーを作成し、そのフォルダーにファイルをコピーする必要があります。
  - *StorageAccount_BlockBlob*  と *StorageAccount_PageBlob* の下のフォルダーはコンテナーです。 コンテナーは、たとえば、*BlockBlob/container* や *PageBlob/container* として作成されます。
  - *StorageAccount_AzFile* の直下に作成された各フォルダーは、Azure ファイル共有に変換されます。
  - Azure Blob Storage は、ディレクトリに対応していません。 *StorageAccount_BlockBlob* フォルダーの下にフォルダーを作成する場合、 blob 名に仮想フォルダーが作成されます。 Azure Files では、実際のディレクトリ構造が維持されます。
- フォルダー内容のマージ:
  - *StorageAccount_BlockBlob* 共有と *StorageAccount_PageBlob* 共有に書き込まれる各ファイルは、それぞれブロック BLOB およびページ BLOB としてアップロードされます。
  - フォルダーに既存のコンテナーと同じ名前が付いている場合、フォルダーの内容はコンテナーの内容とマージされます。 クラウドにまだ存在していないファイルまたは BLOB はコンテナーに追加されます。 ファイルまたは BLOB に、コンテナーに既に存在するファイルまたは BLOB と同じ名前が付いている場合、既存のファイルまたは BLOB は上書きされます。
  - *StorageAccount_BlockBlob* フォルダーと *StorageAccount_PageBlob* フォルダーの下に作成される空のディレクトリ階層 (ファイルを含まない) はアップロードされません。
- アップロードの管理: 
  - データのアップロード中のパフォーマンスを向上させるために、[ストレージ アカウントで大きいファイルの共有を有効にし、共有容量を 100 TiB に増やす](../articles/storage/files/storage-how-to-create-file-share.md#enable-large-files-shares-on-an-existing-account)ことをお勧めします。 大きいファイルの共有は、ローカル冗長ストレージ (LRS) を持つストレージ アカウントでのみサポートされます。
  - Azure へのデータのアップロード時にエラーが発生した場合は、ターゲット ストレージ アカウントにエラー ログが作成されます。 このエラー ログへのパスは、アップロードが完了したときに使用可能になります。ログを確認して、是正措置を行うことができます。 アップロードしたデータを確認せずにソースからデータを削除しないでください。
  - 「[Azure Data Box によるファイルの ACL、属性、タイムスタンプの保持](../articles/databox/data-box-file-acls-preservation.md)」のガイダンスを使用して Azure Files にデータをアップロードすると、ファイル メタデータと NTFS アクセス許可を保持できます。
  - BLOB と Azure Files の両方で、ファイルの階層はクラウドにアップロードするときに保持されます。 たとえば、`<container folder>\A\B\C.txt` というパスでファイルをコピーしたとします。 このファイルは、クラウド内の同じパスにアップロードされます。
  
