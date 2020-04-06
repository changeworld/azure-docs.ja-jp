---
title: 仮想ネットワークの Azure IoT Hub サポート
description: IoT Hub で仮想ネットワークの接続パターンを使用する方法
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: rezas
ms.openlocfilehash: 34f66c13b0e7eb7092332a48744f9abfd8f0db80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501441"
---
# <a name="iot-hub-support-for-virtual-networks"></a>仮想ネットワークの IoT Hub サポート

この記事では、VNET 接続パターンを紹介し、顧客所有の Azure VNET を介した IoT ハブへのプライベート接続エクスペリエンスを設定する方法について詳しく説明します。

> [!NOTE]
> この記事で説明されている IoT Hub 機能は、現在、次のリージョンの[マネージド サービス ID を使用して作成された](#create-an-iot-hub-with-managed-service-identity) IoT ハブでご利用いただけます: 米国東部、米国中南部、米国西部 2。


## <a name="introduction"></a>はじめに

既定では、IoT Hub ホスト名は、インターネット経由でパブリックにルーティング可能な IP アドレスを持つパブリック エンドポイントにマップされます。 次の図に示すように、この IoT Hub のパブリック エンドポイントは、異なる顧客が所有するハブ間で共有され、ワイドエリア ネットワークおよび同様にオンプレミス ネットワーク経由で IoT デバイスからアクセスできます。

[メッセージのルーティング](./iot-hub-devguide-messages-d2c.md)、[ファイルのアップロード](./iot-hub-devguide-file-upload.md)、[デバイスの一括インポートとエクスポート](./iot-hub-bulk-identity-mgmt.md)など、いくつかの IoT Hub 機能では同様に、パブリック エンドポイント経由での IoT Hub から顧客所有の Azure リソースへの接続が必要になります。 以下に示すように、IoT Hub から顧客リソースへのエグレス トラフィックは、これらの接続パスでまとめて構成されています。
![IoT Hub のパブリック エンドポイント](./media/virtual-network-support/public-endpoint.png)


いくつかの理由で、顧客は、自分が所有して操作する VNET を介した Azure リソース (IoT Hub を含む) への接続を制限したいと考える場合があります。 これらの理由には以下のものが含まれます。

* パブリック インターネット経由でのハブへの接続の露出を防ぐことで、IoT ハブのネットワーク レベルの分離による追加のセキュリティ層を導入する。

* オンプレミスのネットワーク資産からのプライベート接続エクスペリエンスを有効にし、データとトラフィックが Azure バックボーン ネットワークに直接送信されるようにする。

* 機微なオンプレミス ネットワークからの抜き取り攻撃を防ぐ。 

* [プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用して確立された Azure 全体の接続パターンに従う。


この記事では、IoT Hub から他の Azure リソースへのエグレス接続に Azure の信頼されたファースト パーティ サービスの例外を使用する場合と同様に、IoT Hub へのイングレス接続に[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使って、これらの目標を達成する方法について説明します。


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>プライベート エンドポイントを使用した IoT Hub へのイングレス接続

プライベート エンドポイントは、Azure リソースに到達可能な、顧客所有の VNET 内に割り当てられたプライベート IP アドレスです。 IoT ハブにプライベート エンドポイントを使用することで、IoT Hub のパブリック エンドポイントにトラフィックを送信することなく、VNET 内で動作するサービスが IoT Hub に到達できるようになります。 同様に、オンプレミスで動作するデバイスでは、[仮想プライベート ネットワーク (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) または [ExpressRoute](https://azure.microsoft.com/services/expressroute/) プライベート ピアリングを使用して、Azure で VNET に接続し、その後、(プライベート エンドポイント経由で) IoT ハブに接続することができます。 その結果、IoT ハブのパブリック エンドポイントへの接続を制限 (場合によっては、完全にブロック) したいと考えている顧客は、プライベート エンドポイントを使ってハブへの接続を維持しながら、[IoT Hub ファイアウォール規則](./iot-hub-ip-filtering.md)を使用して、この目標を達成できます。

> [!NOTE]
> この設定の主な対象は、オンプレミス ネットワーク内のデバイスです。 ワイドエリア ネットワークにデプロイされているデバイスには、この設定はお勧めできません。

![IoT Hub のパブリック エンドポイント](./media/virtual-network-support/virtual-network-ingress.png)

続行する前に、確実に次の前提条件が満たされているようにしてください。

* IoT ハブは、[マネージド サービス ID](#create-an-iot-hub-with-managed-service-identity) を使用してプロビジョニングする必要があります。

* IoT ハブは、[サポートされているリージョン](#regional-availability-private-endpoints)のいずれかでプロビジョニングする必要があります。

* プライベート エンドポイントが作成されるサブネットを持つ Azure VNET をプロビジョニング済みである。 詳細については、[Azure CLI を使用した仮想ネットワークの作成](../virtual-network/quick-create-cli.md)に関するページを参照してください。

* オンプレミス ネットワークの内部で動作するデバイスの場合は、[仮想プライベート ネットワーク (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)、または Azure VNET への [ExpressRoute](https://azure.microsoft.com/services/expressroute/) プライベート ピアリングを設定します。


### <a name="regional-availability-private-endpoints"></a>利用可能なリージョン (プライベート エンドポイント)

次のリージョンで作成された IoT Hub でサポートされるプライベート エンドポイント:

* 米国東部

* 米国中南部

* 米国西部 2


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>IoT Hub のイングレス用のプライベート エンドポイントを設定する

プライベート エンドポイントを設定するには、これらの手順に従います。

1. 次の Azure CLI コマンドを実行し、Azure IoT Hub プロバイダーをサブスクリプションに再登録します。

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. IoT Hub ポータルの **[プライベート エンドポイント接続]** タブ (このタブは[サポートされているリージョン](#regional-availability-private-endpoints)の IoT Hub 内でのみ使用可能) に移動し、 **[+]** 記号をクリックして、新しいプライベート エンドポイントを追加します。

3. サブスクリプション、リソース グループ、名前、および新しいプライベート エンドポイントの作成先となるリージョンを指定して、(理想的には、ハブと同じリージョンにプライベート エンドポイントを作成する必要があります。詳細については、[利用可能なリージョンのセクション](#regional-availability-private-endpoints)を参照してください)。

4. **[次へ: リソース]** をクリックし、IoT Hub リソースのサブスクリプションを指定し、リソースの種類として **"Microsoft.Devices/IotHubs"** 、**リソース**として IoT Hub の名前、ターゲット サブリソースとして **iotHub** を選択します。

5. **[次へ: 構成]** をクリックし、プライベート エンドポイントの作成先となる仮想ネットワークとサブネットを指定します。 必要に応じて、Azure プライベート DNS ゾーンと統合するオプションを選択します。

6. **[次へ: タグ]** をクリックし、必要に応じて、リソースのタグを指定します。

7. **[確認および作成]** をクリックして、プライベート エンドポイント リソースを作成します。


### <a name="pricing-private-endpoints"></a>価格 (プライベート エンドポイント)

料金の詳細については、「[Azure Private Link の料金](https://azure.microsoft.com/pricing/details/private-link)」をご覧ください。


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>IoT Hub から他の Azure リソースへのエグレス接続

IoT Hub には、[メッセージのルーティング](./iot-hub-devguide-messages-d2c.md)、[ファイルのアップロード](./iot-hub-devguide-file-upload.md)、[デバイスの一括インポートとエクスポート](./iot-hub-bulk-identity-mgmt.md) (通常はリソースのパブリック エンドポイントを介して行われる) のための Azure Blob Storage、Event Hubs、Service Bus リソースへのアクセスが必要です。 ストレージ アカウント、Event Hubs または Service Bus リソースを VNET にバインドした場合、既定では、推奨構成でリソースへの接続がブロックされます。 その結果、これらのリソースへのアクセスを必要とする IoT Hub の機能が妨げられます。

この状況を緩和するには、**Azure ファースト パーティの信頼されたサービス** オプションを使用して、IoT Hub リソースからストレージ アカウント、Event Hubs、または Service Bus リソースへの接続を有効にする必要があります。

前提条件は次のとおりです。

* IoT ハブは、[サポートされているリージョン](#regional-availability-trusted-microsoft-first-party-services)のいずれかでプロビジョニングする必要があります。

* IoT ハブには、ハブのプロビジョニング時にマネージド サービス ID を割り当てる必要があります。 [マネージド サービス ID を使用してハブを作成する](#create-an-iot-hub-with-managed-service-identity)方法に関する手順に従ってください。


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>利用可能なリージョン (信頼された Microsoft ファースト パーティ サービス)

Azure Storage、Event Hubs、Service Bus リソースに対するファイアウォールの制限を回避するための Azure の信頼されたファースト パーティ サービスの例外は、次のリージョンの IoT Hub でのみサポートされます。

* 米国東部

* 米国中南部

* 米国西部 2


### <a name="pricing-trusted-microsoft-first-party-services"></a>価格 (信頼された Microsoft ファースト パーティ サービス)

信頼された Microsoft ファースト パーティ サービスの例外機能は、[サポートされているリージョン](#regional-availability-trusted-microsoft-first-party-services)の IoT Hub で無料でご利用いただけます。 プロビジョニング済みのストレージ アカウント、Event Hubs、または Service Bus リソースの料金は個別に適用されます。


### <a name="create-an-iot-hub-with-managed-service-identity"></a>マネージド サービス ID を使用して IoT ハブを作成する

マネージド サービス ID は、リソースのプロビジョニング時にハブに割り当てることができます (この機能は現在、既存のハブではサポートされていません)。その場合、IoT ハブでは、TLS 1.2 を最小バージョンとして使用する必要があります。 そのため、以下の ARM リソース テンプレートを使用する必要があります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Devices/IotHubs",
      "apiVersion": "2020-03-01",
      "name": "<provide-a-valid-resource-name>",
      "location": "<any-of-supported-regions>",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "minTlsVersion": "1.2"
      },
      "sku": {
        "name": "<your-hubs-SKU-name>",
        "tier": "<your-hubs-SKU-tier>",
        "capacity": 1
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-02-01",
      "name": "updateIotHubWithKeyEncryptionKey",
      "dependsOn": [
        "<provide-a-valid-resource-name>"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "0.9.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2020-03-01",
              "name": "<provide-a-valid-resource-name>",
              "location": "<any-of-supported-regions>",
              "identity": {
                "type": "SystemAssigned"
              },
              "properties": {
                "minTlsVersion": "1.2"
              },
              "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
              }
            }
          ]
        }
      }
    }
  ]
}
```

リソースの `name`、`location`、`SKU.name`、`SKU.tier` の値を置き換えた後、以下の Azure CLI を使用して、既存のリソース グループにリソースをデプロイすることができます。

```azurecli-interactive
az group deployment create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

リソースが作成された後、以下の Azure CLI を使用して、ハブに割り当てられたマネージド サービス ID を取得できます。

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

マネージド サービス ID を持つ IoT Hub がプロビジョニングされたら、対応するセクションに従って、[ストレージ アカウント](#egress-connectivity-to-storage-account-endpoints-for-routing)、[Event Hubs](#egress-connectivity-to-event-hubs-endpoints-for-routing)、および [Service Bus](#egress-connectivity-to-service-bus-endpoints-for-routing) リソースにルーティング エンドポイントを設定するか、[ファイルのアップロード](#egress-connectivity-to-storage-accounts-for-file-upload)および[デバイスの一括インポートとエクスポート](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport)を構成します。


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>ルーティングのためのストレージ アカウント エンドポイントへのエグレス接続

IoT Hub は、顧客所有のストレージ アカウントにメッセージをルーティングするように構成できます。 ファイアウォールの制限が適用されているときに、ルーティング機能でストレージ アカウントにアクセスできるようにするには、IoT Hub にマネージド サービス ID が必要です ([マネージド サービス ID を使用してハブを作成する](#create-an-iot-hub-with-managed-service-identity)方法に関するセクションを参照)。 マネージド サービス ID がプロビジョニングされたら、以下の手順に従って、ストレージ アカウントにアクセスするためにハブのリソース ID に RBAC アクセス許可を付与します。

1. Azure portal で、ストレージ アカウントの **[アクセス制御 (IAM)]** タブに移動し、 **[ロールの割り当てを追加する]** セクションの下にある **[追加]** をクリックします。

2. **ロール**として **[ストレージ BLOB データ共同作成者]** 、**アクセスの割当先**として **[Azure AD のユーザー、グループ、サービス プリンシパル]** を選んでから、ドロップダウン リストで IoT Hub のリソース名を選択します。 **[保存]** ボタンをクリックします。

3. ストレージ アカウントの **[ファイアウォールと仮想ネットワーク]** タブに移動し、 **[Allow access from selected networks]\(選択したネットワークからのアクセスを許可する\)** オプションを有効にします。 **[例外]** 一覧で、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** のボックスをオンにします。 **[保存]** ボタンをクリックします。

4. IoT Hub のリソース ページで、 **[メッセージ ルーティング]** タブに移動します。

5. **[カスタム エンドポイント]** セクションに移動し、 **[追加]** をクリックします。 エンドポイントの種類として、 **[ストレージ]** を選択します。

6. 表示されるページで、エンドポイントの名前を指定し、BLOB ストレージで使用する予定のコンテナーを選択して、エンコード、およびファイル名の形式を指定します。 ストレージ エンドポイントに対する**認証の種類**として、 **[システム割り当て済み]**  を選択します。 **[作成]** ボタンをクリックします。

これで、カスタム ストレージ エンドポイントが、ハブのシステム割り当て ID を使用するように設定され、ファイアウォールの制限に関係なく、ストレージ リソースにアクセスするためのアクセス許可が付与されました。 このエンドポイントを使用して、ルーティング規則を設定できるようになりました。


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>ルーティングのための Event Hubs エンドポイントへのエグレス接続

IoT Hub は、顧客所有の Event Hubs 名前空間にメッセージをルーティングするように構成できます。 ファイアウォールの制限が適用されているときに、ルーティング機能で Event Hubs リソースにアクセスできるようにするには、IoT Hub にマネージド サービス ID が必要です ([マネージド サービス ID を使用してハブを作成する](#create-an-iot-hub-with-managed-service-identity)方法に関するセクションを参照)。 マネージド サービス ID がプロビジョニングされたら、以下の手順に従って、Event Hubs にアクセスするためにハブのリソース ID に RBAC アクセス許可を付与します。

1. Azure portal で、Event Hubs の **[アクセス制御 (IAM)]** タブに移動し、 **[ロールの割り当てを追加する]** セクションの下にある **[追加]** をクリックします。

2. **ロール**として **[Event Hubs Data Sender]\(Event Hubs のデータ送信者\)** 、**アクセスの割当先**として **[Azure AD のユーザー、グループ、サービス プリンシパル]** を選んでから、ドロップダウン リストで IoT Hub のリソース名を選択します。 **[保存]** ボタンをクリックします。

3. Event Hubs の **[ファイアウォールと仮想ネットワーク]** タブに移動し、 **[Allow access from selected networks]\(選択したネットワークからのアクセスを許可する\)** オプションを有効にします。 **[例外]** 一覧で、 **[Allow trusted Microsoft services to access event hubs]\(信頼された Microsoft サービスによる Event Hubs に対するアクセスを許可します\)** のボックスをオンにします。 **[保存]** ボタンをクリックします。

4. IoT Hub のリソース ページで、 **[メッセージ ルーティング]** タブに移動します。

5. **[カスタム エンドポイント]** セクションに移動し、 **[追加]** をクリックします。 エンドポイントの種類として、 **[Event Hubs]** を選択します。

6. 表示されたページで、エンドポイントの名前を指定し、Event Hubs の名前空間とインスタンスを選択して、 **[作成]** ボタンをクリックします。

これで、カスタム Event Hubs エンドポイントが、ハブのシステム割り当て ID を使用するように設定され、ファイアウォールの制限に関係なく、Event Hubs リソースにアクセスするためのアクセス許可が付与されました。 このエンドポイントを使用して、ルーティング規則を設定できるようになりました。


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>ルーティングのための Service Bus エンドポイントへのエグレス接続

IoT Hub は、顧客所有の Service Bus 名前空間にメッセージをルーティングするように構成できます。 ファイアウォールの制限が適用されているときに、ルーティング機能で Service Bus リソースにアクセスできるようにするには、IoT Hub にマネージド サービス ID が必要です ([マネージド サービス ID を使用してハブを作成する](#create-an-iot-hub-with-managed-service-identity)方法に関するセクションを参照)。 マネージド サービス ID がプロビジョニングされたら、以下の手順に従って、Service Bus にアクセスするためにハブのリソース ID に RBAC アクセス許可を付与します。

1. Azure portal で、Service Bus の **[アクセス制御 (IAM)]** タブに移動し、 **[ロールの割り当てを追加する]** セクションの下にある **[追加]** をクリックします。

2. **ロール**として **[Service bus Data Sender]\(Service Bus のデータ送信者\)** 、**アクセスの割当先**として **[Azure AD のユーザー、グループ、サービス プリンシパル]** を選んでから、ドロップダウン リストで IoT Hub のリソース名を選択します。 **[保存]** ボタンをクリックします。

3. Service Bus の **[ファイアウォールと仮想ネットワーク]** タブに移動し、 **[Allow access from selected networks]\(選択したネットワークからのアクセスを許可する\)** オプションを有効にします。 **[例外]** 一覧で、 **[Allow trusted Microsoft services to access this service bus]\(信頼された Microsoft サービスによるこの Service Bus に対するアクセスを許可します\)** のボックスをオンにします。 **[保存]** ボタンをクリックします。

4. IoT Hub のリソース ページで、 **[メッセージ ルーティング]** タブに移動します。

5. **[カスタム エンドポイント]** セクションに移動し、 **[追加]** をクリックします。 エンドポイントの種類として、 **[Service Bus キュー]** または **[Service Bus トピック]** (該当する場合) を選択します。

6. 表示されたページで、エンドポイントの名前を指定し、Service Bus の名前空間とキューまたはトピック (該当する場合) を選択します。 **[作成]** ボタンをクリックします。

これで、カスタム Service Bus エンドポイントが、ハブのシステム割り当て ID を使用するように設定され、ファイアウォールの制限に関係なく、Service Bus リソースにアクセスするためのアクセス許可が付与されました。 このエンドポイントを使用して、ルーティング規則を設定できるようになりました。


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>ファイルのアップロードのためのストレージ アカウントへのエグレス接続

IoT Hub のファイルのアップロード機能を使用すると、デバイスで顧客所有のストレージ アカウントにファイルをアップロードできます。 ファイルのアップロードを機能させるには、デバイスと IoT Hub の両方をストレージ アカウントに接続する必要があります。 ストレージ アカウントにファイアウォールの制限が適用されている場合、デバイスでは、サポートされているストレージ アカウントのいずれかのメカニズム ([プライベート エンドポイント](../private-link/create-private-endpoint-storage-portal.md)、[サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)または[ファイアウォールのダイレクト構成](../storage/common/storage-network-security.md)を含む) を使用して接続する必要があります。 同様に、ストレージ アカウントにファイアウォールの制限が適用されている場合は、信頼された Microsoft サービスの例外を使用して、ストレージ リソースにアクセスするように IoT Hub を構成する必要があります。 そのため、IoT Hub にはマネージド サービス ID が必要です (「[マネージド サービス ID を使用してハブを作成する](#create-an-iot-hub-with-managed-service-identity)」を参照)。 マネージド サービス ID がプロビジョニングされたら、以下の手順に従って、ストレージ アカウントにアクセスするためにハブのリソース ID に RBAC アクセス許可を付与します。

1. Azure portal で、ストレージ アカウントの **[アクセス制御 (IAM)]** タブに移動し、 **[ロールの割り当てを追加する]** セクションの下にある **[追加]** をクリックします。

2. **ロール**として **[ストレージ BLOB データ共同作成者]** 、**アクセスの割当先**として **[Azure AD のユーザー、グループ、サービス プリンシパル]** を選んでから、ドロップダウン リストで IoT Hub のリソース名を選択します。 **[保存]** ボタンをクリックします。

3. ストレージ アカウントの **[ファイアウォールと仮想ネットワーク]** タブに移動し、 **[Allow access from selected networks]\(選択したネットワークからのアクセスを許可する\)** オプションを有効にします。 **[例外]** 一覧で、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** のボックスをオンにします。 **[保存]** ボタンをクリックします。

4. IoT Hub のリソース ページで、 **[ファイルのアップロード]** タブに移動します。

5. 表示されたページで、BLOB ストレージで使用する予定のコンテナーを選択し、 **[ファイル通知の設定]** 、 **[SAS TTL]** 、 **[既定の TTL]** および **[最大配信回数]** を必要に応じて構成します。 ストレージ エンドポイントに対する**認証の種類**として、 **[システム割り当て済み]**  を選択します。 **[作成]** ボタンをクリックします。

これで、ファイルのアップロード用のストレージ エンドポイントが、ハブのシステム割り当て ID を使用するように設定され、ファイアウォールの制限に関係なく、ストレージ リソースにアクセスするためのアクセス許可が付与されました。


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>デバイスの一括インポートとエクスポートのためのストレージ アカウントへのエグレス接続

IoT Hub では、顧客指定のストレージ BLOB 間で、デバイスの情報を一括で[インポートおよびエクスポートする](./iot-hub-bulk-identity-mgmt.md)機能がサポートされています。 一括インポートとエクスポートを機能させるには、デバイスと IoT Hub の両方でストレージ アカウントに接続する必要があります。

この機能では、IoT Hub からストレージ アカウントへの接続が必要です。 ファイアウォールの制限が適用されているときに、Service Bus リソースにアクセスするには、IoT Hub にマネージド サービス ID が必要です ([マネージド サービス ID を使用してハブを作成する](#create-an-iot-hub-with-managed-service-identity)方法に関するセクションを参照)。 マネージド サービス ID がプロビジョニングされたら、以下の手順に従って、Service Bus にアクセスするためにハブのリソース ID に RBAC アクセス許可を付与します。

1. Azure portal で、ストレージ アカウントの **[アクセス制御 (IAM)]** タブに移動し、 **[ロールの割り当てを追加する]** セクションの下にある **[追加]** をクリックします。

2. **ロール**として **[ストレージ BLOB データ共同作成者]** 、**アクセスの割当先**として **[Azure AD のユーザー、グループ、サービス プリンシパル]** を選んでから、ドロップダウン リストで IoT Hub のリソース名を選択します。 **[保存]** ボタンをクリックします。

3. ストレージ アカウントの **[ファイアウォールと仮想ネットワーク]** タブに移動し、 **[Allow access from selected networks]\(選択したネットワークからのアクセスを許可する\)** オプションを有効にします。 **[例外]** 一覧で、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** のボックスをオンにします。 **[保存]** ボタンをクリックします。

これで、[インポートとエクスポート ジョブの作成](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) (一括インポートとエクスポート機能を使用する方法については、こちらを参照してください) に Azure IoT REST API を使用できるようになりました。 要求本文に `storageAuthenticationType="identityBased"` を指定し、ストレージ アカウントの入力および出力 URL として `inputBlobContainerUri="https://..."` と `outputBlobContainerUri="https://..."` をそれぞれ使用する必要があることにご注意ください。


Azure IoT Hub SDK では、サービス クライアントのレジストリ マネージャーでもこの機能がサポートされます。 次のコード スニペットでは、C# SDK を使用してインポート ジョブまたはエクスポート ジョブを開始する方法を示しています。

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```


C#、Java、および Node.js 用の仮想ネットワークをサポートする Azure IoT SDK のこのリージョン限定バージョンを使用するには、次のようにします。

1. `EnableStorageIdentity` という名前の環境変数を作成し、その値を `1` に設定します。

2. 次のいずれかの SDK をダウンロードします: [Java](https://aka.ms/vnetjavasdk) | [C#](https://aka.ms/vnetcsharpsdk) | [Node.js](https://aka.ms/vnetnodesdk)
 
Python の場合は、GitHub から限定バージョンをダウンロードします。

1. [GitHub リリース ページ](https://aka.ms/vnetpythonsdk)に移動します。

2. 次のファイルをダウンロードします。これは、リリース ページの下部にある **assets** という名前のヘッダーの下にあります。
    > *azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl*

3. ターミナルを開き、ダウンロードされたファイルがあるフォルダーに移動します。

4. 次のコマンドを実行し、仮想ネットワークがサポートされる Python Service SDK をインストールします。
    > pip install ./azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl


## <a name="next-steps"></a>次のステップ

IoT Hub の機能の詳細について、下記のリンク使用してください。

* [メッセージ ルーティング](./iot-hub-devguide-messages-d2c.md)
* [ファイルのアップロード](./iot-hub-devguide-file-upload.md)
* [デバイスの一括デインポートとエクスポート](./iot-hub-bulk-identity-mgmt.md) 
