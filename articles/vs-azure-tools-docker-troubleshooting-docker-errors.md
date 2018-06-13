---
title: Visual Studio を使用した Windows での Docker クライアント エラーのトラブルシューティング | Microsoft Docs
description: Visual Studio 2017 を使用して、Windows 上で Web アプリを作成し、Docker にデプロイする際に発生する問題のトラブルシューティングを行います。
services: azure-container-service
documentationcenter: na
author: devinb
manager: douge
editor: ''
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 10/13/2017
ms.author: devinb
ms.openlocfilehash: 90dd5df4a607568e2f3a60791da2948af7ce4e50
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2017
ms.locfileid: "24002932"
---
# <a name="troubleshoot-visual-studio-2017-development-with-docker"></a>Docker での Visual Studio 2017 のデプロイのトラブルシューティング

Visual Studio Tools for Docker を使用する際に、アプリケーションのビルドまたはデバッグ中に問題が発生する可能性があります。 一般的なトラブルシューティング手順のいくつかを以下に示します。

## <a name="volume-sharing-is-not-enabled-enable-volume-sharing-in-the-docker-ce-for-windows-settings--linux-containers-only"></a>ボリューム共有が有効にならない。 Docker CE for Windows の設定でのボリューム共有の有効化 (Linux コンテナーのみ)

この問題を解決するには:

1. 通知領域で **[Windows 用の Docker]** を右クリックし、**[設定]** を選択します。
1. **[Shared Drives]\(共有ドライブ\)** を選択し、システム ドライブと共に、プロジェクトが存在するドライブを共有します。

> [!NOTE]
> ファイルが共有されているように見える場合は、ダイアログ下部の [資格情報のリセット] リンクをクリックしてボリューム共有を再度有効にすることが必要な場合があります。

![共有ドライブ](./media/vs-azure-tools-docker-troubleshooting-docker-errors/shareddrives.png)

## <a name="unable-to-start-debugging"></a>デバッグを開始できない

理由の 1 つとして、ユーザー プロファイル フォルダーに古いデバッグ コンポーネントが残っていることが考えられます。 次のコマンドを実行してこれらのフォルダーを削除し、次回のデバッグ セッションで最新のデバッグ コンポーネントがダウンロードされるようにしてください。

- del %userprofile%\vsdbg
- del %userprofile%\onecoremsvsmon

## <a name="errors-specific-to-networking-when-debugging-your-application"></a>アプリケーションのデバッグ時のネットワークに固有のエラー

[コンテナー ホスト ネットワークのクリーンアップ](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/windows-server-container-tools/CleanupContainerHostNetworking)に関するページでダウンロードできるスクリプトを実行してみてください。このスクリプトにより、ホスト コンピューター上のネットワーク関連のコンポーネントが更新されます。


## <a name="microsoftdockertools-github-repo"></a>Microsoft/DockerTools GitHub リポジトリ

その他の問題が発生した場合は、[Microsoft/DockerTools](https://github.com/microsoft/dockertools/issues) の問題に関するページをご覧ください。