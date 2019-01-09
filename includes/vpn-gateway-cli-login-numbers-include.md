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
ms.openlocfilehash: cf3bbb6c45061d6f4885839fbfb7f069264fe986
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444214"
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
