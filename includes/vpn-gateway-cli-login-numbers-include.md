---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 70a9e2a8f6327c0af92698b49d5b622bebba3661
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313610"
---
1. [az login](/cli/azure/#login) コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。 サインインの詳細については、「[Azure CLI 2.0 を使ってみる](/cli/azure/get-started-with-azure-cli)」を参照してください。

  ```azurecli
  az login
  ```
2. 複数の Azure サブスクリプションを所有している場合は、アカウントのサブスクリプションが一覧表示されます。

  ```azurecli
  az account list --all
  ```
3. 使用するサブスクリプションを指定します。

  ```azurecli
  az account set --subscription <replace_with_your_subscription_id>
  ```