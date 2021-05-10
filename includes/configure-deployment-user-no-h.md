---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8f1fe6ae38c708c5205f8c1230da05457d6b7010
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764309"
---
FTP およびローカルの Git では、"*デプロイ ユーザー*" を使用して Azure Web アプリにデプロイできます。 デプロイ ユーザーを構成すると、すべての Azure デプロイでこのユーザーを使用できます。 アカウントレベルのデプロイのユーザー名とパスワードは、Azure サブスクリプションの資格情報とは異なります。 

デプロイ ユーザーを構成するには、Azure Cloud Shell で [az webapp deployment user set](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set) コマンドを実行します。 \<username> と \<password> を、デプロイ ユーザーのユーザー名とパスワードで置き換えます。 

- ユーザー名は、Azure 内で一意である必要があります。ローカル Git プッシュの場合、"\@" 記号を含めることはできません。 
- パスワードは長さが 8 文字以上で、文字、数字、記号のうち 2 つを含む必要があります。 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON 出力には、パスワードが `null` として表示されます。 `'Conflict'. Details: 409` エラーが発生した場合は、ユーザー名を変更します。 `'Bad Request'. Details: 400` エラーが発生した場合は、より強力なパスワードを使用します。 

Web アプリのデプロイに使用するユーザー名とパスワードを記録します。
