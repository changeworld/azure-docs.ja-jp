---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/04/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6e819e1078ac90ef16070702e7961122b06c1d6f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2021
ms.locfileid: "107879491"
---
プレビュー段階では、NFS には次の制限事項があります。

- 現在、NFS 4.1 では、[プロトコル仕様](https://tools.ietf.org/html/rfc5661)のほとんどの機能がサポートされています。 あらゆる種類の委任とコールバック、ロックのアップグレードとダウングレード、Kerberos 認証と暗号化など、一部の機能はサポートされていません。
- 要求の大部分がメタデータ中心の場合、読み取り/書き込み/更新操作と比較すると、待機時間が長くなります。
- NFS 共有を有効化/作成できるのは新しいストレージ アカウント上だけです。既存のものではできません。
- 管理プレーン REST API のみがサポートされています。 データ プレーン REST API は使用できません。したがって、Storage Explorer などのツールが NFS 共有で動作しなくなり、Azure portal では NFS 共有データを参照できなくなります。
- AzCopy は現在サポートされていません。
- Premium レベルでのみ使用できます。
- NFS 共有は、数値 UID/GID のみを受け入れます。 クライアントが英数字の UID/GID を送信しないようにするには、ID マッピングを無効にする必要があります。
- プライベート リンクを使用する場合は、個々の VM の 1 つのストレージ アカウントからのみ共有をマウントできます。 他のストレージ アカウントから共有をマウントしようとすると失敗します。
- プライマリ グループに割り当てられたアクセス許可に依存することが最善です。 場合によっては、ユーザーの非プライマリ グループに割り当てられたアクセス許可が原因で、既知のバグによってアクセスが拒否されることがあります。

### <a name="azure-storage-features-not-yet-supported"></a>まだサポートされていない Azure Storage の機能

また、NFS 共有では、次の Azure Files 機能は使用できません。

- ID ベースの認証
- Azure Backup のサポート
- スナップショット
- 論理的な削除
- 完全な転送中の暗号化のサポート (詳細については、[NFS セキュリティ](../articles/storage/files/storage-files-compare-protocols.md#security)に関するページを参照してください)
- Azure File Sync (NFS 4.1 でサポートされていない Windows クライアントでのみ使用できます)
