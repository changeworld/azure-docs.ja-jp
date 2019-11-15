---
title: Azure Spring Cloud アプリケーションを開始、停止、および削除する方法 | Microsoft Docs
description: Azure Spring Cloud アプリケーションを開始、停止、および削除する方法
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 9f537ab425428728137e04713e434d8dc09e065a
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607723"
---
# <a name="how-to-start-stop-and-delete-your-azure-spring-cloud-application"></a>Azure Spring Cloud アプリケーションを開始、停止、および削除する方法

このガイドでは、Azure portal または CLI を使用して Azure Spring Cloud のアプリケーションの状態を変更する方法について説明します。

## <a name="using-the-azure-portal"></a>Azure ポータルの使用

アプリケーションをデプロイしたら、Azure portal を使用して、アプリケーションを**開始**、**停止**、および**削除**することができます。

1. Azure portal で、Azure Spring Cloud サービス インスタンスに移動します。
1. **[アプリケーション ダッシュボード]** タブを選択します。
1. 状態を変更するアプリケーションを選択します。
2. そのアプリケーションの **[概要]** ページで、アプリケーションを**開始および停止**、**再開**、および**削除**するためのボタンを見つけます。

## <a name="using-the-azure-cli"></a>Azure CLI の使用

> [!NOTE]
> オプションのパラメーターを使用して、Azure CLI で既定値を構成できます。 詳細については、[リファレンス ドキュメント](spring-cloud-cli-reference.md)を参照してください。  

Azure CLI 用の Spring Cloud 拡張機能をインストールします。

```azurecli
az extension add --name spring-cloud
```

* アプリケーションを開始するには:
    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* アプリケーションを停止するには:
    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* アプリケーションを再開するには:
    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* アプリケーションを削除するには:
    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
