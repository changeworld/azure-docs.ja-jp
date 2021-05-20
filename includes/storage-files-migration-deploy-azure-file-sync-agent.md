---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 544bbaa358f0ca158c757524406ffd046ad43af7
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2021
ms.locfileid: "109644731"
---
このセクションでは、Azure File Sync エージェントをご利用の Windows Server インスタンスにインストールします。

[デプロイ ガイド](../articles/storage/file-sync/file-sync-deployment-guide.md)では、 **[Internet Explorer セキュリティ強化の構成]** を無効にする必要があることが説明されています。 このセキュリティ対策は、Azure File Sync には該当しません。これをオフにすると、Azure への認証が問題なく行えるようになります。

PowerShell を開きます。 次のコマンドを使用して必須の PowerShell モジュールをインストールします。 プロンプトが表示されたら、完全なモジュールと NuGet プロバイダーを必ずインストールしてください。

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

サーバーからインターネットへの接続で問題が発生した場合は、この段階で解決する必要があります。 Azure File Sync は、インターネットへの任意の使用可能なネットワーク接続を使用します。 インターネットに接続するためにプロキシ サーバーを要求することもサポートされています。 今すぐにマシン全体のプロキシを構成することも、エージェントのインストール中に Azure File Sync だけが使用するプロキシを指定することもできます。

プロキシを構成することが、このサーバーに対してファイアウォールを開く必要があることを意味する場合は、その方法を採用しても問題ありません。 サーバー インストールの終了時、サーバー登録が完了した後に、選択したリージョン用に Azure File Sync が通信する必要がある Azure 内の正確なエンドポイント URL を示すネットワーク接続レポートが示されます。 このレポートには、なぜ通信が必要なのかも示されています。 このレポートを使用して、このサーバーのファイアウォールを特定の URL にロック ダウンできます。

また、ファイアウォール全体を開かない、より保守的なアプローチを採用することもできます。 代わりに、上位レベルの DNS 名前空間と通信するようサーバーを制限できます。 詳細については、「[Azure File Sync のプロキシとファイアウォールの設定](../articles/storage/file-sync/file-sync-firewall-and-proxy.md)」をご覧ください。 ご自分のネットワークのベスト プラクティスに従ってください。

サーバーのインストール ウィザードの終了時に、サーバーの登録ウィザードが開きます。 以前からご利用のストレージ同期サービスの Azure リソースにサーバーを登録します。

最初にインストールする必要がある PowerShell モジュールを含め、これらの手順については、デプロイ ガイドで詳しく説明されています。[Azure File Sync エージェントのインストール](../articles/storage/file-sync/file-sync-deployment-guide.md)。

最新のエージェントを使用してください。 Microsoft ダウンロード センターからダウンロードできます。[Azure File Sync エージェント](https://aka.ms/AFS/agent "Azure File Sync エージェントのダウンロード")。

インストールとサーバーの登録が正常に完了したら、この手順が正常に完了したことを確認できます。 Azure portal のストレージ同期サービス リソースに移動します。 左側のメニューで、 **[登録済みサーバー]** に移動します。 ご利用のサーバーがそこに一覧表示されます。
