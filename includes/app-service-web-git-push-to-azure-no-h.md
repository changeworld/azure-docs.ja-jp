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
ms.openlocfilehash: 125561d61afe0fb7f704144efa1c8c20ecf03db1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
ms.locfileid: "29821047"
---
"_ローカル ターミナル ウィンドウ_" で、ローカル Git リポジトリに Azure リモートを追加します。 _&lt;deploymentLocalGitUrl-from-create-step>_ を、[Web アプリの作成](#create)に関するセクションで保存した Git リモートの URL に置き換えます。

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

アプリをデプロイするために、次のコマンドで Azure リモートにプッシュします。 Git Credential Manager によって資格情報の入力を求めるメッセージが表示されたら、Azure Portal へのログインに使用する資格情報ではなく、[デプロイ ユーザーの構成](#configure-a-deployment-user)に関するセクションで作成した資格情報を入力してください。

```bash
git push azure master
```

このコマンドの実行には、数分かかる場合があります。 実行中、次の例のような情報が表示されます。
