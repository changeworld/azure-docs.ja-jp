---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devex-tag-azurecli
ms.openlocfilehash: 1a22c63eb01abc3775b2bf299d8464323e69c372
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386614"
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
