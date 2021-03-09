---
title: インクルード ファイル
description: インクルード ファイル
services: azure-netapp-files
author: b-juche
ms.service: azure-netapp-files
ms.topic: include
ms.date: 09/10/2019
ms.author: b-juche
ms.custom: include file
ms.openlocfilehash: e78be737ef8d191cddc2da0f738250ca04cae9a1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "91672357"
---
1. Azure NetApp Files に対して承認されているサブスクリプションを指定します。
    
    ```azurecli-interactive
    az account set --subscription <subscriptionId>
    ```

1. Azure リソース プロバイダーを登録します。 
    
    ```azurecli-interactive
    az provider register --namespace Microsoft.NetApp --wait  
    ```