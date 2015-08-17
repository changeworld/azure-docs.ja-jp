<properties 
   pageTitle="ネットワーク リソース プロバイダー"
   description="ネットワーク リソース プロバイダー"
   services="azure-portal"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="azure-portal"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# ネットワーク リソース プロバイダー
今日のビジネスの成功を支えるニーズとは、大規模なネットワーク対応アプリケーションを俊敏、柔軟、安全、かつ反復可能な方法でビルドおよび管理する能力です。Azure リソース マネージャー (ARM) では、リソース グループ内の 1 つのリソース コレクションとして、このようなアプリケーションを作成することができます。このようなリソースは、ARM の下でさまざまなリソース プロバイダーを介して管理されています。

Azure リソース マネージャー (ARM) では、リソース グループ内の 1 つのリソース コレクションとして、このようなアプリケーションおよび関連するネットワーク リソースを作成することができます。アプリケーションとネットワーク リソースは、ARM リソース グループ内の 1 つの単位として実行されます。

次の管理インターフェイスのいずれかを使用して、ネットワーク リソースを管理できます。

- REST ベースの API
- PowerShell
- .NET SDK
- Node.JS SDK
- Java SDK
- Azure CLI
- Azure ポータル
- ARM テンプレートの言語

ネットワーク リソース プロバイダーの導入により、次の利点を活用することができます。

- **メタデータ** – タグを使用してリソースに情報を追加できます。これらのタグは、リソース グループとサブスクリプションのリソース使用率を追跡するために使用されます。
- **ネットワークの制御の拡大** – ネットワーク リソースは疎結合され、より細かく制御することができます。つまり、ネットワーク リソースを管理する柔軟性が高まります。
- **構成の高速化** – ネットワーク リソースは疎結合されているため、ネットワーク リソースの作成と調整を並行して実行できます。これにより、構成にかかる時間が大幅に減少しました。
- **ロール ベースのアクセス制御** – RBAC は、特定のセキュリティのスコープを持つ既定のロールを備えているだけでなく、セキュリティで保護された管理用のカスタム ロールを作成できます。 
- **管理とデプロイメントの簡略化** – アプリケーション全体のスタックを、リソース グループ内の 1 つのリソース コレクションとして作成できるため、アプリケーションのデプロイと管理が簡単になります。テンプレートの JSON ペイロードを用意するだけでデプロイできるため、デプロイにかかる時間が短縮されます。
- **迅速なカスタマイズ** – 宣言型形式のテンプレートを使用して、反復可能な方法で迅速にデプロイメントをカスタマイズできます。 
- **反復可能なカスタマイズ** – 宣言型形式のテンプレートを使用して、反復可能な方法で迅速にデプロイメントをカスタマイズできます。

## ネットワーク リソース 
ネットワーク リソースを、1 つのコンピューティング リソース (仮想マシン) を介して管理するのではなく、独立して管理できるようになりました。これにより、リソース グループに複雑かつ大規模なインフラストラクチャを構成する際により高度な柔軟性と俊敏性が保証されます。
 
次の図は、ネットワーク リソース モデルとその関係の概要を示しています。最上位のリソースは、青色の輪郭が付けられています。最上位のリソースに加えて、子リソースはグレーの輪郭付きで表示されます。各リソースを個別に管理することができます。

![ネットワーク リソース モデル](./media/resource-group-networking/Figure1.png)

複数の層のアプリケーションに関連するサンプルのデプロイメントの概念図を次に示します。すべてのネットワーク リソースは、青色の輪郭が付けられています。

![ネットワーク リソース モデル](./media/resource-group-networking/Figure2.png)

## REST API 
前述のように、ネットワーク リソースは、さまざまなインターフェイス (REST API、NET SDK、Node.JS SDK、Java SDK、PowerShell、CLI、Azure ポータルおよびテンプレートなど) を経由して管理できます。

REST API は、HTTP 1.1 プロトコル仕様に準拠しています。API の一般的な URI の構造は次のとおりです。

	https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

