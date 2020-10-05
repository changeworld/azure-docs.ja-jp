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
ms.openlocfilehash: 8901f54c4802766eb3f12830d98d29fb47337ae5
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2020
ms.locfileid: "90705216"
---
プレビュー段階では、NFS には次の制限事項があります。

- 現在、NFS 4.1 では、[プロトコル仕様](https://tools.ietf.org/html/rfc5661)の必須機能のみがサポートされています。 あらゆる種類の委任とコールバック、ロックのアップグレードとダウングレード、Kerberos 認証と暗号化など、オプション機能はサポートされていません。
- 要求の大部分がメタデータ中心の場合、読み取り/書き込み/更新操作と比較すると、待機時間が長くなります。
- NFS 共有を作成するには、新しいストレージ アカウントを作成する必要があります。
- 管理プレーン REST API のみがサポートされています。 データ プレーン REST API は使用できません。したがって、Storage Explorer などのツールは NFS 共有で動作せず、Azure portal で NFS 共有データを参照できません。
- Premium レベルでのみ使用できます。
- 現在、ローカル冗長ストレージでのみ使用できます。

### <a name="azure-storage-features-not-yet-supported"></a>まだサポートされていない Azure Storage の機能

また、NFS 共有では、次の Azure Files 機能は使用できません。

- ID ベースの認証
- Azure Backup のサポート
- スナップショット
- 論理的な削除
- 完全な転送中の暗号化のサポート (詳細については、[NFS セキュリティ](../articles/storage/files/storage-files-compare-protocols.md#security)に関するページを参照してください)
- Azure File Sync (NFS 4.1 でサポートされていない Windows クライアントでのみ使用できます)