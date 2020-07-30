---
title: Azure Data Share に関する Azure CLI リファレンス
description: Azure Data Share に関する Azure CLI リファレンスのランディング ページ
services: data-share
author: dbradish-microsoft
manager: barbkess
ms.service: data-share
ms.devlang: azurecli
ms.topic: reference
ms.date: 05/27/2020
ms.author: dbradish
ms.openlocfilehash: 404022b13f44174e4b647f0430a58fac5eeb81df
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87298364"
---
# <a name="azure-cli-for-azure-data-share"></a>Azure Data Share の Azure CLI

Azure コマンド ライン インターフェイス ([Azure CLI](/cli/azure/what-is-azure-cli)) は、Azure リソースを作成および管理するためのコマンド セットです。  Azure Data Share など多数の Azure サービス全体で使用できます。  データ共有用に 65 個を超えるさまざまなコマンドが用意されています。  これらのコマンドを使用すると、コマンド ラインからサービスを効率的に操作することができます。

## <a name="references-for-data-share"></a>Data Share のリファレンス

現在、Azure Data Share の Azure CLI コマンドはすべて、Azure CLI の拡張機能です。  この拡張機能により、リリース前の実験的なコマンドにアクセスすることができます。  拡張機能のリファレンスについて詳しくは、「[Azure CLI で拡張機能を使用する](/cli/azure/azure-cli-extensions-overview)」を参照してください。

|Azure CLI リファレンス |説明
|-|-|-|
| [az datashare](/cli/azure/ext/datashare/datashare) | Azure Data Share 管理用のすべてのコマンド
| [az datashare account](/cli/azure/ext/datashare/datashare/account) | Azure Data Share アカウントを管理するためのコマンドです。
| [az datashare consumer](/cli/azure/ext/datashare/datashare/consumer) | Azure Data Share を管理するためのコンシューマー向けのコマンドです。
| [az datashare dataset](/cli/azure/ext/datashare/datashare/dataset) | Azure Data Share データセットを管理するプロバイダー向けのコマンドです。
| [az datashare invitation](/cli/azure/ext/datashare/datashare/invitation) | Azure Data Share の招待を管理するコンシューマーが向けのコマンドです。
| [az datashare provider-share-subscription](/cli/azure/ext/datashare/datashare/provider-share-subscription) | Azure Data Share サブスクリプションを管理するためのプロバイダー向けのコマンドです。
| [az datashare synchronization](/cli/azure/ext/datashare/datashare/synchronization)  | Azure Data Share 同期を管理するためのコマンドです。
| [az datashare synchronization-setting](/cli/azure/ext/datashare/datashare/synchronization-setting)  | Azure Data Share の同期設定を管理するプロバイダー向けのコマンドです。

## <a name="reference-examples"></a>リファレンスの例

各 Azure CLI リファレンスで例が提供されています。 これらのタスクは Azure portal を通じて実行することもできますが、Azure CLI を使用するには 1 つのコマンド ラインが必要です。  以下に、Azure CLI の使用がどのくらい簡単であるかを理解できるように、いくつかのコード ブロックを示します。

Azure Data Share を使用するには、まずリソース グループが必要です。  Azure リソース グループを使用すると、Azure CLI による作成および管理が簡単になります。  

```azurecli
#create a resource group
az group create -location westus -name MyResourceGroup
```

```azurecli
#get a list of resource groups for a subscription
az group list --subscription MySubscription --output table
```

データ共有アカウントの作成も簡単です。

```azurecli
#create a data share account
az datashare account create --location "West US 2" --tags tag1=Red tag2=White --name MyAccount --resource-group MyResourceGroup
```

## <a name="see-also"></a>関連項目

* インストールとサインインについては、「[Azure CLI の概要](/cli/azure/get-started-with-azure-cli)」を参照してください。

* その他の[コア](/cli/azure/reference-index)機能および[拡張](/cli/azure/azure-cli-extensions-list)機能のリファレンスについては、Azure CLI のドキュメントを参照してください。
