---
title: Azure Service Bus のトラブルシューティング ガイド | Microsoft Docs
description: Azure Service Bus の使用時に発生する可能性のあるいくつかの問題のトラブルシューティングに関するヒントと推奨事項について説明します。
ms.topic: article
ms.date: 03/03/2021
ms.openlocfilehash: 27249d7e016ea8aee0552bbbf1687647760d4b6f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786569"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Azure Service Bus のトラブルシューティング ガイド
この記事では、Azure Service Bus の使用時に発生する可能性のあるいくつかの問題のトラブルシューティングに関するヒントと推奨事項について説明します。 

## <a name="connectivity-certificate-or-timeout-issues"></a>接続、証明書、またはタイムアウトの問題
次の手順は、*.servicebus.windows.net のすべてのサービスの接続/証明書/タイムアウトの問題のトラブルシューティングに役立ちます。 

- `https://<yournamespace>.servicebus.windows.net/` の参照または [wget](https://www.gnu.org/software/wget/) を行います。 これは、IP フィルタリング、仮想ネットワーク、または証明書チェーンの問題 (java SDK の使用時には一般的) があるかどうかを確認するのに役立ちます。

    成功したメッセージの例を次に示します。
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    失敗したエラー メッセージの例を次に示します。

    ```xml
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- 次のコマンドを実行して、ファイアウォールでポートがブロックされているかどうかを確認します。 使用されるポートは、443 (HTTPS)、5671 (AMQP)、9354 (Net Messaging/SBMP) です。 使用するライブラリによっては、他のポートも使用されます。 5671 ポートがブロックされているかどうかを確認するサンプル コマンドを次に示します。 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Linux の場合:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- 接続の問題が断続的に発生する場合は、次のコマンドを実行して、破棄されたパケットがあるかどうかを確認します。 このコマンドは、1 秒ごとに 25 件の異なる TCP 接続をサービスと確立しようとします。 次に、成功または失敗した数を確認し、TCP 接続の待機時間も確認します。 `psping` ツールは、[こちら](/sysinternals/downloads/psping)からダウンロードできます。

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    `tnc` や`ping` などの他のツールを使用している場合は、同等のコマンドを使用できます。 
- 前の手順で解決できない場合は、ネットワーク トレースを取得して [Wireshark](https://www.wireshark.org/) などのツールを使用して分析します。 必要に応じて [Microsoft サポート](https://support.microsoft.com/) にお問い合わせください。 
- 接続の許可リストに追加する適切な IP アドレスを見つけるには、「[どのような IP アドレスを許可リストに追加する必要がありますか](service-bus-faq.yml#what-ip-addresses-do-i-need-to-add-to-allow-list-)」を参照してください。 


## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>サービスのアップグレードまたは再起動で発生する可能性のある問題

### <a name="symptoms"></a>現象
- 要求が一時的に調整される場合があります。
- 受信メッセージや要求がドロップされる可能性があります。
- ログ ファイルにエラー メッセージが含まれる可能性があります。
- アプリケーションが数秒間サービスから切断される可能性があります。

### <a name="cause"></a>原因
バックエンド サービスのアップグレードと再起動によって、アプリケーションに次のような問題が生じる可能性があります。

### <a name="resolution"></a>解像度
アプリケーション コードで SDK を利用している場合、再試行ポリシーは既に組み込まれており、アクティブになっています。 アプリケーションやワークフローに大きな影響を与えずに、アプリケーションは再接続されます。

## <a name="unauthorized-access-send-claims-are-required"></a>未承認のアクセス: 要求の送信が必要

### <a name="symptoms"></a>現象 
このエラーは、オンプレミスのコンピューター上で、送信アクセス許可を持つユーザー割り当てマネージド ID を使用して Visual Studio から Service Bus のトピックにアクセスしようとしたときに発生することがあります。

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

### <a name="cause"></a>原因
この ID には、Service Bus トピックにアクセスするためのアクセス許可がありません。 

### <a name="resolution"></a>解像度
このエラーを解決するには、[Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) ライブラリをインストールします。  詳しくは、「[ローカル開発用における認証](/dotnet/api/overview/azure/service-to-service-authentication#local-development-authentication)」をご覧ください。 

ロールにアクセス許可を割り当てる方法については、「[Azure Service Bus リソースにアクセスするために Azure Active Directory を使用してマネージド ID を認証する](service-bus-managed-service-identity.md)」をご覧ください。

## <a name="service-bus-exception-put-token-failed"></a>Service Bus の例外:Put トークンが失敗しました

### <a name="symptoms"></a>現象
同じ Service Bus 接続を使用して 1000 を超えるメッセージを送信しようとすると、次のエラー メッセージが表示されます。 

`Microsoft.Azure.ServiceBus.ServiceBusException: Put token failed. status-code: 403, status-description: The maximum number of '1000' tokens per connection has been reached.` 

### <a name="cause"></a>原因
Service Bus 名前空間への 1 つの接続を使用するメッセージの送受信に使用されるトークンの数には、制限があります。 それは 1000 です。 

### <a name="resolution"></a>解像度
より多くのメッセージを送信するために、Service Bus 名前空間への新しい接続を開きます。

## <a name="adding-virtual-network-rule-using-powershell-fails"></a>PowerShell を使って仮想ネットワーク規則を追加できない

### <a name="symptoms"></a>現象
仮想ネットワーク規則で 1 つの仮想ネットワークから 2 つのサブネットを構成しました。 [Remove-AzServiceBusVirtualNetworkRule](/powershell/module/az.servicebus/remove-azservicebusvirtualnetworkrule) コマンドレットを使用して 1 つのサブネットを削除しようとしたところ、仮想ネットワーク規則からこのサブネットが削除されません。 

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName $resourceGroupName -Namespace $serviceBusName -SubnetId $subnetId
```

### <a name="cause"></a>原因
サブネットに指定した Azure Resource Manager ID が無効である可能性があります。 これは、仮想ネットワークが、Service Bus 名前空間があるリソース グループとは別のものに存在する場合に発生する可能性があります。 CLI コマンドで仮想ネットワークのリソース グループを明示的に指定しないと、Service Bus 名前空間のリソース グループを使用して Azure Resource Manager ID が作成されます。 そのため、ネットワーク規則からサブネットを削除できません。 

### <a name="resolution"></a>解決方法
仮想ネットワークがあるリソース グループの名前を含むサブネットの完全な Azure Resource Manager ID を指定します。 次に例を示します。

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName myRG -Namespace myNamespace -SubnetId "/subscriptions/SubscriptionId/resourcegroups/ResourceGroup/myOtherRG/providers/Microsoft.Network/virtualNetworks/myVNet/subnets/mySubnet"
```

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。 

- 「[Azure Resource Manager の例外](service-bus-resource-manager-exceptions.md)」。 この記事では、Azure Resource Manager を使用して (テンプレートまたは直接呼び出しにより) Azure Service Bus とやり取りするときに生成される例外を示します。
- 「[メッセージングの例外](service-bus-messaging-exceptions.md)」。 この記事では、Azure Service Bus の .NET Framework によって生成される例外の一覧を示します。