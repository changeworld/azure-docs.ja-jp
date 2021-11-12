---
title: Azure Spring Cloud で組み込みの永続的ストレージを使用する方法 | Microsoft Docs
description: Azure Spring Cloud で組み込みの永続的ストレージを使用する方法
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: karler
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: b56932b9638ede2b3db36050310a06921d115103
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477992"
---
# <a name="use-built-in-persistent-storage-in-azure-spring-cloud"></a>Azure Spring Cloud で組み込みの永続的ストレージを使用する

**この記事の適用対象:** ✔️ Java ✔️ C#

Azure Spring Cloud には、アプリケーション用に永続的ストレージと一時ストレージの 2 種類の組み込みストレージが用意されています。

Azure Spring Cloud の既定では、各アプリケーション インスタンスに一時ストレージが提供されます。 一時ストレージはインスタンスごとに 5 GB に制限されており、既定のマウント パスは /tmp になります。

> [!WARNING]
> アプリケーション インスタンスを再起動すると、それに関連付けられている一時ストレージが完全に削除されます。

永続的ストレージは、Azure によって管理され、アプリケーションごとに割り当てられるファイル共有コンテナーです。 永続的ストレージに格納されているデータは、アプリケーションの全インスタンスによって共有されます。 Azure Spring Cloud インスタンスには、永続的ストレージを有効にしたアプリケーションを最大 10 個指定できます。 各アプリケーションには、50 GB の永続的ストレージが割り当てられます。 永続的ストレージの既定のマウント パスは /persistent です。

> [!WARNING]
> アプリケーションの永続的ストレージを無効にすると、すべてのストレージの割り当てが解除され、格納されているデータがすべて失われます。

## <a name="enable-or-disable-built-in-persistent-storage"></a>組み込みの永続ストレージを有効または無効にする

組み込みの永続ストレージの状態は、Azure portal または Azure CLI を使用して変更できます。

#### <a name="portal"></a>[ポータル](#tab/azure-portal)
## <a name="enable-or-disable-built-in-persistent-storage-with-the-portal"></a>ポータルで組み込みの永続ストレージを有効または無効にする
ポータルを使用して、組み込みの永続ストレージを有効または無効にできます。

1. Azure portal の **ホーム ページ** から、**[すべてのリソース]** を選択します。

    >![[すべてのリソース] アイコンを見つける](media/portal-all-resources.jpg)

1. 永続的ストレージを必要とする Azure Spring Cloud リソースを選択します。 この例では、**upspring** という名前のアプリケーションが選択されています。

    > ![アプリケーションを選択します。](media/select-service.jpg)

1. **[設定]** という見出しの下にある **[アプリ]** を選択します。

1. Azure Spring Cloud サービスが表に表示されます。  永続的ストレージを追加するサービスを選択します。 この例では、**ゲートウェイ** サービスが選択されています。

    > ![サービスを選択する](media/select-gateway.jpg)

1. サービスの構成ページから、**[構成]** を選択します

1. **[永続的なストレージ]** タブを選択し、 **[有効]** を選択して永続ストレージを有効にするか、 **[無効]** を選択して永続ストレージを無効にします。

    > ![永続的ストレージを有効にする](media/enable-persistent-storage.jpg)

永続的ストレージが有効になると、そのサイズとパスが **[永続ストレージ]** タブに表示されます。

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
## <a name="use-the-azure-cli-to-enable-or-disable-built-in-persistent-storage"></a>Azure CLI を使用して組み込みの永続ストレージを有効または無効にする
必要な場合は、次のコマンドを使用して、Azure CLI 用の Spring Cloud 拡張機能をインストールします。

```azurecli
az extension add --name spring-cloud
```

その他の操作:

* 組み込みの永続的ストレージが有効になった状態のアプリを作成するには、次の手順を実行します。

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* 既存のアプリで組み込みの永続的ストレージを有効にするには、次の手順を実行します。

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* 既存のアプリで組み込みの永続的ストレージを無効にするには:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```
---
> [!WARNING]
> アプリケーションの永続的ストレージを無効にすると、すべてのストレージの割り当てが解除され、格納されているデータがすべて完全に失われます。

## <a name="next-steps"></a>次の手順

* アプリケーションとサービスのクォータについては、[こちら](./quotas.md)をご覧ください。
* [アプリケーションを手動でスケーリングする](./how-to-scale-manual.md)方法について学習してください。
