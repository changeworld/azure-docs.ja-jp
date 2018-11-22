---
title: ご利用の Azure Cosmos アカウントに対して IP ファイアウォールを構成する方法
description: Azure Cosmos DB データベース アカウント上でファイアウォールをサポートするために IP アクセス制御ポリシーを構成する方法について説明します。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: d9a00bccb83fc60c96594ffacc5abde98c0f8470
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51632583"
---
# <a name="how-to-configure-ip-firewall-for-your-azure-cosmos-account"></a>ご利用の Azure Cosmos アカウントに対して IP ファイアウォールを構成する方法

ご利用の Azure Cosmos アカウントに格納されたデータは、IP ファイアウォールを使用することによりセキュリティで保護することができます。 Azure Cosmos DB では、受信ファイアウォールをサポートするために IP ベースのアクセス制御に対応しています。 Azure Cosmos アカウント上で IP ファイアウォールを設定するには、次の方法のいずれかを使用できます。

1. Azure portal から
2. Azure Resource Manager テンプレートを使用した宣言による
3. Azure CLI または Azure Powershell を介してプログラムで、**ipRangeFilter** プロパティを更新する。

## <a id="configure-ip-policy"></a> Azure portal を使用した IP ファイアウォールの構成

Azure portal で IP アクセス制御ポリシーを設定するには、Azure Cosmos DB アカウント ページに移動し、ナビゲーション メニューの **[ファイアウォールと仮想マシン]** をクリックして、**[許可するアクセス元]** の値を **[選択されたネットワーク]** に変更して、**[保存]** をクリックします。 

![Azure Portal で [ファイアウォール] ページを開く方法を示すスクリーンショット](./media/how-to-configure-firewall/azure-portal-firewall.png)

IP アクセス制御をオンにすると、Azure portal では、IP アドレスと IP アドレス範囲を指定することができ、他の Azure サービスおよび Azure portal へのアクセスを有効にするスイッチも提供されます。 これらのスイッチの詳細については、以下のセクションで説明します。

> [!NOTE]
> ご利用の Azure Cosmos アカウントの IP アクセス制御ポリシーを有効にすると、IP アドレス範囲の許可リストに入っていないマシンからご利用の Azure Cosmos アカウントへの要求はすべてブロックされます。 アクセス制御の整合性を確保するために、ポータルから Azure Cosmos DB リソースを参照することもブロックされます。

### <a name="allow-requests-from-the-azure-portal"></a>Azure portal からの要求を許可する 

IP アクセス制御ポリシーをプログラムで有効にする場合は、アクセスを維持するために、Azure Portal の IP アドレスを **ipRangeFilter** プロパティに追加する必要があります。 ポータルの IP アドレスは次のとおりです。

|リージョン|IP アドレス|
|------|----------|
|ドイツ|51.4.229.218|
|中国|139.217.8.252|
|米国政府|52.244.48.71|
|上記の 3 つを除くすべてのリージョン|104.42.195.92、40.76.54.131、52.176.6.30、52.169.50.45、52.187.184.26|

Azure portal へのアクセスを有効にするには、次のスクリーン ショットに示すように、**[Azure portal からのアクセスを許可する]** を選択します。 

