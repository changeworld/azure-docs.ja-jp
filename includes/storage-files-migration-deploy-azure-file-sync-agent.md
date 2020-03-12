---
title: Azure File Sync エージェントのデプロイ
description: Azure File Sync エージェントのデプロイ。 移行ドキュメント間で共有する共通のテキスト ブロック。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 694becc49667204ef2071a140bb6330285089039
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209278"
---
このセクションでは、Azure File Sync エージェントを Windows Server にインストールします。
[デプロイ ガイド](../articles/storage/files/storage-sync-files-deployment-guide.md)では、**IE のセキュリティ強化**を無効にする必要があることが説明されています。 IE セキュリティ強化は、Azure File Sync では適用されないセキュリティ対策であり、無効にすると、問題なく Azure に対して認証できるようになります。

PowerShell を開き、次のコマンドを使用して必要な PowerShell モジュールをインストールします。 プロンプトが表示されたら、完全なモジュールと NuGet プロバイダーをインストールしてください。

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

サーバーからインターネットへの接続で問題がある場合は、ここで解決する必要があります。 Azure File Sync は、インターネットへの任意の使用可能なネットワーク接続を使用します。
インターネットに接続するためにプロキシ サーバーを要求することもサポートされています。 今すぐにマシン全体のプロキシを構成するか、エージェントのインストール中に、File Sync だけが使用するプロキシを指定できます。

そのために、このサーバーに対してファイアウォールを開く必要がある場合は、その方法を受け入れることができます。 サーバー インストールの終了時、サーバー登録が完了した後に、選択したリージョン用に File Sync が通信する必要がある Azure 内の正確なエンドポイント URL を示すネットワーク接続レポートが作成されます。 このレポートには、通信が必要な理由も示されます。 このレポートを使用して、このサーバーのファイアウォールを特定の URL にロックダウンできます。

また、ファイアウォールを広く開くのではなく、より高いレベルの DNS 名前空間と通信するようにサーバーを制限するという、より保守的な方法を取ることもできます。詳細については、「[Azure File Sync のプロキシとファイアウォールの設定](../articles/storage/files/storage-sync-files-firewall-and-proxy.md)」という記事を参照してください。 ご自分のネットワークのベスト プラクティスに従ってください。

サーバー "*インストール*" ウィザードの終了時に、サーバー "*登録*" ウィザードがポップアップ表示されます。
以前からのストレージ同期サービスの Azure リソースにサーバーを登録します。

最初にインストールする必要がある上記の PowerShell モジュールを含め、これらの手順については、デプロイ ガイドの [Azure File Sync エージェントのインストール](../articles/storage/files/storage-sync-files-deployment-guide.md)に関するセクションで詳しく説明されています。

最新のエージェントを使用する必要があります。これは、Microsoft ダウンロード センター ([Azure File Sync - エージェント](https://aka.ms/AFS/agent "Azure File Sync エージェントのダウンロード")) からダウンロードできます。

インストールとサーバーの登録が正常に完了したら、次のようにして、この手順が正常に完了したことを確認できます。Azure portal でストレージ同期サービス リソースに移動し、左側のメニューに従って「登録済みサーバー」を表示します。 サーバーがそこにすぐに一覧表示されます。
