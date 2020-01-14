---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: e5ba08da5c58ff486bc26f2c771dfef55452629a
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2020
ms.locfileid: "75681045"
---
ローカル ターミナル ウィンドウで、ローカル Git リポジトリに Azure リモートを追加します。 *\<deploymentLocalGitUrl-from-create-step>* を、[Web アプリの作成](#create-a-web-app)に関するセクションで保存した Git リモートの URL に置き換えます。

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

アプリをデプロイするために、次のコマンドで Azure リモートにプッシュします。 Git Credential Manager によって資格情報の入力を求めるメッセージが表示されたら、Azure portal へのサインインに使用する資格情報ではなく、「[デプロイ ユーザーを構成する](/azure/app-service/containers/tutorial-python-postgresql-app#configure-a-deployment-user)」で作成した資格情報を入力してください。

```bash
git push azure master
```

このコマンドの実行には、数分かかる場合があります。 実行中、次の例のような情報が表示されます。
