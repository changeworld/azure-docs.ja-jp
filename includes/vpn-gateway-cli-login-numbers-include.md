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
ms.openlocfilehash: 82de8eab089e5f666e1a2ce4eab09bfd2895185b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47020083"
---
1. [az login](/cli/azure/#login) コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。 サインインの詳細については、「[Azure CLI を使ってみる](/cli/azure/get-started-with-azure-cli)」を参照してください。

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
