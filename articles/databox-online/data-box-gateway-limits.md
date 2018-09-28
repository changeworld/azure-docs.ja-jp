---
title: Azure Data Box Gateway の制限 | Microsoft Docs
description: Microsoft Azure Data Box Gateway のシステム制限と推奨サイズについて説明します。
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/24/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: edb4995b626055be830a7accb74d99f1db3ef8d0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962204"
---
# <a name="azure-data-box-gateway-limits-preview"></a>Azure Data Box Gateway の制限 (プレビュー)


Microsoft Azure Data Box Gateway ソリューションをデプロイおよび運用する際には、以下の制限事項を考慮してください。 

> [!IMPORTANT] 
> Data Box Gateway はプレビュー段階にあります。 このソリューションをデプロイする前に、[プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)を確認してください。 


## <a name="data-box-gateway-service-limits"></a>Data Box Gateway サービスの制限

- このリリースでは、米国、EU、アジア太平洋地域の特定のリージョンでのみサービスが利用可能です。 詳細については、「[利用可能なリージョン](#data-box-gateway-overview#region-availability)」を参照してください。 ストレージ アカウントは、デバイスがデプロイされたリージョンに物理的に最も近い場所にする必要があります (サービス地域と異なる場合があります)。
- Data Box Gateway リソースの別のサブスクリプションまたはリソース グループへの移動は、サポートされていません。 詳細については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)」を参照してください。

## <a name="data-box-gateway-device-limits"></a>Data Box Gateway デバイスの制限

次の表で、Data Box Gateway デバイスの制限について説明します。

| 説明 | 値 |
|---|---|
|いいえ。 デバイスあたりのファイル数 |1 億 <br> 制限は、2 TB のディスク領域ごとに最大 2500 万ファイルであり、上限は 1 億です |
|いいえ。 デバイスあたりの共有数 |24 |
|1 つの共有に書き込まれる最大サイズ ファイル|5 TB |

## <a name="azure-storage-limits"></a>Azure Storage の制限

ここでは、Data Box Gateway/Data Box Edge サービスに適用される Azure Storage サービスの制限および Azure Files、Azure ブロック BLOB、Azure ページ BLOB に必要な名前付け規則について説明します。 ストレージの制限を十分に確認し、すべての推奨事項に従ってください。

Azure Storage サービスの制限と共有、コンテナー、およびファイルの名前付けのベスト プラクティスに関する最新情報については、以下のページをご覧ください。

- [Naming and referencing containers](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (コンテナーの名前付けと参照)
- [Naming and referencing shares](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (共有の名前付けと参照)
- [Block blobs and page blob conventions](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) (ブロック BLOB とページ BLOB に関する規則)

> [!IMPORTANT]
> Azure Storage サービスの制限を超えているファイル/ディレクトリ、または Azure Files/BLOB の名前付け規則に準拠していないファイル/ディレクトリは、Data Box Gateway/Data Box Edge サービスを通じて Azure Storage に取り込まれません。

## <a name="data-upload-caveats"></a>データのアップロードに関する注意事項

Azure にデータを移動する際には、次の注意事項がデータに適用されます。

- 複数のデバイスが同じコンテナーに書き込まないようにすることをお勧めします。
- コピー対象のオブジェクトと同じ名前の既存の Azure オブジェクト (BLOB やファイルなど) がクラウド内にある場合、デバイスはクラウド内のファイルを上書きします。 
- 共有フォルダーの下に作成される空のディレクトリ階層 (ファイルを含まない) は、BLOB コンテナーにアップロードされません。


## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure ストレージ アカウント サイズとオブジェクト サイズの制限

ここでは、ストレージ アカウントにコピーするデータのサイズ制限を示します。 アップロードするデータがこれらの制限に準拠していることを確認してください。 これらの制限の最新情報については、「[Azure Blob Storage のスケール ターゲット](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets)」および「[Azure Files のスケール ターゲット](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets)」をご覧ください。

| Azure ストレージ アカウントにコピーするデータのサイズ                      | 既定の制限          |
|---------------------------------------------------------------------|------------------------|
| ブロック BLOB とページ BLOB                                            | ストレージ アカウントあたり 500 TB|


## <a name="azure-object-size-limits"></a>Azure オブジェクトのサイズ制限

ここでは、書き込むことのできる Azure オブジェクトのサイズを示します。 アップロードするすべてのファイルがこれらの制限に準拠していることを確認してください。

| Azure オブジェクトの種類 | 既定の制限                                             |
|-------------------|-----------------------------------------------------------|
| ブロック BLOB        | 8 TB 以下                                                 |
| ページ BLOB         | 1 TB (テラバイト) <br> ページ BLOB 形式でアップロードするファイルには 512 バイトをアライン (整数倍) する必要があります。そうでないと、アップロードが失敗します。 <br> VHD と VHDX には 512 バイトがアラインされます。 |


## <a name="next-steps"></a>次の手順

- [Azure Data Box Gateway のデプロイを準備する](data-box-gateway-deploy-prep.md)
