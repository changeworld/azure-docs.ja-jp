---
title: Azure Cosmos DB のファイアウォール サポートと IP アクセス制御 | Microsoft Docs
description: Azure Cosmos DB データベース アカウントで IP アクセス制御ポリシーを使用したファイアウォールを実現する方法について説明します。
keywords: IP アクセス制御, ファイアウォール サポート
services: cosmos-db
author: kanshiG
manager: kfile
tags: azure-resource-manager
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: govindk
ms.openlocfilehash: 7c9367cccf8d59d60dfa474f02567d59b9c8c8c2
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038421"
---
# <a name="azure-cosmos-db-firewall-support"></a>Azure Cosmos DB のファイアウォール サポート
Azure Cosmos DB データベース アカウントに格納されているデータを保護するために、Azure Cosmos DB は、強力なハッシュベースのメッセージ認証コード (HMAC) を利用したシークレット ベースの[承認モデル](https://msdn.microsoft.com/library/azure/dn783368.aspx)を備えています。 シークレット ベースの承認モデルに加え、Azure Cosmos DB は現在、ポリシーに基づく IP ベースのアクセス制御を使った受信ファイアウォールにも対応しています。 このモデルは、従来型データベース システムのファイアウォール規則に似ていますが、Azure Cosmos DB データベース アカウントのセキュリティ水準がさらに高くなっています。 このモデルによって今後は、承認されているコンピューターのグループやクラウド サービスからのみアクセスできるように Azure Cosmos DB データベース アカウントを構成することができます。 ただし承認されているコンピューターのグループやサービスから Azure Cosmos DB リソースにアクセスするためには、呼び出し側が有効な承認トークンを提示する必要がある点は変わりません。

> [!NOTE]
> ファイアウォールのサポートは、現在、Azure Cosmos DB SQL API アカウントと Mongo API アカウントで利用できます。 その他の API やソブリン クラウド (Azure Germany、Azure Government など) 用にファイアウォールを構成する機能は、間もなく利用できるようになります。 既存の IP ファイアウォールが構成されている Azure Cosmos DB アカウントのためにサービス エンドポイント ACL を構成しようとしている場合は、ファイアウォールの構成を書き留め、IP ファイアウォールを削除してから、サービス エンドポイント ACL を構成してください。 サービス エンドポイントを構成した後、必要な場合は IP ファイアウォールを再度有効にすることができます。

## <a name="ip-access-control-overview"></a>IP アクセス制御の概要
既定では、有効な承認トークンと共に要求が送信されれば、Azure Cosmos DB データベース アカウントにパブリック インターネットからアクセスすることができます。 IP ポリシー ベースのアクセス制御を構成するためには、特定のデータベース アカウントのクライアント IP の許可リストとして追加する一連の IP アドレスまたは IP アドレス範囲を CIDR 形式でユーザーが指定する必要があります。 この構成を適用すると、この許可リストにないマシンを発信元とする要求はすべて、サーバーによってブロックされます。  以下の図は、IP ベースのアクセス制御における接続処理フローを表したものです。

![IP ベースのアクセス制御における接続プロセスの図](./media/firewall-support/firewall-support-flow.png)

## <a id="configure-ip-policy"></a> IP アクセス制御ポリシーの構成
IP アクセス制御ポリシーは、Azure Portal で設定するか、[Azure CLI](cli-samples.md)、[Azure Powershell](powershell-samples.md)、または [REST API](/rest/api/cosmos-db/) を使用して **ipRangeFilter** プロパティを更新することでプログラムによって設定できます。 

Azure portal で IP アクセス制御ポリシーを設定するには、Azure Cosmos DB アカウント ページに移動し、ナビゲーション メニューの **[ファイアウォールと仮想マシン]** をクリックして、**[許可するアクセス元]** の値を **[選択されたネットワーク]** に変更して、**[保存]** をクリックします。 

![Azure Portal で [ファイアウォール] ページを開く方法を示すスクリーンショット](./media/firewall-support/azure-portal-firewall.png)

IP アクセス制御をオンにすると、ポータルでは、IP アドレスと範囲を指定でき、他の Azure サービスおよび Azure Portal へのアクセスを有効にするスイッチも提供されます。 これらのスイッチの詳細については、次のセクションを参照してください。

> [!NOTE]
> Azure Cosmos DB データベース アカウントの IP アクセス制御ポリシーを有効にすると、構成されている許可リストの IP アドレス範囲に該当しないコンピューターからのアクセスがすべてブロックされます。 このモデルにより、ポータルからのデータ プレーン操作の閲覧もブロックされ、アクセス制御の整合性が確保されます。

## <a name="connections-from-the-azure-portal"></a>Azure Portal からの接続 

IP アクセス制御ポリシーをプログラムで有効にする場合は、アクセスを維持するために、Azure Portal の IP アドレスを **ipRangeFilter** プロパティに追加する必要があります。 ポータルの IP アドレスは次のとおりです。

|リージョン|IP アドレス|
|------|----------|
|以下を除くすべてのリージョン|104.42.195.92、40.76.54.131、52.176.6.30、52.169.50.45、52.187.184.26|
|ドイツ|51.4.229.218|
|中国|139.217.8.252|
|米国政府|52.244.48.71|

Azure Portal でファイアウォールの設定を **[選択されたネットワーク]** に変更すると、Azure Portal へのアクセスが既定で有効になります。 

![Azure Portal へのアクセスを有効にする方法を示すスクリーンショット](./media/firewall-support/enable-azure-portal.png)

## <a name="connections-from-global-azure-datacenters-or-azure-paas-services"></a>グローバル Azure データセンターまたは Azure PaaS サービスからの接続
Azure では、Azure Stream Analytics、Azure Functions、Azure App Service などの PaaS サービスが、Azure Cosmos DB と組み合わせて使用されます。 IP アドレスが簡単に利用できないこれらのサービスから Azure Cosmos DB データベース アカウントにアクセスできるようにするには、プログラムで、Azure Cosmos DB データベース アカウントに関連付けられている IP アドレスの許可リストに、IP アドレス 0.0.0.0 を追加します。 

Azure portal でファイアウォールの設定を **[選択されたネットワーク]** に変更すると、グローバル Azure データセンター内からの接続へのアクセスが既定で有効になります。 

![Azure Portal で [ファイアウォール] ページを開く方法を示すスクリーンショット](./media/firewall-support/enable-azure-services.png)

## <a name="connections-from-your-current-ip"></a>現在の IP からの接続

開発をシンプルにするために、Azure Portal では、クライアント コンピューターの IP アドレスを識別して許可リストに追加することができます。これにより、コンピューターで実行中のアプリが Azure Cosmos DB アカウントにアクセスすることができます。 ここでは、クライアント IP アドレスは、ポータルから見た IP アドレスとして検出されます。 コンピューターのクライアント IP アドレスである可能性がありますが、ネットワーク ゲートウェイの IP アドレスである可能性もあります。 運用環境にそれを移行する前に削除することを忘れないでください。

現在の IP を有効にするには、**[現在の IP を追加する]** を選んで IP アドレスの一覧に現在の IP アドレスを追加してから、**[保存]** をクリックします。

![現在の IP のファイアウォール設定を構成する方法を示すスクリーンショット](./media/firewall-support/enable-current-ip.png)

## <a name="connections-from-cloud-services"></a>クラウド サービスからの接続
Azure では、Azure Cosmos DB を使用して中間層のサービス ロジックをホストする手段としてクラウド サービスがよく利用されます。 クラウド サービスから Azure Cosmos DB データベース アカウントにアクセスできるようにするには、[IP アクセス制御ポリシーを構成する](#configure-ip-policy)ことで、ご使用の Azure Cosmos DB データベース アカウントに関連付けられている IP アドレスの許可リストにクラウド サービスのパブリック IP アドレスを追加する必要があります。 これにより、クラウド サービスのすべてのロール インスタンスからその Azure Cosmos DB データベース アカウントにアクセスできるようになります。 クラウド サービスの IP アドレスは Azure Portal で確認できます。次のスクリーンショットをご覧ください。

![Azure Portal に表示されるクラウド サービスのパブリック IP アドレスを示すスクリーンショット](./media/firewall-support/public-ip-addresses.png)

新たに (複数の) ロール インスタンスを追加してクラウド サービスをスケールアウトした場合、それらのインスタンスは、同じクラウド サービスに属すことになるので、Azure Cosmos DB データベース アカウントへのアクセス権が自動的に割り当てられます。

## <a name="connections-from-virtual-machines"></a>仮想マシンからの接続
Azure Cosmos DB を使用する中間層サービスのホスティングには、[仮想マシン](https://azure.microsoft.com/services/virtual-machines/)または[仮想マシン スケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)を使用することもできます。  仮想マシンからのアクセスを許可するように Azure Cosmos DB データベース アカウントを構成するには、[IP アクセス制御ポリシーを構成する](#configure-ip-policy)ことで、仮想マシンや仮想マシン スケール セットのパブリック IP アドレスを、ご使用の Azure Cosmos DB データベース アカウントで許可された IP アドレスとして構成する必要があります。 仮想マシンの IP アドレスは Azure Portal で確認できます。次のスクリーンショットを参照してください。

![Azure Portal に表示される仮想マシンのパブリック IP アドレスを示すスクリーンショット](./media/firewall-support/public-ip-addresses-dns.png)

このグループに新たに仮想マシン インスタンスを追加した場合、それらのインスタンスにも自動的に Azure Cosmos DB データベース アカウントへのアクセス権が割り当てられます。

## <a name="connections-from-the-internet"></a>インターネットからの接続
インターネット上のコンピューターから Azure Cosmos DB データベース アカウントにアクセスするときは、そのコンピューターのクライアント IP アドレスまたは IP アドレス範囲を Azure Cosmos DB データベース アカウントの IP アドレスの許可リストに追加する必要があります。 

## <a name="using-azure-resource-manager-template-to-set-up-the-ip-access-control"></a>Azure Resource Manager テンプレートを使用して IP アクセス制御を設定する

次の JSON をテンプレートに追加し、IP アクセス制御を設定します。 アカウントの Resource Manager テンプレートに ipRangeFilter 属性が与えられます。この属性はホワイトリストに登録する IP 範囲の一覧です。

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
     "ipRangeFilter":"10.0.0.1,10.0.0.2,183.240.196.255"
   }
   }
```

## <a name="troubleshooting-the-ip-access-control-policy"></a>IP アクセス制御ポリシーのトラブルシューティング
### <a name="portal-operations"></a>ポータルの操作
Azure Cosmos DB データベース アカウントの IP アクセス制御ポリシーを有効にすると、構成されている許可リストの IP アドレス範囲に該当しないコンピューターからのアクセスがすべてブロックされます。 そのため、コンテナーの参照やドキュメントのクエリなどのポータルのデータ プレーン操作を有効にする場合は、ポータルの **[ファイアウォール]** ページを使用して Azure Portal へのアクセスを明示的に許可する必要があります。 

### <a name="sdk--rest-api"></a>SDK と Rest API
許可リストに追加されていないコンピューターから SDK または REST API 経由でアクセスした場合、セキュリティ上の理由から、詳しい情報を含まない汎用的な 404 Not Found 応答が返されます。 お客様の Azure Cosmos DB データベース アカウント用に構成されている IP 許可リストを確認して、そのアカウントに適切なポリシー構成が適用されていることを確認してください。

## <a name="next-steps"></a>次の手順
ネットワークに関連したパフォーマンス上のヒントについては、[パフォーマンスに関するヒント](performance-tips.md)のページを参照してください。

