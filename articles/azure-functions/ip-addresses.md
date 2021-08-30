---
title: Azure Functions の IP アドレス
description: 関数アプリの着信 IP アドレスと送信 IP アドレスを確認する方法、およびこれらのアドレスが変更される理由について説明します。
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: a884edd23fa1538fcc2b00c80190eab6699e1e47
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414487"
---
# <a name="ip-addresses-in-azure-functions"></a>Azure Functions の IP アドレス

この記事では、関数アプリの IP アドレスに関連する次の概念について説明します。

* 関数アプリが現在使用している IP アドレスの確認。
* 関数アプリの IP アドレスが変更される条件。
* 関数アプリにアクセスできる IP アドレスの制限。
* 関数アプリの専用の IP アドレスの定義。

IP アドレスは、個々の関数ではなく、関数アプリに関連付けられています。 着信 HTTP 要求は、着信 IP アドレスを使用して個々の関数を呼び出すことはできません。既定のドメイン名 (functionappname.azurewebsites.net) またはカスタム ドメイン名を使用する必要があります。

## <a name="function-app-inbound-ip-address"></a>関数アプリの着信 IP アドレス

各関数アプリには、1 つの着信 IP アドレスがあります。 この IP アドレスを確認するには、次のようにします。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 関数アプリに移動します。
3. **[設定]** で **[プロパティ]** を選択します。 受信 IP アドレスは **[仮想 IP アドレス]** の下に表示されます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

ローカル クライアント コンピューターから `nslookup` ユーティリティを使用します。

```command
nslookup <APP_NAME>.azurewebsites.net
```

---

## <a name="function-app-outbound-ip-addresses"></a><a name="find-outbound-ip-addresses"></a>関数アプリの送信 IP アドレス

各関数アプリには、使用可能な一連の送信 IP アドレスがあります。 関数からバックエンド データベースなどへの送信接続では、使用可能な送信 IP アドレスの 1 つが送信元 IP アドレスとして使用されます。 指定された接続でどの IP アドレスが使用されるかを事前に知ることはできません。 このため、バックエンド サービスでは、関数アプリのすべての送信 IP アドレスに対してファイアウォールを開く必要があります。

