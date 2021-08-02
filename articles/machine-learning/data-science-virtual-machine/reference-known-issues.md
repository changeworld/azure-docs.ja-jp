---
title: 'リファレンス: 既知の問題とトラブルシューティング'
titleSuffix: Azure Data Science Virtual  Machine
description: Azure Data Science Virtual Machine に関する既知の問題、回避策、トラブルシューティングの一覧を示します
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: reference
ms.date: 05/27/2021
ms.openlocfilehash: 8e3393f236152e591b054ee2d7c4c2753c2ff670
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2021
ms.locfileid: "111408751"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine の既知の問題とトラブルシューティング

この記事は、Azure Data Science Virtual Machine の使用時に発生する可能性のあるエラーや障害を見つけて修正するのに役立ちます。

## <a name="prompted-for-password-when-running-sudo-command-ubuntu"></a>sudo コマンドを実行するときにパスワードの入力を求められる (Ubuntu)

Ubuntu マシンで `sudo` コマンドを実行するときに、実際にログインするユーザーであることを確認するために、パスワードの再入力を求められる場合があります。 これは想定されている動作であり、Ubuntu などの Linux システムでは既定です。
ただし、一部のシナリオでは認証を繰り返す必要がなく、煩わしい場合があります。

ほとんどの場合、再認証を無効にするには、ターミナルで次のコマンドを実行します。

`echo -e "\n$USER ALL=(ALL) NOPASSWD: ALL\n" | sudo tee -a /etc/sudoers`

ターミナルを再起動した後、sudo によって別のログインが要求されることはなく、セッション ログインからの認証で十分であると見なされます。

## <a name="accessing-sql-server-windows"></a>SQL Server にアクセスする (Windows)

プレインストールされている SQL Server インスタンスに接続しようとすると、"ログインに失敗しました" というエラーが発生することがあります。 SQL Server インスタンスに正常に接続するには、接続対象のプログラム ( SQL Server Management Studio (SSMS) など) を管理者モードで実行する必要があります。 DSVM の既定では、管理者だけが接続を許可されるため、管理者モードである必要があります。

## <a name="python-package-installation-issues"></a>Python パッケージのインストールに関する問題

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>pip を使用してパッケージをインストールすると、Linux 上の依存関係が壊れる

パッケージをインストールする際、`pip install` ではなく、`sudo pip install` を使用してください。

## <a name="disk-encryption-issues"></a>ディスクの暗号化の問題

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Ubuntu DSVM でディスクの暗号化が失敗する

Azure Disk Encryption (ADE) は、現在 Ubuntu DSVM ではサポートされていません。 回避策として、[Azure Managed Disks のサーバー側暗号化](../../virtual-machines/disk-encryption.md)を構成することを検討してください。

## <a name="tool-appears-disabled"></a>ツールが無効に見える

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V が Windows DSVM で動作しない

Hyper-V が最初は Windows で正しく動作しないのは、想定されている動作です。 ブート パフォーマンスのために、一部のサービスを無効にしました。 Hyper-V を有効化するには、次の手順に従います。

1. Windows DSVM で検索バーを開きます
1. 「サービス」と入力します
1. すべての Hyper-V サービスを "手動" に設定します
1. "Hyper-V 仮想マシンの管理" を "自動" に設定します

最後の画面は、次のようになります。

   ![Hyper-V の有効化](./media/workaround/hyperv-enable-dsvm.png)