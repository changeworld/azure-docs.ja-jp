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
ms.openlocfilehash: cd88dec2ad79ad9f4b4c004866060be86b777cd9
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2018
ms.locfileid: "49311186"
---
# <a name="troubleshoot-visual-studio-2017-development-with-docker"></a>Docker での Visual Studio 2017 のデプロイのトラブルシューティング

Visual Studio Tools for Docker を使用する際に、アプリケーションのビルドまたはデバッグ中に問題が発生する可能性があります。 一般的なトラブルシューティング手順のいくつかを以下に示します。

## <a name="volume-sharing-is-not-enabled-enable-volume-sharing-in-the-docker-ce-for-windows-settings--linux-containers-only"></a>ボリューム共有が有効にならない。 Docker CE for Windows の設定でのボリューム共有の有効化 (Linux コンテナーのみ)

この問題を解決するには:

1. 通知領域で **[Windows 用の Docker]** を右クリックし、**[設定]** を選択します。
1. **[Shared Drives]\(共有ドライブ\)** を選択し、システム ドライブと共に、プロジェクトが存在するドライブを共有します。

> [!NOTE]
> ファイルが共有されているように見える場合は、ダイアログ下部の [資格情報のリセット] リンクをクリックしてボリューム共有を再度有効にすることが必要な場合があります。 資格情報をリセットした後で続行するには、Visual Studio の再起動が必要な場合があります。

![共有ドライブ](./media/vs-azure-tools-docker-troubleshooting-docker-errors/shareddrives.png)

## <a name="mounts-denied"></a>マウントが拒否される

MacOS 用の Docker を使用している場合、フォルダー /usr/local/share/dotnet/sdk/NuGetFallbackFolder を参照するとエラーが発生する可能性があります。 Docker で [File Sharing]\(ファイル共有\) タブにフォルダーを追加します。

## <a name="unable-to-start-debugging"></a>デバッグを開始できない

理由の 1 つとして、ユーザー プロファイル フォルダーに古いデバッグ コンポーネントが残っていることが考えられます。 次のコマンドを実行してこれらのフォルダーを削除し、次回のデバッグ セッションで最新のデバッグ コンポーネントがダウンロードされるようにしてください。

- del %userprofile%\vsdbg
- del %userprofile%\onecoremsvsmon

## <a name="errors-specific-to-networking-when-debugging-your-application"></a>アプリケーションのデバッグ時のネットワークに固有のエラー

[コンテナー ホスト ネットワークのクリーンアップ](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/windows-server-container-tools/CleanupContainerHostNetworking)に関するページでダウンロードできるスクリプトを実行してみてください。このスクリプトにより、ホスト コンピューター上のネットワーク関連のコンポーネントが更新されます。


## <a name="microsoftdockertools-github-repo"></a>Microsoft/DockerTools GitHub リポジトリ

その他の問題が発生した場合は、[Microsoft/DockerTools](https://github.com/microsoft/dockertools/issues) の問題に関するページをご覧ください。