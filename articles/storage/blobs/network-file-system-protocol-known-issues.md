---
title: Azure Blob Storage の NFS 3.0 に関する既知の問題
description: Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポートに関する制限事項と既知の問題について学習します。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: normesta
ms.reviewer: yzheng
ms.openlocfilehash: 13808d38a4ddb7e00af64b02df390eee1e1f9469
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131421971"
---
# <a name="known-issues-with-network-file-system-nfs-30-protocol-support-in-azure-blob-storage"></a>Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポートにおける既知の問題

この記事では、Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポートに関する制限事項と既知の問題について説明します。

> [!IMPORTANT]
> NFS 3.0 を使用するには、お使いのアカウントの階層型名前空間機能を有効にする必要があるため、「[Azure Data Lake Storage Gen2 に関する既知の問題](data-lake-storage-known-issues.md)」で説明されている既知の問題もすべて、ご自身のアカウントに適用されます。

## <a name="nfs-30-support"></a>NFS 3.0 のサポート

- 既存のストレージ アカウントで NFS 3.0 のサポートを有効にすることはできません。

- ストレージ アカウントで NFS 3.0 のサポートを一度有効にすると、無効に戻すことはできません。

- NFS 3.0 のストレージ アカウントを作成するときの冗長性オプションとして、GRS、GZRS、RA-GRS はサポートされません。

## <a name="nfs-30-features"></a>NFS 3.0 の機能

次の NFS 3.0 の機能はまだサポートされていません。

- UDP 経由の NFS 3.0。 TCP 経由の NFS 3.0 のみがサポートされています。

- ネットワーク ロック マネージャー (NLM) を使用したファイルのロック。 mount コマンドに `-o nolock` パラメーターを含める必要があります。

- サブディレクトリのマウント。 ルート ディレクトリ (コンテナー) のみをマウントできます。

- マウントの一覧表示 (例: `showmount -a` コマンドを使用)

- エクスポートの一覧表示 (例: `showmount -e` コマンドを使用)

- ハード リンク

- コンテナーを読み取り専用としてエクスポートする

## <a name="nfs-30-clients"></a>NFS 3.0 クライアント

NFS 用の Windows クライアントはまだサポートされていません

## <a name="blob-storage-features"></a>Blob Storage の機能

NFS 3.0 プロトコルのサポートを有効にすると、一部の Blob Storage 機能は完全にサポートされますが、機能によっては、プレビュー レベルでのみサポートされる機能や、現時点ではまだまったくサポートされていないものもあります。

NFS 3.0 のサポートが有効になっているアカウントでの各 Blob Storage 機能のサポート状況を確認するには、「[Azure ストレージ アカウントにおける Blob Storage 機能のサポート](storage-feature-support-in-storage-accounts.md)」を参照してください。

## <a name="see-also"></a>関連項目

- [Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポート](network-file-system-protocol-support.md)
- [ネットワーク ファイル システム (NFS) 3.0 プロトコルを使用して Blob Storage をマウントする](network-file-system-protocol-support-how-to.md)
