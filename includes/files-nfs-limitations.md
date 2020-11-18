---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 10177dd949ac531027e13cf633b11c16674fd4ab
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2020
ms.locfileid: "94386467"
---
プレビュー段階では、NFS には次の制限事項があります。

- 現在、NFS 4.1 では、[プロトコル仕様](https://tools.ietf.org/html/rfc5661)の必須機能のみがサポートされています。 あらゆる種類の委任とコールバック、ロックのアップグレードとダウングレード、Kerberos 認証と暗号化など、オプション機能はサポートされていません。
- 要求の大部分がメタデータ中心の場合、読み取り/書き込み/更新操作と比較すると、待機時間が長くなります。
- NFS 共有を作成するには、新しいストレージ アカウントを作成する必要があります。
- 管理プレーン REST API のみがサポートされています。 データ プレーン REST API は使用できません。したがって、Storage Explorer などのツールが NFS 共有で動作しなくなり、Azure portal では NFS 共有データを参照できなくなります。
- Premium レベルでのみ使用できます。
- 現在、ローカル冗長ストレージ (LRS) でのみ使用できます。

### <a name="azure-storage-features-not-yet-supported"></a>まだサポートされていない Azure Storage の機能

また、NFS 共有では、次の Azure Files 機能は使用できません。

- ID ベースの認証
- Azure Backup のサポート
- スナップショット
- 論理的な削除
- 完全な転送中の暗号化のサポート (詳細については、[NFS セキュリティ](../articles/storage/files/storage-files-compare-protocols.md#security)に関するページを参照してください)
- Azure File Sync (NFS 4.1 でサポートされていない Windows クライアントでのみ使用できます)
