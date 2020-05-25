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
ms.openlocfilehash: 6b5aa4f409b8c2f5a9125ab01e8587f4ac9c4ee5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945149"
---
## <a name="create-a-project-zip-file"></a>プロジェクトの ZIP ファイルを作成する

>[!NOTE]
> ファイルを ZIP ファイルでダウンロードした場合は、まずファイルを抽出します。 たとえば、GitHub から ZIP ファイルをダウンロードした場合、そのファイルをそのままデプロイすることはできません。 GitHub では、入れ子になったディレクトリが追加されますが、これらは App Service と連携しません。 
>

ローカル ターミナル ウィンドウで、アプリ プロジェクトのルート ディレクトリに移動します。 

このディレクトリには、Web アプリへの入力ファイルを含める必要があります (_index.html_、 _index.php_、_app.js_ など)。 パッケージ管理ファイルを含めることもできます (_project.json_、_composer.json_、_package.json_、_bower.json_、_requirements.txt_ など)。

デプロイの自動化を App Service で自動的に実行したい場合を除き、すべてのビルド タスク (`npm`、`bower`、`gulp`、`composer`、`pip` など) を実行し、アプリの実行に必要なファイルがすべて揃っていることを確認してください。 [パッケージを直接実行](../articles/app-service/deploy-run-package.md)したい場合、この手順は必須です。

プロジェクト内のすべての ZIP アーカイブを作成します。 次のコマンドは、ターミナルの既定ツールを使用します。

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

