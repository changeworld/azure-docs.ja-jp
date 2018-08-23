---
title: Azure Data Box Disk の制限 | Microsoft Docs
description: Microsoft Azure Data Box Disk のシステム制限と推奨サイズについて説明します。
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/12/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 358f81498d8caad7c0a67519bbb776efa153249f
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2018
ms.locfileid: "42140936"
---
# <a name="azure-data-box-disk-limits-preview"></a>Azure Data Box Disk の制限 (プレビュー)


Microsoft Azure Data Box Disk ソリューションをデプロイおよび運用する際には、以下の制限事項を考慮してください。 

> [!IMPORTANT] 
> Azure Data Box Disk はプレビュー段階です。 このソリューションをデプロイする前に、[プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)を確認してください。 


## <a name="data-box-service-limits"></a>Data Box サービスの制限

 - Data Box サービスは、米国と EU における Azure パブリック クラウドのすべての Azure リージョンでのみ利用できます。
 - Data Box Disk では 1 つのストレージ アカウントがサポートされます。

## <a name="data-box-disk-performance"></a>Data Box Disk のパフォーマンス

USB 3.0 で接続したディスクでテストしたところ、ディスクのパフォーマンスは最大 430 MB/s でした。 実際の数値は、使用するファイルのサイズによって異なります。 ファイルのサイズが小さい場合は、パフォーマンスが低下する可能性があります。

## <a name="azure-storage-limits"></a>Azure Storage の制限

ここでは、Data Box サービスに適用される Azure Storage サービスの制限および Azure Files、Azure ブロック BLOB、Azure ページ BLOB に必要な名前付け規則について説明します。 ストレージの制限を十分に確認し、すべての推奨事項に従ってください。

Azure Storage サービスの制限と共有、コンテナー、およびファイルの名前付けのベスト プラクティスに関する最新情報については、以下のページをご覧ください。

- [Naming and referencing containers](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (コンテナーの名前付けと参照)
- [Naming and referencing shares](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (共有の名前付けと参照)
- [Block blobs and page blob conventions](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) (ブロック BLOB とページ BLOB に関する規則)

> [!IMPORTANT]
> Azure Storage サービスの制限を超えているファイル/ディレクトリ、または Azure Files/BLOB の名前付け規則に準拠していないファイル/ディレクトリは、Data Box サービスを通じて Azure Storage に取り込まれません。

## <a name="data-upload-caveats"></a>データのアップロードに関する注意事項

- ディスクに直接データをコピーしないでください。 事前に作成した *BlockBlob* フォルダーと *PageBlob* フォルダーにデータをコピーします。
- *BlockBlob* および *PageBlob* の下位のフォルダーはコンテナーです。 たとえば、*BlockBlob/container* および *PageBlob/container* としてコンテナーが作成されます。
- コピー対象のオブジェクトと同じ名前の既存の Azure オブジェクト (BLOB など) がクラウド内にある場合、Data Box Disk はクラウド内のファイルを上書きします。
- *BlockBlob* 共有と *PageBlob* 共有に書き込まれる各ファイルは、それぞれブロック BLOB およびページ BLOB としてアップロードされます。
- *BlockBlob* フォルダーと *PageBlob* フォルダーの下に作成される空のディレクトリ階層 (ファイルを含まない) はアップロードされません。
- Azure へのデータのアップロード時にエラーが発生した場合は、ターゲット ストレージ アカウントにエラー ログが作成されます。 このエラー ログへのパスは、アップロードが完了するとポータルに表示されます。ログを確認して、是正措置を行うことができます。 アップロードしたデータを確認せずにソースからデータを削除しないでください。

## <a name="azure-storage-account-size-limits"></a>Azure ストレージ アカウントのサイズ制限

ここでは、ストレージ アカウントにコピーするデータのサイズ制限を示します。 アップロードするデータがこれらの制限に準拠していることを確認してください。 これらの制限の最新情報については、「[Azure Blob Storage のスケール ターゲット](https://docs.microsoft.com/en-us/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets)」および「[Azure Files のスケール ターゲット](https://docs.microsoft.com/en-us/azure/storage/common/storage-scalability-targets#azure-files-scale-targets)」をご覧ください。

| Azure ストレージ アカウントにコピーするデータのサイズ                      | 既定の制限          |
|---------------------------------------------------------------------|------------------------|
| ブロック BLOB とページ BLOB                                            | ストレージ アカウントあたり 500 TB。 <br> これには、Data Box Disk を含むすべてのソースのデータが含まれます。|


## <a name="azure-object-size-limits"></a>Azure オブジェクトのサイズ制限

ここでは、書き込むことのできる Azure オブジェクトのサイズを示します。 アップロードするすべてのファイルがこれらの制限に準拠していることを確認してください。

| Azure オブジェクトの種類 | 既定の制限                                             |
|-------------------|-----------------------------------------------------------|
| ブロック BLOB        | 8 TB 以下                                                 |
| ページ BLOB         | 1 TB (テラバイト) <br> (ページ BLOB 形式でアップロードするファイルには 512 バイトをアライン (整数倍) する必要があります。そうでないと、アップロードが失敗します。 <br> VHD と VHDX には 512 バイトがアラインされます。) |


## <a name="azure-block-blob-and-page-blob-naming-conventions"></a>Azure ブロック BLOB とページ BLOB の名前付け規則

| エンティティ                                       | 規則                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ブロック BLOB とページ BLOB のコンテナー名 | 3 ～ 63 文字の有効な DNS 名にする必要があります。 <br>  先頭は文字か数字にします。 <br> 小文字、数字、ハイフン (-) のみを含めることができます。 <br> すべてのハイフン (-) は、その直前または直後に文字または数字が使用されている必要があります。 <br> 連続するハイフンを名前に使用することはできません。 |
| ブロック BLOB とページ BLOB の BLOB 名      | BLOB 名は、大文字と小文字が区別されます。また、名前には任意の組み合わせの文字を使用できます。 <br> BLOB 名は 1 文字から 1,024 文字にする必要があります。 <br> URL の予約文字は適切にエスケープしてください。 <br>BLOB 名を構成するパスのセグメントの数が 254 個を超えないようにしてください。 パスのセグメントは、仮想ディレクトリの名前に対応した連続する区切り記号文字 (スラッシュ "/" など) の間の文字列です。 |
