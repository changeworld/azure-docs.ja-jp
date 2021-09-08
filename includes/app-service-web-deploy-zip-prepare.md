---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/14/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 54a29bb1cc92347d9a2578a4f0ec9febb22553ac
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225473"
---
## <a name="create-a-project-zip-package"></a>プロジェクトの ZIP パッケージを作成する

>[!NOTE]
> ファイルを ZIP パッケージでダウンロードした場合は、まずファイルを抽出します。 たとえば、GitHub から ZIP パッケージをダウンロードした場合、そのファイルをそのままデプロイすることはできません。 GitHub では、入れ子になったディレクトリが追加されますが、これらは App Service と連携しません。 
>

ローカル ターミナル ウィンドウで、アプリ プロジェクトのルート ディレクトリに移動します。 

このディレクトリには、Web アプリへの入力ファイルを含める必要があります (_index.html_、 _index.php_、_app.js_ など)。 パッケージ管理ファイルを含めることもできます (_project.json_、_composer.json_、_package.json_、_bower.json_、_requirements.txt_ など)。

デプロイの自動化を App Service で自動的に実行したい場合を除き、すべてのビルド タスク (`npm`、`bower`、`gulp`、`composer`、`pip` など) を実行し、アプリの実行に必要なファイルがすべて揃っていることを確認してください。 [パッケージを直接実行](../articles/app-service/deploy-run-package.md)したい場合、この手順は必須です。

プロジェクト内のすべての ZIP アーカイブを作成します。 `dotnet` プロジェクトの場合、このフォルダーは `dotnet publish` コマンドの出力フォルダーです。 次のコマンドには、お使いの端末の既定のツールを使用します。

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

