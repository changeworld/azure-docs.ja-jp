---
title: Azure Import/Export サービスの要件 | Microsoft Azure
description: Azure Import/Export サービスのソフトウェアとハードウェアの要件を理解します。
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: alkohli
ms.component: common
ms.openlocfilehash: 10e8fb6ac5bcce278de3924ebd3a0d9f90392217
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528056"
---
# <a name="azure-importexport-system-requirements"></a>Azure Import/Export のシステム要件

この記事では、Azure Import/Export サービスの重要な要件について説明します。 Import/Export サービスを使用する前に以下の情報を確認し、操作中にも必要に応じて参照することをお勧めします。

## <a name="supported-operating-systems"></a>サポートされているオペレーティング システム

WAImportExport ツールを使用してハード ドライブを準備するために、以下の **BitLocker ドライブ暗号化をサポートする 64 ビット OS** がサポートされます。


|プラットフォーム |Version |
|---------|---------|
|Windows     | Windows 7 Enterprise、Windows 7 Ultimate <br> Windows 8 Pro、Windows 8 Enterprise、Windows 8.1 Pro、Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012、Windows Server 2012 R2         |


## <a name="supported-storage-accounts"></a>サポートされるストレージ アカウント

Azure Import/Export サービスでは、以下の [Azure ストレージ](storage-account-options.md) アカウントをサポートします。
- General Purpose v1 ストレージ アカウント (クラシックまたは Azure Resource Manager の両方のデプロイ)
- BLOB ストレージ アカウント
- General Purpose v2 ストレージ アカウント

各ジョブを使用できるのは、1 つのストレージ アカウントとの間でのデータ転送だけです。 言い換えると、1 つのインポート/エクスポート ジョブを、複数のストレージ アカウントに対して使用することはできません。 新しいストレージ アカウントの作成については、「 [ストレージ アカウントの作成方法](storage-create-storage-account.md#create-a-storage-account)」を参照してください。

> [!IMPORTANT] 
> Azure Import Export サービスでは、[仮想ネットワークのサービス エンドポイント](../../virtual-network/virtual-network-service-endpoints-overview.md)機能が有効になっているストレージ アカウントはサポートされません。 

## <a name="supported-storage-types"></a>サポートされるストレージの種類

Azure Import/Export サービスでは、次の一覧のストレージの種類をサポートされます。


|ジョブ  |ストレージ サービス |サポートされています  |サポートされていません  |
|---------|---------|---------|---------|
|[インポート]     |  Azure BLOB ストレージ <br><br> Azure File ストレージ       | ブロック BLOB と ページ BLOB をサポート <br><br> Files をサポート          |
|エクスポート     |   Azure BLOB ストレージ       | ブロック BLOB、ページ BLOB、および追加 BLOB をサポート         | Azure Files はサポートされない


## <a name="supported-hardware"></a>サポートされるハードウェア 

Azure Import/Export サービスでは、データのコピーをサポートしているディスクが必要です。

### <a name="supported-disks"></a>サポートされるディスク

Import/Export サービスでは、次のディスクの一覧の使用がサポートされます。


|ディスクの種類  |サイズ  |サポートされています |サポートされていません  |
|---------|---------|---------|---------|
|SSD    |   2.5"      |         |         |
|HDD     |  2.5"<br>3.5"       |SATA II、SATA III         |USB アダプターが組み込まれた外部 HDD <br> 外部 HDD のケーシング内のディスク         |


1 つのインポート/エクスポート ジョブは、以下を使用できます。
- 最大 10 台の HDD/SSD。
- 任意のサイズの HDD/SSD の組み合わせ。

多数のドライブを複数のジョブに分散でき、また、作成可能なジョブの数に制限はありません。 インポート ジョブの場合は、ドライブの最初のデータ ボリュームだけが処理されます。 データ ボリュームは NTFS でフォーマットされている必要があります。

WAImportExport ツールを使用してハード ドライブの準備とデータのコピーを行うときに、外部 USB アダプターを使用できます。 市販の USB 3.0 以降のアダプターの大半が機能します。 


## <a name="next-steps"></a>次の手順

* [WAImportExport ツールを設定する](storage-import-export-tool-how-to.md)
* [AzCopy コマンド ライン ユーティリティを使ったデータの転送](storage-use-azcopy.md)
* [Azure Import Export REST API サンプル](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

