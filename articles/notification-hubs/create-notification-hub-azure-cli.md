---
title: クイック スタート - Azure CLI を使用して Azure 通知ハブを作成する | Microsoft Docs
description: このチュートリアルでは、Azure CLI を使用して Azure 通知ハブを作成する方法を学習します。
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 03/17/2020
ms.author: dbradish
ms.reviewer: sethm
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 830fd33e19a10ec6472650e3d26fec677b82c3d7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "80069485"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>クイック スタート:Azure CLI を使用して Azure 通知ハブを作成する

Azure Notification Hubs は、任意のバックエンド (クラウドまたはオンプレミス) から任意のプラットフォーム (iOS、Android、Windows、Kindle、Baidu など) に通知を送信できる、使いやすく、かつスケールアウトされたプッシュ エンジンを提供します。 このサービスの詳細については、「[Azure Notification Hubs とは](notification-hubs-push-notification-overview.md)」を参照してください。

このクイック スタートでは、Azure CLI を使用して通知ハブを作成します。 最初のセクションでは、通知ハブの名前空間を作成し、その名前空間のアクセス ポリシー情報に対してクエリを実行する手順について説明します。 2 つ目のセクションでは、既存の名前空間に通知ハブを作成する手順について説明します。  カスタム アクセス ポリシーを作成する方法についても学習します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Notification Hubs には、バージョン 2.0.67 以降の Azure CLI が必要です。 `az --version` を実行し、インストールされているバージョンおよび依存ライブラリを検索します。 インストールまたはアップグレードする必要には、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」をご覧ください。

## <a name="prepare-your-environment"></a>環境を準備する

1. サインインします。

   CLI のローカル インストールを使用する場合は、[az login](/cli/azure/reference-index#az-login) コマンドを使用してサインインします。

    ```azurecli-interactive
    az login
    ```

    ターミナルに表示される手順に従って、認証プロセスを完了します。

2. Azure CLI 拡張機能をインストールします。

   Notification Hubs の Azure CLI コマンドを実行するには、Azure CLI の [Notification Hubs 用の拡張機能](/cli/azure/ext/notification-hub/notification-hub)をインストールします。  

    ```azurecli-interactive
    az extension add --name notification-hub
   ```

3. リソース グループを作成します。

   Azure Notification Hubs は、すべての Azure リソースと同様に、リソース グループにデプロイする必要があります。 リソース グループを使用すると、関連する Azure リソースを整理して管理できます。

   このクイック スタートでは、次の [az group create](/cli/azure/group#az-group-create) コマンドを使用して、*spnhubrg* という名前のリソース グループを *eastus* の場所に作成します。

   ```azurecli-interactive
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>通知ハブ名前空間を作成する

1. 通知ハブ用の名前空間を作成します。

   名前空間には 1 つまたは複数のハブが含まれており、名前はすべての Azure サブスクリプションで一意である必要があります。  指定されたサービス名前空間の可用性を確認するには、[az notification-hub namespace check-availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability) コマンドを使用します。  [az notification-hub namespace create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) コマンドを実行して、名前空間を作成します。  

   ```azurecli-interactive
   #check availability
   az notification-hub namespace check-availability --name spnhubns

   #create the namespace
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

2. 名前空間アクセス ポリシーのキーと接続文字列を一覧表示します。

   新しい名前空間には、**RootManageSharedAccessKey** という名前のアクセス ポリシーが自動的に作成されます。  すべてのアクセス ポリシーに、キーと接続文字列の 2 つのセットがあります。  名前空間のキーと接続文字列を一覧表示するには、[az notification-hub namespace authorization-rule](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) コマンドを実行します。

   ```azurecli-interactive
   az notification-hub namespace authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --name RootManageSharedAccessKey
   ```

## <a name="create-notification-hubs"></a>通知ハブを作成する

1. 最初の通知ハブを作成します。

   これで、新しい名前空間に通知ハブを作成できるようになりました。  [az notification-hub create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) コマンドを実行して、通知ハブを作成します。

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. 2 つ目の通知ハブを作成します。

   単一の名前空間に複数の通知ハブを作成することができます。  同じ名前空間に 2 つ目の通知ハブを作成するには、別のハブ名を使用して `az notification-hub create` コマンドをもう一度実行します。

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

## <a name="work-with-access-policies"></a>アクセス ポリシーを操作する

1. 通知ハブ用の新しい承認規則を作成します。

   新しい通知ハブごとに、アクセス ポリシーが自動的に作成されます。  独自のアクセス ポリシーを作成してカスタマイズするには、[az notification-hub authorization-rule create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) コマンドを使用します。

   ```azurecli-interactive
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Send
   ```

2. 通知ハブ用のアクセス ポリシーを一覧表示します。

   通知ハブに存在するアクセス ポリシーに対してクエリを実行するには、[az notification-hub authorization-rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) コマンドを使用します。

   ```azurecli-interactive
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > アプリケーションで **DefaultFullSharedAccessSignature** ポリシーを使用しないでください。 これはバックエンドのみで使用します。  クライアント アプリケーションでは **Listen** アクセス ポリシーのみを使用します。

3. 通知ハブ アクセス ポリシー用のキーと接続文字列を一覧表示する

   アクセス ポリシーごとに、キーと接続文字列の 2 つのセットがあります。  これらは、後でプッシュ通知を処理するために必要になります。  通知ハブ アクセス ポリシーのキーと接続文字列を一覧表示するには、[az notification-hub authorization-rule](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) コマンドを使用します。

   ```azurecli-interactive
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output json

   #query the keys and connection strings for the custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > [通知ハブ名前空間](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys)と[通知ハブ](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys)には、別個のアクセス ポリシーがあります。  キーと接続文字列に対してクエリを実行する場合は、正しい Azure CLI 参照を使用していることを確認してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[az group delete](/cli/azure/group) コマンドを使用して、リソース グループとすべての関連リソースを削除します。

```azurecli-interactive
az group delete --name spnhubrg
```

## <a name="see-also"></a>関連項目

Azure CLI で通知ハブを管理するためのすべての機能を検出します。

* [Notification Hubs の完全な Azure CLI 参照リスト](/cli/azure/ext/notification-hub/notification-hub)
* [Notification Hubs 名前空間の Azure CLI 参照リスト](/cli/azure/ext/notification-hub/notification-hub/namespace)
* [Notification Hubs 承認規則の Azure CLI 参照リスト](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)
* [Notification Hubs 資格情報の Azure CLI 参照リスト](/cli/azure/ext/notification-hub/notification-hub/credential)
