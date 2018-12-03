---
title: Azure PowerShell のサンプル - App Service | Microsoft Docs
description: Azure PowerShell のサンプル - App Service
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 211284f4eff112b9aebec7fa1a031f292a4a92f4
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290741"
---
# <a name="azure-powershell-samples"></a>Azure PowerShell のサンプル

次の表には、Azure PowerShell を使用して構築された PowerShell スクリプトへのリンクが含まれています。

| | |
|-|-|
|**アプリの作成**||
| [GitHub からのデプロイでアプリを作成する](./scripts/app-service-powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| GitHub からコードを取得する Azure Web アプリを作成します。 |
| [GitHub からの継続的なデプロイでアプリを作成する](./scripts/app-service-powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| GitHub から継続的にコードをデプロイする Azure Web アプリを作成します。 |
| [アプリを作成し、FTP を使用してコードをデプロイする](./scripts/app-service-powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure Web アプリを作成し、FTP を使用してローカル ディレクトリからファイルをアップロードします。 |
| [アプリを作成してローカル Git リポジトリからコードをデプロイする](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure Web アプリを作成し、ローカル Git リポジトリからのコードのプッシュを構成します。 |
| [アプリを作成してステージング環境にコードをデプロイする](./scripts/app-service-powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | コードの変更をステージングするためのデプロイ スロットを持つ Azure Web アプリを作成します。 |
|**アプリケーションの構成**||
| [アプリにカスタム ドメインをマップする](./scripts/app-service-powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Web アプリを作成し、カスタム ドメイン名をマップします。 |
| [カスタム SSL 証明書をアプリにバインドする](./scripts/app-service-powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Web アプリを作成し、カスタム ドメイン名の SSL 証明書をバインドします。 |
|**アプリのスケール**||
| [アプリを手動でスケーリングする](./scripts/app-service-powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure Web アプリを作成し、2 つのインスタンス間でスケールします。 |
| [高可用性アーキテクチャを使用して世界規模でアプリをスケーリングする](./scripts/app-service-powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 2 つの異なる地理的リージョンに 2 つの Azure Web アプリを作成し、Azure Traffic Manager を使用して、1 つのエンドポイントを介して利用できるようにします。 |
|**アプリのリソースへの接続**||
| [アプリを SQL Database に接続する](./scripts/app-service-powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Web アプリと SQL データベースを作成し、データベース接続文字列をアプリケーション設定に追加します。 |
| [アプリをストレージ アカウントに接続する](./scripts/app-service-powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Web アプリとストレージ アカウントを作成し、ストレージ接続文字列をアプリケーション設定に追加します。 |
|**アプリのバックアップと復元**||
| [アプリをバックアップする](./scripts/app-service-powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure Web アプリを作成し、その Web アプリの 1 回限りのバックアップを作成します。 |
| [アプリのスケジュールされたバックアップを作成する](./scripts/app-service-powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure Web アプリを作成し、その Web アプリのスケジュールされたバックアップを作成します。 |
| [アプリのバックアップを削除する](./scripts/app-service-powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | アプリの既存のバックアップを削除します。 |
| [アプリをバックアップから復元する](./scripts/app-service-powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 以前に完了したバックアップからアプリを復元します。 |
| [複数のサブスクリプションにわたってバックアップを復元する](./scripts/app-service-powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Web アプリを他のサブスクリプション内のバックアップから復元します。 |
|**アプリの監視**||
| [Web サーバー ログを使用してアプリを監視する](./scripts/app-service-powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure Web アプリを作成し、ログ記録を有効にし、ログをローカル コンピューターにダウンロードします。 |
| | |
