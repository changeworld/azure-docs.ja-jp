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
ms.openlocfilehash: 21dbec52dded5a195cc764741ab9e8d79737c549
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121184"
---
1. [az login](/cli/azure/) コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。 サインインの詳細については、「[Azure CLI を使ってみる](/cli/azure/get-started-with-azure-cli)」を参照してください。

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
