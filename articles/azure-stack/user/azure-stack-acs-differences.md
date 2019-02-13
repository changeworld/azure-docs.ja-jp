---
title: Azure Stack ストレージの違いと考慮事項 | Microsoft Docs
description: Azure Stack のデプロイに関する考慮事項と一緒に、Azure Stack ストレージと Azure ストレージの相違点について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/30/2019
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.lastreviewed: 01/30/2019
ms.openlocfilehash: 11736b978242416bcfb95d3025975028e4148e98
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55486540"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Azure Stack ストレージ:相違点と考慮事項

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

Azure Stack ストレージは、Microsoft Azure Stack 内のストレージ クラウド サービスのセットです。 Azure Stack ストレージでは、Azure と整合性のあるセマンティクスによって BLOB、テーブル、キュー、アカウント管理機能が提供されます。

この記事では、Azure Stack Storage サービスと Azure Storage サービスとの間で確認されている相違点をまとめています。 Azure Stack をデプロイするときに考慮すべき事柄も取り上げています。 グローバル Azure と Azure Stack との違いの概要については、[主な考慮事項](azure-stack-considerations.md)の記事をご覧ください。

## <a name="cheat-sheet-storage-differences"></a>チート シート:ストレージの相違点

| 機能 | Azure (グローバル) | Azure Stack |
| --- | --- | --- |
|File Storage|クラウド ベースの SMB ファイル共有のサポート|まだサポートされていません
|Azure Storage Service Encryption for Data at Rest|256 ビット AES 暗号化。 Key Vault でユーザーが管理するキーを使用した暗号化をサポートしています。|BitLocker 128 ビット AES 暗号化。 ユーザーが管理するキーを使用した暗号化はサポートされていません。
|ストレージ アカウントの種類|汎用 V1、V2、および Blob Storage アカウント|汎用 V1 のみ。
|レプリケーション オプション|ローカル冗長ストレージ、geo 冗長ストレージ、読み取りアクセス geo 冗長ストレージ、およびゾーン冗長ストレージ|ローカル冗長ストレージ。
|Premium Storage|完全にサポートされます|プロビジョニング可能ですがパフォーマンス制限や保証がありません。
|マネージド ディスク|Premium および標準がサポートされます|使用バージョンが 1808 以降の場合にサポートされます。
|BLOB 名|1,024 文字 (2,048 バイト)|880 文字 (1,760 バイト)
|ブロック BLOB の最大サイズ|4.75 TB (100 MB X 50,000 ブロック)|1802 update 以降のバージョンでは、4.75 TB (100 MB x 50,000 ブロック)。 それより前のバージョンでは 50,000 X 4 MB (約 195 GB)。
|ページ BLOB のスナップショット コピー|実行中の VM にアタッチされている Azure の管理対象外 VM ディスクのバックアップはサポートされています|まだサポートされていません。
|ページ BLOB の増分スナップショットのコピー|Premium および標準の Azure ページ BLOB がサポートされます|まだサポートされていません。
|Blob Storage のストレージ層|ホット ストレージ層、クール ストレージ層、アーカイブ ストレージ層。|まだサポートされていません。
|Blob Storage の論理的な削除|一般提供|まだサポートされていません。
|ページ BLOB の最大サイズ|8 TB|1 TB (テラバイト)
|ページ BLOB のページ サイズ|512 バイト|4 KB
|テーブルのパーティション キーと行キーのサイズ|1,024 文字 (2,048 バイト)|400 文字 (800 バイト)
|BLOB スナップショット|1 つの BLOB の最大スナップショット数は制限されていません。|1 つの BLOB の最大スナップショット数は 1,000 です。
|ストレージの Azure AD Authentication|プレビュー段階|まだサポートされていません。
|不変 BLOB|一般提供|まだサポートされていません。
|ストレージのファイアウォールおよび仮想ネットワーク規則|一般提供|まだサポートされていません。|

ストレージ メトリックにも相違点があります。

* ストレージ メトリックのトランザクション データでは、内部と外部のネットワーク帯域幅が区別されません。
* ストレージ メトリックのトランザクション データには、マウントされたディスクへの仮想マシンのアクセスは含まれません。

## <a name="api-version"></a>API バージョン

Azure Stack Storage では以下のバージョンがサポートされます。

Azure Storage サービスの API:

1811 update 以降のバージョン:

- [2017-11-09](https://docs.microsoft.com/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](https://docs.microsoft.com/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

以前のバージョン:

- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Azure Storage サービスの Management API:

1811 update 以降のバージョン:

- [2017-10-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2017-06-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-12-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-05-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

以前のバージョン:

- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

Azure Stack でサポートされるストレージ クライアント ライブラリの詳細については、「[Azure Stack ストレージの開発ツールの概要](azure-stack-storage-dev.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* [Azure Stack Storage の開発ツールの概要](azure-stack-storage-dev.md)
* [Azure Stack ストレージのデータ転送ツールの使用](azure-stack-storage-transfer.md)
* [Azure Stack Storage の概要](azure-stack-storage-overview.md)