![Azure Portal へのアクセスを有効にする方法を示すスクリーンショット](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>世界中の Azure データ センターまたは Azure 内の他のソースからの要求を許可する

Azure Stream Analytics や Azure Functions などの静的 IP を提供しないサービスから Azure Cosmos アカウントにアクセスする場合も、IP ファイアウォールを使用してアクセスを制限できます。 そのようなサービスから Azure Cosmos アカウントへのアクセスを許可するには、ファイアウォール設定を有効にする必要があります。 このファイアウォール設定を有効にするには、許可された IP アドレスの一覧に IP アドレス 0.0.0.0 を追加します。 0.0.0.0 により、Azure データセンターの IP 範囲からご利用の Azure Cosmos アカウントへの要求が制限されます。 この設定では、その他の IP 範囲からご利用の Azure Cosmos アカウントへのアクセスは許可されません。

> [!NOTE]
> このオプションでは、Azure にデプロイされた他のお客様のサブスクリプションからの要求を含む、Azure からのすべての要求を許可するようにファイアウォールが構成されます。 このオプションで許可される IP のリストは非常に範囲が広くなるので、ファイアウォール ポリシーの有効性が制限されます。 このオプションは、ご利用の要求が静的 IP または VNET 内のサブネットから発生しない場合にのみ使用する必要があります。 このオプションを選択すると Azure portal からのアクセスは自動的に許可されるようになります。これは、Azure portal が Azure にデプロイされているからです。

Azure portal へのアクセスを有効にするには、次のスクリーン ショットに示すように、**[Accept connections from public Azure datacenters]\(パブリック Azure データセンターからの接続を受け入れる\)** を選択します。 

![Azure Portal で [ファイアウォール] ページを開く方法を示すスクリーンショット](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>現在ご利用の IP からの要求

開発をシンプルにするために、Azure portal では、ご利用のクライアント マシンの IP アドレスを識別して許可リストに追加することができます。これにより、そのマシンで稼働しているアプリで、ご利用の Azure Cosmos アカウントにアクセスすることができます。 クライアント IP アドレスは、ポータルによって自動的に検出されます。 それはご利用のマシンのクライアント IP アドレスである場合もあれば、ご利用のネットワーク ゲートウェイの IP アドレスである場合もあります。 運用環境にご利用のワークロードを移行する前に、この IP アドレスを必ず削除してください。 

現在の IP を有効にするには、**[現在の IP を追加する]** を選んで IP アドレスの一覧に現在の IP アドレスを追加してから、**[保存]** をクリックします。

![現在の IP のファイアウォール設定を構成する方法を示すスクリーンショット](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>クラウド サービスからの要求

Azure では、Azure Cosmos DB を使用して中間層のサービス ロジックをホストする手段としてクラウド サービスがよく利用されます。 クラウド サービスからご利用の Azure Cosmos アカウントにアクセスできるようにするには、[IP アクセス制御ポリシーを構成する](#configure-ip-policy)ことで、その Azure Cosmos アカウントに関連付けられている IP アドレスの許可リストにクラウド サービスのパブリック IP アドレスを追加する必要があります。 これにより、クラウド サービスのすべてのロール インスタンスからその Azure Cosmos アカウントに確実にアクセスできるようになります。 クラウド サービスの IP アドレスは Azure Portal で確認できます。次のスクリーンショットをご覧ください。

![Azure Portal に表示されるクラウド サービスのパブリック IP アドレスを示すスクリーンショット](./media/how-to-configure-firewall/public-ip-addresses.png)

新たに (複数の) ロール インスタンスを追加してご利用のクラウド サービスをスケールアウトした場合、それらのインスタンスは、同じクラウド サービスに属すことになるので、Azure Cosmos アカウントへのアクセス権が自動的に割り当てられます。

### <a name="requests-from-virtual-machines"></a>仮想マシンからの要求

Azure Cosmos DB を使用する中間層サービスのホスティングには、[仮想マシン](https://azure.microsoft.com/services/virtual-machines/)または[仮想マシン スケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)を使用することもできます。 仮想マシンからのアクセスを許可するようにご利用の Cosmos アカウントを構成するには、[IP アクセス制御ポリシーを構成する](#configure-ip-policy)ことで、仮想マシンや仮想マシン スケール セットのパブリック IP アドレスを、ご利用の Azure Cosmos アカウントで許可された IP アドレスの 1 つとして構成する必要があります。 仮想マシンの IP アドレスは Azure portal で取得できます。次のスクリーンショットを参照してください。

![Azure Portal に表示される仮想マシンのパブリック IP アドレスを示すスクリーンショット](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

このグループに新たに仮想マシン インスタンスを追加した場合、それらのインスタンスにも自動的にご利用の Azure Cosmos アカウントへのアクセス権が割り当てられます。

### <a name="requests-from-the-internet"></a>インターネットからの要求

インターネット上のコンピューターからご利用の Azure Cosmos アカウントにアクセスするときは、そのコンピューターのクライアント IP アドレスまたは IP アドレス範囲を、ご利用のアカウントの IP アドレスの許可リストに追加する必要があります。

## <a id="configure-ip-firewall-arm"></a>Resource Manager テンプレートを使用して IP ファイアウォールを構成する

ご利用の Azure Cosmos アカウントへのアクセス制御を構成するには、Resource Manager テンプレートで、ホワイトリストに登録される必要のある IP 範囲のリストを使用して **ipRangeFilter** 属性を指定する必要があります。 たとえば、次の JSON コードをご利用のテンプレートに追加します。

```json
   {
     "apiVersion": "2015-04-08",
     "type": "Microsoft.DocumentDB/databaseAccounts",
     "kind": "GlobalDocumentDB",
     "name": "[parameters('databaseAccountName')]",
     "location": "[resourceGroup().location]",
     "properties": {
     "databaseAccountOfferType": "Standard",
     "name": "[parameters('databaseAccountName')]",
     "ipRangeFilter":"183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
   }
```

## <a id="configure-ip-firewall-cli"></a> Azure CLI を使用して IP アクセス制御ポリシーを構成する

次のコマンドでは、IP アクセス制御を使用して Azure Cosmos アカウントを作成する方法が示されています。 

```azurecli-interactive

name="<Azure Cosmos account name>"
resourceGroupName="<Resource group name>"

az cosmosdb create \
  --name $name \
  --kind GlobalDocumentDB \
  --resource-group $resourceGroupName \
  --max-interval 10 \
  --max-staleness-prefix 200 \
  --ip-range-filter "183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
```

既存のアカウント用のファイアウォール設定を更新するには、次のコマンドを実行します。

```azurecli-interactive
az cosmosdb update \
      --name $name \
      --resource-group $resourceGroupName \
      --ip-range-filter "183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
```

## <a id="troubleshoot-ip-firewall"></a>IP アクセス制御ポリシーに関する問題のトラブルシューティングの方法

次のオプションを使用して IP アクセス制御ポリシーに関する問題のトラブルシューティングを行うことができます。 

### <a name="azure-portal"></a>Azure ポータル 
ご利用の Azure Cosmos アカウントの IP アクセス制御ポリシーを有効にすることにより、IP アドレス範囲の許可リストに入っていないマシンからご利用のアカウントへのアクセスはすべてブロックされます。 そのため、コンテナーの参照やドキュメントのクエリなどのポータルのデータ プレーン操作を有効にする場合は、ポータルの **[ファイアウォール]** ウィンドウを使用して Azure portal へのアクセスを明示的に許可する必要があります。

### <a name="sdks"></a>SDK 
許可リストに入っていないマシンから SDK を使用して Azure Cosmos リソースにアクセスすると、**"404 - 見つかりません" という一般的な応答が返され、詳しい情報は示されません**。 ご利用の Cosmos アカウントに対する IP 許可リストを確認し、そのアカウントに正しいポリシー構成が確実に適用されるようにします。 

### <a name="check-source-ips-in-blocked-requests"></a>ブロックされた要求内のソース IP を確認する
ご利用の Azure Cosmos アカウントで診断ログを有効にすると、これらのログによって各要求と応答が示されます。 **ファイアウォールに関連するメッセージは、403 リターン コードを使用して内部的にログに記録されます**。 これらのメッセージをフィルター処理することにより、ブロックされた要求のソース IP を確認できます。 「[Azure Cosmos DB 診断ログ](logging.md)」を参照してください。

### <a name="requests-from-subnet-with-service-endpoint-for-azure-cosmos-database-enabled"></a>Azure Cosmos データベース用のサービス エンドポイントが有効にされているサブネットからの要求
Azure Cosmos DB 用のサービス エンドポイントが有効にされている VNET 内のサブネットからの要求では、VNET およびサブネットの ID が Azure Cosmos アカウントに送信されます。 これらの要求は、ソースのパブリック IP を含んでいないので、IP フィルターによって拒否されます。 VNET 内の特定のサブネットからのアクセスを許可するには、[ご利用の Azure Cosmos アカウントへの仮想ネットワークおよびサブネット ベースのアクセスを構成する方法](how-to-configure-vnet-service-endpoint.md)に関するページに概説されている VNET アクセス制御リストを追加します。 ファイアウォール規則が適用されるまで、最大で 15 分かかる場合があります。


## <a name="next-steps"></a>次の手順

ご利用の Azure Cosmos DB アカウントに対して仮想ネットワーク サービス エンドポイントを構成するには、次の記事を参照してください。

* [ご利用の Azure Cosmos アカウントへの VNET およびサブネットのアクセス制御](vnet-service-endpoint.md)
* [ご利用の Azure Cosmos アカウントへの仮想ネットワークおよびサブネット ベースのアクセスを構成する方法](how-to-configure-vnet-service-endpoint.md)


