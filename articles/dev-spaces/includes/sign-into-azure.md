---
title: インクルード ファイル
description: インクルード ファイル
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 875dd9d768b5f40f2d4ed3fad5b14a6cd6f6f6ba
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129309"
---
### <a name="sign-in-to-azure-cli"></a>Azure CLI へのサインイン
Azure にサインインします。 ターミナル ウィンドウで次のコマンドを入力します。

```cmd
az login
```

> [!Note]
> Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free)を作成できます。

#### <a name="if-you-have-multiple-azure-subscriptions"></a>複数の Azure サブスクリプションがある場合:
以下を実行して、サブスクリプションを表示できます。 

```cmd
az account list
```
JSON 出力に `isDefault: true` が含まれているサブスクリプションを見つけます。
それが使用したいサブスクリプションでない場合は、既定のサブスクリプションを変更できます。

```cmd
az account set --subscription <subscription ID>
```