中かっこ付きのパラメーターは、次の要素を表します。

- **subscription-id** – Azure のサブスクリプション ID。
- **resource-provider-namespace** – 使用中のプロバイダーの名前空間。ネットワーク リソース プロバイダーの値は *Microsoft.Network* です。
- **region-name** – Azure リージョン名。

REST API を呼び出す際に、次の HTTP メソッドがサポートされます。

- **PUT** – 指定された型のリソースの作成、リソース プロパティの変更、またはリソース間の関連付けの変更に使用されます。 
- **GET** – プロビジョニングされているリソースの情報の取得に使用されます。
- **DELETE** – 既存のリソースの削除に使用されます。

要求と応答はいずれも JSON ペイロードの形式に準拠しています。詳細については、「[Azure リソース管理 API](https://msdn.microsoft.com/library/azure/dn948464.aspx)」を参照してください。

## ARM テンプレートの言語
(API または SDK を使って) 命令型でリソースを管理するだけでなく、ARM テンプレート言語を使用することで、宣言型のプログラミング形式によってネットワーク リソースをビルドおよび管理することもできます。

テンプレートのサンプル表現を次に示します –

	{
	  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	  "contentVersion": "<version-number-of-template>",
	  "parameters": { <parameter-definitions-of-template> },
	  "variables": { <variable-definitions-of-template> },
	  "resources": [ { <definition-of-resource-to-deploy> } ],
	  "outputs": { <output-of-template> }    
	}

テンプレートとは基本的に言ってリソースおよびインスタンス値をパラメーターで挿入した JSON による記述です。次の例を使用すると、2 つのサブネットを含む仮想ネットワークを作成できます。

	{
	    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
	    "contentVersion": "1.0.0.0",
	    "parameters" : {
	      "location": {
	        "type": "String",
	        "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
	        "metadata" : {
	          "Description" : "Deployment location"
	        }
	      },
	      "virtualNetworkName":{
	        "type" : "string",
	        "defaultValue":"myVNET",
	        "metadata" : {
	          "Description" : "VNET name"
	        }
	      },
	      "addressPrefix":{
	        "type" : "string",
	        "defaultValue" : "10.0.0.0/16",
	        "metadata" : {
	          "Description" : "Address prefix"
	        }
	
	      },
	      "subnet1Name": {
	        "type" : "string",
	        "defaultValue" : "Subnet-1",
	        "metadata" : {
	          "Description" : "Subnet 1 Name"
	        }
	      },
	      "subnet2Name": {
	        "type" : "string",
	        "defaultValue" : "Subnet-2",
	        "metadata" : {
	          "Description" : "Subnet 2 name"
	        }
	      },
	      "subnet1Prefix" : {
	        "type" : "string",
	        "defaultValue" : "10.0.0.0/24",
	        "metadata" : {
	          "Description" : "Subnet 1 Prefix"
	        }
	      },
	      "subnet2Prefix" : {
	        "type" : "string",
	        "defaultValue" : "10.0.1.0/24",
	        "metadata" : {
	          "Description" : "Subnet 2 Prefix"
	        }
	      }
	    },
	    "resources": [
	    {
	      "apiVersion": "2015-05-01-preview",
	      "type": "Microsoft.Network/virtualNetworks",
	      "name": "[parameters('virtualNetworkName')]",
	      "location": "[parameters('location')]",
	      "properties": {
	        "addressSpace": {
	          "addressPrefixes": [
	            "[parameters('addressPrefix')]"
	          ]
	        },
	        "subnets": [
	          {
	            "name": "[parameters('subnet1Name')]",
	            "properties" : {
	              "addressPrefix": "[parameters('subnet1Prefix')]"
	            }
	          },
	          {
	            "name": "[parameters('subnet2Name')]",
	            "properties" : {
	              "addressPrefix": "[parameters('subnet2Prefix')]"
	            }
	          }
	        ]
	      }
	    }
	    ]
	}

テンプレートを使用する場合は、パラメーター値を手動で提供するオプションか、パラメーター ファイルを使用するオプションがあります。次の例は、上記のテンプレートで使用可能なパラメーター値のセットを示しています。

	{
	  "location": {
	      "value": "East US"
	  },
	  "virtualNetworkName": {
	      "value": "VNET1"
	  },
	  "subnet1Name": {
	      "value": "Subnet1"
	  },
	  "subnet2Name": {
	      "value": "Subnet2"
	  },
	  "addressPrefix": {
	      "value": "192.168.0.0/16"
	  },
	  "subnet1Prefix": {
	      "value": "192.168.1.0/24"
	  },
	  "subnet2Prefix": {
	      "value": "192.168.2.0/24"
	  }
	}


テンプレートを使用する主な利点は、次のとおりです。

- 宣言型形式でリソース グループに複雑なインフラストラクチャをビルドできます。依存関係の管理など、リソースを作成するオーケストレーションは、ARM によって処理されます。 
- インフラストラクチャは、パラメーターを変更するだけで、さまざまなリージョンで、あるいは同一リージョン内で、繰り返し作成することができます。 
- 宣言型形式では、テンプレートの作成とインフラストラクチャの展開におけるリード タイムを短縮できます。 

テンプレートのサンプルについては、「[Azure のクイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates)」を参照してください。

ARM テンプレート言語の詳細については、「[Azure リソース マネージャーのテンプレート言語](https://msdn.microsoft.com/library/azure/dn835138.aspx)」を参照してください。

上記のサンプル テンプレートでは、仮想ネットワークとサブネットのリソースを使用します。その他の使用可能なネットワーク リソースは、次の一覧のとおりです。

## NIC
ネットワーク インターフェイス カード (NIC) は、仮想マシン (VM) に関連するネットワーク インターフェイスを表します。VM には、1 つ以上の NIC を含めることができます。

![1 つの VM での NIC](./media/resource-group-networking/Figure3.png)

NIC のリソースの主なプロパティは次のとおりです。

- IP 設定

NIC は次のネットワーク リソースとも関連付けられています。

- ネットワーク セキュリティ グループ (NSG) 
- Load Balancer

## Virtual Network とサブネット
Virtual Network (VNET) とサブネットは、Azure で実行するワークロードにセキュリティの境界を定義する際に役立ちます。VNET は、アドレス空間 (CIDR ブロックともいう) を特徴としています。

サブネットは、VNET の子リソースで、IP アドレスのプレフィックスを使用して、CIDR ブロック内のアドレス スペースのセグメントの定義に役立ちます。さまざまなワークロードを実行する VM は、基本的にサブネットの境界内で動作します。

![1 つの VM での NIC](./media/resource-group-networking/Figure4.png)

VNET のリソースの主なプロパティは次のとおりです。

- IP アドレス空間 (CIDR ブロック) 
- VNET 名
- サブネット

VNET は次のネットワーク リソースとも関連付けられています。

- VPN Gateway

サブネットの主なプロパティは次のとおりです。

- IP アドレス プレフィックス
- サブネット名

サブネットは次のネットワーク リソースとも関連付けられています。

- NSG

## Load Balancer
ロード バランサーは、アプリケーションの拡大/縮小を行う際に使用します。一般的なデプロイメントのシナリオには、複数の VM インスタンスで実行するアプリケーションなどがあります。VM インスタンスは、ネットワーク トラフィックをさまざまなインスタンスに分散するロード バランサーを介して動作します。

![1 つの VM での NIC](./media/resource-group-networking/Figure5.png)

ロード バランサーには、次の子リソースが含まれます。

- **フロント エンド IP 構成** – ロード バランサーには、1 つ以上のフロント エンド IP アドレス、あるいは仮想 IP (VIP) と呼ばれるものを含めることができます。これらの IP アドレスは、受信トラフィック用として機能します。 
- **バックエンド アドレス プール** – これらは、負荷分散される VM の NIC に関連付けられた IP アドレスです。
- **負荷分散規則** – 規則のプロパティは、特定のフロント エンド IP とポートの組み合わせを、バック エンド IP アドレスとポートの組み合わせのセットにマップします。ロード バランサーのリソースの 1 つの定義によって複数の負荷分散規則を定義できます。各規則は、フロント エンド IP とポートと、VM に関連付けられたバック エンド IP とポートとの組み合わせを反映します。 
- **プローブ** – プローブを使用すると、VM インスタンスの正常性を追跡できます。正常性プローブが失敗した場合、VM インスタンスは自動的にローテーションから除外されます。
- **受信 NAT 規則** – フロント エンド IP を通過し、バック エンド IP に配信される受信トラフィックを定義する NAT 規則です。

## パブリック IP
パブリック IP アドレス リソースは、予約されたパブリック IP アドレスか、動的なパブリック IP アドレスを提供します。パブリック IP アドレスは、ロード バランサー、NAT に割り当てるか、VM の NIC のプライベート IP アドレスに関連付けることができます。

パブリック IP のリソースの主なプロパティは次のとおりです。

- **IP の割り当て方法** – 予約済みか動的。 

## ネットワーク セキュリティ グループ (NSG)
NSG リソースを使用すると、許可または拒否の規則を実装することで、ワークロードのセキュリティ境界を作成できます。このような規則は、NIC レベル (VM インスタンス レベル) またはサブネット レベル (VM のグループ) で適用されます。

NSG リソースの主なプロパティは次のとおりです。

- **セキュリティ規則** – NSG には複数のセキュリティ規則を定義できます。各規則で、各種のトラフィックを許可または拒否できます。

## セキュリティ規則
セキュリティ規則は、NSG の子リソースです。

セキュリティの規則の主なプロパティは次のとおりです。

- **プロトコル** – この規則が適用されるネットワーク プロトコル。
- **送信元ポート範囲** – 送信元ポート、または 0 ～ 65535 の範囲。すべてのポートを適合させるために、ワイルドカードを使用できます。 
- **送信先ポート範囲** – 送信先ポート、または 0 ～ 65535 の範囲。すべてのポートを適合させるために、ワイルドカードを使用できます。
- **送信元アドレスのプレフィックス** – 送信元 IP アドレスの範囲。 
- **送信先アドレスのプレフィックス** – 送信先 IP アドレスの範囲。
- **アクセス** – トラフィックの*許可*または*拒否*。
- **優先度** – 100 から 4096 までの値。セキュリティ規則のコレクションでは、優先度番号は各規則に対して一意である必要があります。優先度番号が低いほど、規則の優先度が高くなります。
- **方向** – 規則をトラフィックに適用する方向を、*受信*方向にするか、それとも*送信*方向にするかを指定します。 

## VPN Gateway 
VPN ゲートウェイ リソースでは、オンプレミスのデータ センターと Azure の間にセキュリティで保護された接続を作成することができます。VPN ゲートウェイ リソースは、次の 3 つの異なる方法で構成できます。
 
- **ポイント対サイト** – 任意のコンピューターから VPN クライアントを使用して、VNET 内でホストされている Azure のリソースに安全にアクセスすることができます。 
- **複数サイト接続** – オンプレミスのデータ センターから VNET で実行しているリソースに安全に接続できます。 
- **VNET 対 VNET** – 同じリージョン内またはリージョン間で、Azure VNET 間を安全に接続できます。これにより、地理的冗長性のあるワークロードをビルドできます。

VPN ゲートウェイの主なプロパティは次のとおりです。
 
- **ゲートウェイの種類** - 動的にルーティングされたゲートウェイ、または静的にルーティングされたゲートウェイ。 
- **VPN クライアント アドレス プールのプレフィックス** – ポイント対サイトの構成で接続しているクライアントに割り当てられる IP アドレスです。

## Traffic Manager プロファイル
Traffic Manager とその子エンドポイントのリソースは、Azure 内と Azure 外部のエンドポイントへトラフィックを分散できるようにします。このようなトラフィック分散は、ポリシーによって管理されます。Traffic Manager では、エンドポイントの正常性を監視できます。また、エンドポイントの正常性に基づいて、トラフィックが適切に転送できるようにします。

Traffic Manager プロファイルの主なプロパティは次のとおりです。

- **トラフィックのルーティング方法** – 有効な値は、*パフォーマンス*、*重み付け*、および *優先度* です。
- **DNS 構成** – プロファイルの FQDN です。
- **プロトコル** – 監視プロトコル。有効な値は、*HTTP* と *HTTPS* です。
- **ポート** – 監視ポート。 
- **パス** – 監視パス。
- **エンドポイント** – エンドポイント リソースのコンテナーです。

## エンドポイント 
エンドポイントは、Traffic Manager プロファイルの子リソースです。Traffic Manager プロファイル リソースに構成されたポリシーに基づいてユーザー トラフィックが配信されるサービスまたは Web エンドポイントを表します。

エンドポイントの主なプロパティは次のとおりです。
 
- **種類** – エンドポイントの種類。有効な値は *Azure エンドポイント*、*外部エンドポイント*、および*入れ子になったエンドポイント*です。 
- **ターゲット リソース ID** – サービスまたは Web のエンドポイントのパブリック IP アドレスです。Azure のエンドポイントまたは外部のエンドポイントを指定できます。
- **重み** – トラフィック管理に使用されるエンドポイントの重み付けです。 
- **優先度** – フェールオーバー アクションを定義するために使用されるエンドポイントの優先度です。 

## テンプレートの使用

テンプレートから Azure にサービスを展開するには、PowerShell、AzureCLI を使用するか、または GitHub からクリック操作によって展開します。GitHub でテンプレートからサービスを展開するには、次の手順を実行します。

1. GitHub で template3 ファイルを開きます。たとえば、[2 つのサブネットを含む仮想ネットワーク](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network) を開きます。
2. **[Azure に展開]** をクリックし、資格情報を使用して Azure ポータルにサインインします。
3. テンプレートを確認し、**[保存]** をクリックします。
4. **[パラメーターの編集]** をクリックし、VNET およびサブネットの場所 (*[米国西部]* など) を選択します。
5. 必要に応じて、**ADDRESSPREFIX** および **SUBNETPREFIX** パラメーターを変更し、**[OK]** をクリックします。
6. **[リソース グループの選択]** をクリックし、VNET とサブネットの追加先となるリソース グループをクリックします。あるいは、**[新規作成]** をクリックして、新しいリソース グループを作成することもできます。
3. **[作成]** をクリックします。**[テンプレートの展開をプロビジョニング中]**というタイトルが表示されます。展開が完了すると、画面の表示は以下のようになります。

![サンプル テンプレートの展開](./media/resource-group-networking/Figure6.png)

## 関連項目

[Azure ネットワーク API のリファレンス](https://msdn.microsoft.com/library/azure/dn948464.aspx)

[ネットワーク用 Azure PowerShell のリファレンス](https://msdn.microsoft.com/library/azure/mt163510.aspx)

[Azure リソース マネージャー テンプレートの言語](https://msdn.microsoft.com/library/azure/dn835138.aspx)

[Azure のネットワーク – 一般的に使用されているテンプレート](https://github.com/Azure/azure-quickstart-templates)

[コンピューティング リソース プロバイダー](../virtual-machines-azurerm-versus-azuresm)

[Azure リソース マネージャーの概要](../resource-group-overview)

[Azure リソース マネージャーでのロール ベースのアクセス制御](https://msdn.microsoft.com/library/azure/dn906885.aspx)

[Azure リソース マネージャーでのタグの使用](https://msdn.microsoft.com/library/azure/dn848368.aspx)

[テンプレートの展開](https://msdn.microsoft.com/library/azure/dn790549.aspx)

<!---HONumber=August15_HO6-->