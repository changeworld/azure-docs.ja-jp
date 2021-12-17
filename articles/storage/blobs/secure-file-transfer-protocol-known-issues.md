---
title: Azure Blob Storage での SFTP (プレビュー) に関する既知の問題 | Microsoft Docs
description: Azure Blob Storage でのセキュア ファイル転送プロトコル (SFTP) のサポートに関する制限事項と既知の問題について説明します。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 11/15/2021
ms.author: normesta
ms.reviewer: ylunagaria
ms.openlocfilehash: c9c7a1395006aa7613aa40ff9ea7b402f69bbf5d
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557221"
---
# <a name="known-issues-with-secure-file-transfer-protocol-sftp-support-in-azure-blob-storage-preview"></a>Azure Blob Storage でのセキュア ファイル転送プロトコル (SFTP) のサポート (プレビュー) に関する既知の問題

この記事では、Azure Blob Storage での SFTP サポートに関する制限事項と既知の問題について説明します。

> [!IMPORTANT]
> SFTP のサポートは、現在プレビュー段階であり、[これらのリージョン](secure-file-transfer-protocol-support.md#regional-availability)で使用できます。
> 
> ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。
>
> プレビューに登録するには、[こちらのフォーム](https://forms.office.com/r/gZguN0j65Y)を参照してください。

## <a name="data-redundancy-options"></a>データ冗長オプション

- SFTP が有効になっているアカウントでは、geo 冗長ストレージ (GRS) と Geo ゾーン冗長ストレージ (GZRS) はされていません。

## <a name="authorization"></a>承認

- ローカル ユーザーは、SFTP エンドポイントで現在サポートされている ID 管理の唯一の形式です。

- Azure Active Directory (Azure AD)、Shared Access Signature (SAS)、およびアカウント キーの承認は、SFTP エンドポイントではサポートされていません。

- POSIX のようなアクセス制御リスト (ACL) は、SFTP エンドポイントではサポートされていません。

  > [!NOTE]
  > データが Azure Storage に取り込まれた後は、Azure のさまざまなストレージ セキュリティ設定を利用できます。 ロールベースのアクセス制御 (RBAC) やアクセス制御リストなどの承認メカニズムは、接続する側の SFTP クライアントを承認する手段としてはサポートされていませんが、これらは、Azure portal、Azure CLI、Azure PowerShell コマンド、AzCopy などの Azure ツールや、Azure SDK、Azure REST API によるアクセスを承認するために使用できます。 

- コンテナーの一覧表示、配置と取得、作成と削除などのアカウント レベルの操作はサポートされていません。
 
## <a name="networking"></a>ネットワーク

- パーティション分割された DNS エンドポイントはサポートされていません。

- SFTP を使用してストレージ アカウントにアクセスするには、ネットワークで、ポート 22 のトラフィックを許可する必要があります。

- ファイアウォールが構成されていると、許可されていない IP からの接続が、予期したとおりに拒否されません。 ただし、認証済みユーザーの成功した接続が存在する場合は、すべてのデータ プレーンの操作が拒否されます。

## <a name="supported-algorithms"></a>サポートされているアルゴリズム

| ホスト キー | キーの交換 | 暗号/暗号化 | 整合性/MAC | 公開キー |
|----------|--------------|--------------------|---------------|------------|
| rsa-sha2-256 | ecdh-sha2-nistp384 | aes128-gcm@openssh.com | hmac-sha2-256 | ssh-rsa |
| rsa-sha2-512 | ecdh-sha2-nistp256 | aes256-gcm@openssh.com | hmac-sha2-512 | ecdsa-sha2-nistp256 |
| ecdsa-sha2-nistp256 | diffie-hellman-group14-sha256 | aes128-cbc| | ecdsa-sha2-nistp384 |
| ecdsa-sha2-nistp384| diffie-hellman-group16-sha512 | aes256-cbc |  | 
||| aes192-cbc ||

## <a name="security"></a>セキュリティ

- ホスト キーは[ここ](secure-file-transfer-protocol-host-keys.md)で公開されています。 パブリック プレビューの間、ホスト キーのローテーションは最大で 1 か月に 1 回行われます。

- "リモート ホスト ID が変更されました" という警告には、いくつかの異なる理由があります。

  - リモート ホスト キーが更新された (ホスト キーは定期的にローテーションされます)。
  
  - クライアントで、ローカルの ssh ファイル "known_hosts" に格納されているものとは異なるホスト キー アルゴリズムが選択された。 OpenSSH では、ホスト (account.blob.core.windows.net) が一致すると、アルゴリズムが一致しているとは限らない場合でも、既に信頼されているキーが使用されます。
  
  - ストレージ アカウントが別のリージョンにフェールオーバーした。
  
  - リモートホスト (account.blob.core.windows.net) が偽造されている。

## <a name="integrations"></a>統合

- 変更フィードはサポートされていません。

- トランザクションや容量などのアカウント メトリックを使用できます。 操作別にログをフィルター処理して、SFTP アクティビティを表示します。

- ネットワーク ファイル システム (NFS) 3.0 と SFTP を同じストレージ アカウントで有効にすることはできません。

## <a name="performance"></a>パフォーマンス

- 一部のクライアントで、既定の設定を使用するとアップロードのパフォーマンスが低下する場合があります。 一部でこれが予期されるのは、SFTP は通信量の多いプロトコルで、小さいメッセージ要求を送信するためです。 バッファー サイズを増やし、複数のコンカレント接続を使用すると、接続の速度を大幅に向上できます。 

  - WinSCP の場合は、最大 9 個のコンカレント接続を使用して、複数のファイルをアップロードできます。 

  - Windows の OpenSSH の場合、バッファー サイズを 100000: sftp -B 100000 testaccount.user1@testaccount.blob.core.windows.net に増やせます 

  - Linux の OpenSSH の場合、バッファー サイズを 262000: sftp -B 262000 -R 32 testaccount.user1@testaccount.blob.core.windows.net に増やせます 

- アイドル状態や非アクティブである接続については、4 分のタイムアウトがあります。 OpenSSH は、ハングしたように見えてから切断されます。 一部のクライアントでは、自動的に再接続が行われます。 

- アップロードの最大ファイル サイズは、クライアント メッセージのサイズによって制限されています。 以下にいくつかの例を示します。 

  - 32 k メッセージ (OpenSSH の既定値) * 50 k ブロック = 1.52 GB 

  - 100 k メッセージ (Windows での OpenSSH の最大値) * 50 k ブロック = 4.77 GB 

  - 256 k メッセージ (Linux での OpenSSH の最大値) * 50 k ブロック = 12.20 GB 

## <a name="other"></a>その他

- シンボリック リンクはサポートされていません。

- PowerShell と Azure CLI はサポートされていません。 パブリック プレビュー用のポータルと ARM テンプレートを利用できます。

## <a name="see-also"></a>関連項目

- [Azure Blob Storage でのセキュア ファイル転送 (SFTP) プロトコルのサポート](secure-file-transfer-protocol-support.md)