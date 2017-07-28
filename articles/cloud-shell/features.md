---
title: "Azure Cloud Shell (プレビュー) の機能 | Microsoft Docs"
description: "Azure Cloud Shell の機能の概要"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 2b434ccb9218a0a2731642da6125031592ea8ce6
ms.contentlocale: ja-jp
ms.lasthandoff: 06/28/2017

---

# <a name="features--tools-for-azure-cloud-shell"></a>Azure Cloud Shell の機能とツール
Azure Cloud Shell は、Azure リソースを開発および管理するための、ブラウザー ベースのシェル環境です。

Cloud Shell は、インストール、バージョン管理、およびコンピューター管理のオーバーヘッドなしで、ブラウザーでアクセスできる、事前構成済みのシェル環境を提供します。

Cloud Shell は要求ごとにコンピューターをプロビジョニングするため、セッション間ではコンピューターの状態が保持されません。 Cloud Shell は対話型セッション向けに構築されているため、シェルが 10 分間非アクティブの状態になると、シェルは自動的に終了します。

## <a name="bash-in-cloud-shell"></a>Cloud Shell の Bash
### <a name="tools"></a>ツール
|カテゴリ   |名前   |
|---|---|
|Linux シェル インタープリター|Bash<br> sh               |
|Azure ツール            |Azure CLI 2.0 および 1.0<br> AzCopy     |
|テキスト エディター           |vim<br> nano<br> emacs       |
|ソース管理         |git                    |
|ビルド ツール            |make<br> maven<br> npm<br> pip         |
|コンテナー             |Docker CLI/Docker Machine<br> Kubectl<br> DC/OS CLI         |
|データベース              |MySQL クライアント<br> PostgreSql クライアント<br> sqlcmd ユーティリティ      |
|その他                  |iPython クライアント<br> Cloud Foundry CLI<br> |

### <a name="language-support"></a>言語のサポート
|言語   |バージョン   |
|---|---|
|.NET       |1.01       |
|Go         |1.7        |
|Java       |1.8        |
|Node.js    |6.9.4      |
|Python     |2.7 および 3.5 (既定)|

## <a name="secure-automatic-authentication"></a>自動認証をセキュリティで保護する
Cloud Shell は、Azure CLI 2.0 のアカウント アクセスを安全かつ自動的に認証します。

## <a name="azure-files-persistence"></a>Azure ファイルの永続化
Cloud Shell は、一時的なマシンを使用して、要求ごとに割り当てられるため、$Home の外のローカル ファイルやマシンの状態は、セッション間で保持されません。
セッション間でファイルを保持する場合、Cloud Shell の初回起動時に、Azure ファイル共有のアタッチについてのチュートリアルがあります。
チュートリアルが完了すると、今後のすべてのセッションで、記憶域が自動的にアタッチされます。

[Azure ファイル共有を Cloud Shell にアタッチする方法の詳細について説明します。](persisting-shell-storage.md)

## <a name="next-steps"></a>次のステップ
[Cloud Shell のクイック スタート](quickstart.md) <br>
[Azure CLI 2.0 について](https://docs.microsoft.com/cli/azure/) <br>
