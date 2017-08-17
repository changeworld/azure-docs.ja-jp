---
title: "Azure RemoteApp からユーザー データを移行する | Microsoft Docs"
description: "Azure RemoteApp を移行先または移行元とするデータの移行方法について説明します。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: d7e4fbf1-cb42-4430-94a0-ed6d4676fc86
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: ba3cf4c6834279bbd7f94d666fd8abbb7ac05bf0
ms.contentlocale: ja-jp
ms.lasthandoff: 07/22/2017

---
# <a name="how-to-migrate-data-into-and-out-of-azure-remoteapp"></a>Azure RemoteApp を移行先または移行元とするデータの移行方法
> [!IMPORTANT]
> Azure RemoteApp の提供は、2017 年 8 月 31 日で終了します。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

多様なツールおよび方法を使用して、 [ユーザー データ](remoteapp-upd.md) を Azure RemoteApp へ、または Azure RemoteApp から転送できます。 次に方法をいくつか示します。

* クリップボードの共有を使用して、コピーして貼り付ける
* ファイルとデータをファイル サーバーにコピーする
* ブラウザー経由でファイルを OneDrive for Business にコピーする
* リダイレクトを使用してファイルをコピーする

> [!NOTE]
> OneDrive for Business やコンシューマー同期エージェントを有効にすることはできません。これらは Azure RemoteApp で[サポートされていません](remoteapp-onedrive.md)。
> 
> 

## <a name="use-copy-and-paste-in-file-explorer"></a>エクスプローラーでコピー/貼り付けを使用する
クリップボードを使用したコピー/貼り付けは、RemoteApp のデプロイで[既定](remoteapp-redirection.md)で有効になります。 そのため、ユーザーはローカル PC と RemoteApp のアプリとの間でファイルをコピーできます。 多くの場合、RemoteApp でアプリを使用する標準的な過程では、ユーザーは UPD にファイルを保存します。RemoteApp からデータを移動するのは簡単です。

1. RemoteApp コレクションで[エクスプローラーをアプリとして公開発行します](remoteapp-publish.md) (これは管理タスクであることに注意してください)。
2. 公開したエクスプローラーのアプリを起動し、これを使用してファイルを UPD との間でコピー/貼り付けを行うようにユーザーに指示します。

## <a name="upload-files-and-data-to-a-file-server-by-using-standard-network-file-copy"></a>標準のネットワーク ファイルのコピーを使用して、ファイルとデータをファイル サーバーにアップロードする
組織がファイル サーバーを使用して一般的なデータを格納するのはよくあることです。 サーバー名または場所がわかっている場合、ユーザーは、ローカル ネットワークでサーバーを参照して、先ほど実行したのと同様にそこにファイルをコピーすることができます。 ここでもエクスプローラーを RemoteApp に公開し、ユーザーと共有します。

> [!NOTE]
> ファイル サーバーは、RemoteApp がデプロイされているルーティング可能なネットワークに配置する必要があります。
> 
> 

## <a name="copy-files-to-onedrive-for-business"></a>OneDrive for Business にファイルをコピーする
RemoteApp で OneDrive for Business 同期エージェントを有効にすることはできませんが、ブラウザーを使用して UPD から OneDrive for Business にファイルをコピーすることはできます。 

1. エクスプローラーを RemoteApp に公開し、このアプリを使用してファイルにアクセスするようにユーザーに伝えます。 
2. ファイルの転送は、圧縮されている場合に最も簡単になります。そのため、ユーザーは、OneDrive for Business に移動するすべてのファイルを格納した .zip ファイルを作成する必要があります。
3. ユーザーに、Office 365 ポータルに移動し、OneDrive に移動して .zip ファイルをアップロードするように依頼します。

## <a name="copy-files-by-using-drive-redirection"></a>ドライブ リダイレクトを使用してファイルをコピーする
[ドライブ リダイレクト](remoteapp-redirection.md)を有効にしている場合、ユーザー用にマップされたドライブが既に作成されています。 この場合、ユーザーはリダイレクトされたドライブ上でファイルを zip 圧縮してから、ローカル PC に保存できます。

## <a name="how-administrators-can-export-data"></a>管理者がデータをエクスポートする方法

Azure RemoteApp の管理者は、サブスクリプション内のすべてのコレクションについて、すべてのユーザー プロファイル ディスク (UPD) を、Azure PowerShell コマンドレット Export-AzureRemoteAppUserDisk を使用して、Azure Storage にエクスポートすることができます。  UPD を個別に選択する機能はありません。  この PowerShell コマンドを実行すると、各ユーザー ディスクは 50 GB の固定ディスク サイズになり、Azure Storage にエクスポートされます。  Azure Storage のコストは、このストレージに対して直ちに発生します。  このコマンドを実行するときは、セッションが 1 つもないことを確認してください。セッションがあると、エクスポートは失敗します。

ドメインに参加している Azure RemoteApp デプロイの UPD の再利用は、RDS デプロイ内でのみ可能です。ドメインに参加していないデプロイは使用できません。  RDS デプロイでこれらのディスクを使用する場合は、RDS デプロイに UPD をエクスポート、変換、インポートする[自動化スクリプト](https://github.com/arcadiahlyy/aramigration)の利用をお勧めします。