関数アプリで使用可能な送信 IP アドレスを確認するには、次のようにします。

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. [Azure Resource Explorer](https://resources.azure.com) にサインインします。
2. **[サブスクリプション] > {自分のサブスクリプション} > [プロバイダー] > [Microsoft.Web] > [サイト]** を選択します。
3. JSON パネルで、末尾が関数アプリ名の `id` プロパティを持つサイトを探します。
4. `outboundIpAddresses` と `possibleOutboundIpAddresses` を参照してください。 

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az functionapp show --resource-group <GROUP_NAME> --name <APP_NAME> --query outboundIpAddresses --output tsv
az functionapp show --resource-group <GROUP_NAME> --name <APP_NAME> --query possibleOutboundIpAddresses --output tsv
```
---

現在、関数アプリでは、`outboundIpAddresses` のセットを使用できます。 `possibleOutboundIpAddresses` のセットには、関数アプリが[他の価格レベルにスケーリングする](#outbound-ip-address-changes)場合にのみ使用できる IP アドレスが含まれています。

> [!NOTE]
> [従量課金プラン](consumption-plan.md)または [Premium プラン](functions-premium-plan.md)で実行されている関数アプリをスケーリングすると、新しい送信 IP アドレスの範囲が割り当てられる場合があります。 これらのプランで実行する場合、報告された送信 IP アドレスを使用して、確定的な許可リストを作成することはできません。 動的スケーリング中に使用される可能性のあるすべての送信アドレスを含めるには、データ センター全体を許可リストに追加する必要があります。

## <a name="data-center-outbound-ip-addresses"></a>データ センターの送信 IP アドレス

関数アプリで使用する送信 IP アドレスを許可リストに登録する必要がある場合、別の選択肢となるのは、関数アプリのデータ センター (Azure リージョン) を許可リストに登録することです。 [すべての Azure データ センターの IP アドレスが記述された JSON ファイルをダウンロード](https://www.microsoft.com/en-us/download/details.aspx?id=56519)してください。 次に、関数アプリを実行するリージョンに適用される JSON 要素を検索します。

たとえば、次の JSON フラグメントは、西ヨーロッパの許可リストがどのようになるかを示しています。

```
{
  "name": "AzureCloud.westeurope",
  "id": "AzureCloud.westeurope",
  "properties": {
    "changeNumber": 9,
    "region": "westeurope",
    "platform": "Azure",
    "systemService": "",
    "addressPrefixes": [
      "13.69.0.0/17",
      "13.73.128.0/18",
      ... Some IP addresses not shown here
     "213.199.180.192/27",
     "213.199.183.0/24"
    ]
  }
}
```

 このファイルがいつ更新されるかや IP アドレスがいつ変更されるかの詳細については、[[ダウンロード センター] ページ](https://www.microsoft.com/en-us/download/details.aspx?id=56519)の **[詳細]** セクションを展開してください。

## <a name="inbound-ip-address-changes"></a><a name="inbound-ip-address-changes"></a>着信 IP アドレスの変更

次の操作を行うと、着信 IP アドレスが変更される **ことがあります**。

- 関数アプリを削除した後、別のリソース グループ内で再作成する。
- リソース グループとリージョンの組み合わせに含まれる最後の関数アプリを削除した後、再作成する。
- [証明書の更新](../app-service/configure-ssl-certificate.md#renew-certificate)時などに TLS バインドを削除する。

関数アプリが[従量課金プラン](consumption-plan.md)または [Premium プラン](functions-premium-plan.md)で実行される場合、着信 IP アドレスは、[上記のような](#inbound-ip-address-changes)アクションを実行しなくても、変更される場合があります。

## <a name="outbound-ip-address-changes"></a>送信 IP アドレスの変更

送信 IP アドレスの相対的な安定性は、ホスティング プランによって異なります。  

### <a name="consumption-and-premium-plans"></a>従量課金および Premium プラン

自動スケールの動作により、送信 IP は、[従量課金プラン](consumption-plan.md)または [Premium プラン](functions-premium-plan.md)で実行しているときにいつでも変更できます。 

許可リストに追加する必要がある場合など、関数アプリの送信 IP アドレスを制御する必要がある場合は、Premium ホスティング プランでの実行中に[仮想ネットワーク NAT ゲートウェイ](#virtual-network-nat-gateway-for-outbound-static-ip)を実装することを検討してください。 これは、Dedicated (App Service) プランで実行しても行えます。

### <a name="dedicated-plans"></a>専用プラン

専用 (App Service) プランで実行する場合は、次のような場合に、関数アプリに使用できる送信 IP アドレスのセットが変更される可能性があります。

* 着信 IP アドレスが変更される可能性のあるアクションを実行する。
* 専用 (App Service) プランの価格レベルを変更する。 すべての価格レベルについて、アプリで使用可能なすべての送信 IP アドレスのリストが `possibleOutboundIPAddresses` プロパティに含まれています。 「[IP アドレスを見つける](#find-outbound-ip-addresses)」を参照してください。

#### <a name="forcing-an-outbound-ip-address-change"></a>送信 IP アドレスの変更の強制

専用 (App Service) プランの送信 IP アドレスの変更を意図的に強制するには、次の手順に従います。

1. App Service プランの価格レベルを Standard から上げるか、または Premium v2 から下げます。

2. 10 分間待ちます。

3. 最初の価格レベルに戻します。

## <a name="ip-address-restrictions"></a>IP アドレスの制限

関数アプリへのアクセス許可または拒否する IP アドレスの一覧を構成できます。 詳細については、「[Azure App Service 静的 IP 制限](../app-service/app-service-ip-restrictions.md)」を参照してください。

## <a name="dedicated-ip-addresses"></a>専用の IP アドレス

関数アプリに静的な専用の IP アドレスが必要な場合は、検討可能ないくつかの方法があります。 

### <a name="virtual-network-nat-gateway-for-outbound-static-ip"></a>送信の静的な IP 用の仮想ネットワーク NAT ゲートウェイ

仮想ネットワーク NAT ゲートウェイを使用して、静的パブリック IP アドレス経由でトラフィックを送信することで、関数からの送信トラフィックの IP アドレスを制御できます。 このトポロジは、[Premium プラン](functions-premium-plan.md)または [Dedicated (App Service) プラン](dedicated-plan.md)で実行する場合に使用できます。 詳しくは、「[チュートリアル: Azure 仮想ネットワーク NAT ゲートウェイを使用して Azure Functions の送信 IP を制御する](functions-how-to-use-nat-gateway.md)」を参照してください。

### <a name="app-service-environments"></a>App Service Environment

受信と送信の両方の IP アドレスを完全に制御するには、[App Service Environment](../app-service/environment/intro.md) (App Service プランの[分離レベル](https://azure.microsoft.com/pricing/details/app-service/)) をお勧めします。 詳細については、次を参照してください。 [App Service Environment の IP アドレス](../app-service/environment/network-info.md#ase-ip-addresses)と[App Service Environment への受信トラフィックを制御する方法](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)します。

関数アプリが App Service 環境内で実行されるかどうかを確認するには、次のようにします。

# <a name="azure-porta"></a>[Azure Portal](#tab/portal)

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 関数アプリに移動します。
3. **[概要]** タブを選択します。
4. App Service プランの階層は、 **[App Service プラン/価格レベル]** の下に表示されます。 App Service 環境の価格レベルは、 **[Isolated]** です。

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

---

App Service 環境 `sku` は `Isolated` です。

## <a name="next-steps"></a>次のステップ

多くの場合、IP が変更されるのは、関数アプリのスケールが変更されるためです。 [関数アプリのスケーリングの詳細](functions-scale.md)を参照してください。
