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
ms.openlocfilehash: 463e8b0122339831d5d6a65b6e41d4f697a82013
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38731904"
---
Cloud Shell で [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az_webapp_deployment_user_set) コマンドを使用して、デプロイ資格情報を作成します。 FTP と、Web アプリへのローカル Git のデプロイには、デプロイ ユーザーが必要です。 ユーザー名とパスワードはアカウント レベルです。 "_Azure サブスクリプションの資格情報とは異なります。_"

次の例では、(かっこも含めて) *\<username>* と *\<password>* を新しいユーザー名とパスワードで置き換えます。 ユーザー名は Azure 内で一意になっている必要があります。 パスワードは長さが 8 文字以上で、文字、数字、記号のうち 2 つを含む必要があります。 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

パスワードが `null` と表示された状態で JSON 出力が返されます。 `'Conflict'. Details: 409` エラーが発生した場合は、ユーザー名を変更します。 ` 'Bad Request'. Details: 400` エラーが発生した場合は、より強力なパスワードを使用します。

このデプロイ ユーザーの作成は 1 回だけ実行する必要があります。すべての Azure デプロイでこのユーザーを使用できます。

> [!NOTE]
> ユーザー名とパスワードを記録します。 後で Web アプリをデプロイするときに必要になります。
>
>
