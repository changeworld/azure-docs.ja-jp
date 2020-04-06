---
title: Azure Dev Spaces でのコードのリモート デバッグのしくみ
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure Dev Spaces による Azure Kubernetes Service でのリモート デバッグのプロセスについて説明します
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
ms.openlocfilehash: a9f303ea6f954285a319be137a594f7ce127f740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241245"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Azure Dev Spaces でのコードのリモート デバッグのしくみ

Azure Dev Spaces には、Kubernetes アプリケーションを迅速に反復処理してデバッグし、Azure Kubernetes Service (AKS) クラスターでチームと共同作業を行うための複数の方法が用意されています。 開発スペースでプロジェクトを実行すると、Azure Dev Spaces により AKS で実行中のアプリケーションをアタッチしてデバッグする手段が提供されます。

この記事では、Dev Spaces でのリモート デバッグのしくみについて説明します。

## <a name="debug-your-code"></a>コードをデバッグする

Java、.NET Core、および Node.js アプリケーションの場合、Visual Studio Code または Visual Studio を使用して、実行中のアプリケーションを開発スペースで直接デバッグできます。 Visual Studio Code と Visual Studio には、開発スペースに接続してアプリケーションを起動し、デバッガーをアタッチするためのツールが用意されています。 `azds prep` の実行後、Visual Studio Code または Visual Studio でプロジェクトを開くことができます。 Visual Studio Code または Visual Studio では、`azds prep` の実行とは別の接続用の独自の構成ファイルが生成されます。 Visual Studio Code または Visual Studio 内から、ブレークポイントを設定し、開発スペースに対してアプリケーションを起動できます。

![コードのデバッグ](media/get-started-node/debug-configuration-nodejs2.png)

Visual Studio Code または Visual Studio を使用してデバッグ用にアプリケーションを起動すると、`azds up` の実行と同じ方法で起動および開発スペースへの接続が処理されます。 また、Visual Studio Code および Visual Studio のクライアント側ツールには、デバッグ用の特定の情報を含む追加のパラメーターが用意されています。 このパラメーターには、デバッガー イメージの名前、デバッガー イメージ内のデバッガーの場所、およびアプリケーションのコンテナー内でのデバッガー フォルダーのマウント先が含まれています。

デバッガー イメージは、クライアント側ツールによって自動的に指定されます。 `azds prep` の実行時に Dockerfile と Helm チャートの生成に使用したのと同じ方法が使用されます。 アプリケーションのイメージにマウントされたデバッガーは `azds exec` を使用して実行されます。

## <a name="next-steps"></a>次のステップ

プロジェクトのリモート デバッグ用に Azure Dev Spaces の使用を開始するには、以下のクイック スタートを参照してください。

* [Visual Studio Code と Java を使用した迅速な反復処理とデバッグ][quickstart-java]
* [Visual Studio Code と .NET を使用した迅速な反復処理とデバッグ][quickstart-netcore]
* [Visual Studio Code と Node.js を使用した迅速な反復処理とデバッグ][quickstart-node]
* [Visual Studio と .NET Core を使用した迅速な反復処理とデバッグ][quickstart-vs]


[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
