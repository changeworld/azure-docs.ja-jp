---
title: Azure File Sync エージェントをデプロイする
description: Azure File Sync エージェントのデプロイ。 移行ドキュメント間で共有される一般的なテキスト ブロック。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 66388f14949b4f398de18c9162ca453e7fb3cbe1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143587"
---
このセクションでは、Azure File Sync エージェントをご利用の Windows Server インスタンスにインストールします。

[デプロイ ガイド](../articles/storage/files/storage-sync-files-deployment-guide.md)では、 **[Internet Explorer セキュリティ強化の構成]** を無効にする必要があることが示されています。 このセキュリティ対策は、Azure File Sync には適用されません。この機能を無効にすると、問題なく Azure を認証できるようになります。

PowerShell を開き、次のコマンドを使用して必須の PowerShell モジュールをインストールします。 プロンプトが表示されたら、完全なモジュールと NuGet プロバイダーを必ずインストールしてください。

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

サーバーからインターネットへの接続で問題がある場合は、ここで解決する必要があります。 Azure File Sync は、インターネットへの任意の使用可能なネットワーク接続を使用します。 インターネットに接続するためにプロキシ サーバーを要求することもサポートされています。 今すぐにマシン全体のプロキシを構成することも、エージェントのインストール中に Azure File Sync だけが使用するプロキシを指定することもできます。

プロキシを構成することが、このサーバーに対してファイアウォールを開く必要があることを意味する場合は、その方法を受け入れることができます。 サーバー インストールの終了時、サーバー登録が完了した後に、選択したリージョン用に Azure File Sync が通信する必要がある Azure 内の正確なエンドポイント URL を示すネットワーク接続レポートが示されます。 このレポートには、通信が必要な理由も示されます。 このレポートを使用して、このサーバーのファイアウォールを特定の URL にロック ダウンできます。

また、ファイアウォール全体を開かない、より保守的なアプローチに従うこともできますが、サーバーは上位レベルの DNS 名前空間との通信に制限されます。 詳細については、「[Azure File Sync のプロキシとファイアウォールの設定](../articles/storage/files/storage-sync-files-firewall-and-proxy.md)」をご覧ください。 ご自分のネットワークのベスト プラクティスに従ってください。

サーバーの "*インストール*" ウィザードの終了時に、サーバーの "*登録*" ウィザードが開きます。 以前からご利用のストレージ同期サービスの Azure リソースにサーバーを登録します。

最初にインストールする必要がある PowerShell モジュールを含め、これらの手順については、デプロイ ガイドで詳しく説明されています: [Azure File Sync エージェントのインストール](../articles/storage/files/storage-sync-files-deployment-guide.md)。

最新のエージェントを使用してください。 Microsoft ダウンロード センターからダウンロードできます。[Azure File Sync エージェント](https://aka.ms/AFS/agent "Azure File Sync エージェントのダウンロード")。

インストールとサーバーの登録が正常に完了したら、この手順が正常に完了したことを確認できます。 Azure portal でストレージ同期サービス リソースに移動し、左側のメニューの **[登録済みサーバー]** に進みます。 ご利用のサーバーがそこに一覧表示されます。
