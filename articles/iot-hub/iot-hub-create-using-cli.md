---
title: "Azure CLI (az.py) を使用して IoT ハブを作成する | Microsoft Docs"
description: "クロスプラットフォームの Azure CLI 2.0 (プレビュー) (az.py) を使用して Azure IoT Hub を作成する方法。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 39c8c4944ef19379dc04e04a717ab60d305593c4
ms.openlocfilehash: c52d9a5fadf494cc066bee773543c9d67bb8334b


---
# <a name="create-an-iot-hub-using-the-azure-cli-20-preview"></a>Azure CLI 2.0 (プレビュー) を使用して IoT ハブを作成する

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>はじめに

Azure CLI 2.0 (プレビュー) (az.py) を使用して、Azure IoT Hub をプログラムで作成、管理できます。 この記事では、Azure CLI 2.0 (プレビュー) (az.py) を使用して IoT ハブを作成する方法を説明します。

次のいずれかの CLI バージョンを使用してタスクを完了できます。

* [Azure CLI (azure.js)](iot-hub-create-using-cli-nodejs.md) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI
* Azure CLI 2.0 (プレビュー) (az.py) - この記事で説明している、Resource Manager デプロイメント モデル用の次世代 CLI

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウント。 アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成することができます。
* [Azure CLI 2.0 (プレビュー)][lnk-CLI-install]。

## <a name="sign-in-and-set-your-azure-account"></a>サインインして Azure アカウントを設定する

Azure アカウントにサインインし、IoT Hub リソースを操作する Azure CLI を構成します。

1. コマンド プロンプトで、[ログイン コマンド][lnk-login-command]を実行します。
    
    ```azurecli
    az login
    ```

    指示に従って、コードを使用して認証し、Web ブラウザーで Azure アカウントにサインインします。

2. 複数の Azure サブスクリプションがある場合は、Azure にサインインすると、資格情報に関連付けられているすべてのAzure アカウントへのアクセスが許可されます。 以下の、利用できる [Azure アカウントを一覧表示するコマンド][lnk-az-account-command] を使用します。
    
    ```azurecli
    az account list 
    ```

    以下のコマンドを使用して、コマンドを実行して IoT ハブを作成するために使用するサブスクリプションを選択します。 前のコマンドの出力から、サブスクリプション名または ID のいずれかを使用できます。

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. IoT リソースをデプロイする前に、IoT プロバイダーを登録する必要があります。 以下の、[IoT プロバイダーを登録するコマンド][lnk-az-register-command]を実行します。
    
    ```azurecli
    az provider register -namespace "Microsoft.Devices"
    ```

4. 場合によっては、Azure CLI _IoT コンポーネント_をインストールする必要があります。 以下の、[IoT コンポーネントを追加するコマンド][lnk-az-addcomponent-command]を実行します。
    
    ```azurecli
    az component update --add iot
    ```

## <a name="create-an-iot-hub"></a>IoT Hub の作成

Azure CLI を使用してリソース グループを作成してから、IoT ハブを追加します。

1. IoT ハブを作成するときは、リソース グループの中に作成する必要があります。 既存のリソース グループを使用するか、以下の、[リソース グループを作成するコマンド][lnk-az-resource-command]を実行します。
    
    ```azurecli
     az resource group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > 上の例では、West US という場所にリソース グループを作成します。 `az account list-locations -o table` コマンドを実行すると、利用できる場所を一覧表示できます。
    >
    >

2. 以下の、リソース グループに[IoT ハブを作成するコマンド][lnk-az-iot-command]を実行します。
    
    ```azurecli
    az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1
    ```

> [!NOTE]
> IoT ハブの名前は、グローバルに一意である必要があります。 上のコマンドは、課金の対象となる S1 価格レベルに IoT ハブを作成します。 詳細については、「[Azure IoT Hub の価格][lnk-iot-pricing]」をご覧ください。
>
>

## <a name="remove-an-iot-hub"></a>IoT Hub の削除

Azure CLI を使用して、IoT Hub など、[個々のリソースを削除][lnk-az-resource-command]することも、リソース グループとそのすべてのリソース (IoT Hub など) を削除をすることもできます。

IoT Hub を削除するには、次のコマンドを実行します。

```azurecli
az resource delete --name {your iot hub name} --resource-group {your resource group name} --resource-type Microsoft.Devices/IotHubs
```

リソース グループとそのすべてのリソースを削除するには、次のコマンドを実行します。

```azurecli
az resource group delete --name {your resource group name}
```

## <a name="next-steps"></a>次のステップ
IoT Hub の開発に関する詳細については、以下を参照してください

* [IoT Hub 開発者向けガイド][lnk-devguide]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [Azure Portal を使用した IoT Hub の管理][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 



<!--HONumber=Dec16_HO2-->


