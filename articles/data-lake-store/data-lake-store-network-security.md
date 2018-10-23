---
title: Azure Data Lake Storage Gen1 のネットワーク セキュリティ | Microsoft Docs
description: Azure Data Lake Storage Gen1 における IP ファイアウォールと仮想ネットワーク統合のしくみについて説明します
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2018
ms.author: elsung
ms.openlocfilehash: 0da5962bc0b48a387ee82a1db36099682e14bca3
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2018
ms.locfileid: "49168124"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1---preview"></a>Azure Data Lake Storage Gen1 の Virtual Network 統合 - プレビュー

Azure Data Lake Storage Gen1 の Virtual Network 統合 (プレビュー) について紹介します。 VNET 統合を使用すると、ご利用の Azure Data Lake Storage Gen1 アカウントを特定の仮想ネットワークおよびサブネットにロックして、それらのアカウントへの承認されていないアクセスを防止できます。 指定された仮想ネットワークとサブネットからのトラフィックのみを受け入れ、それ以外のアクセスはブロックするよう、ADLS Gen1 アカウントを構成できるようになりました。 これは、ADLS アカウントを外部の脅威からセキュリティ保護するのに役立ちます。

ADLS Gen1 の VNET 統合には、ご利用の仮想ネットワークと Azure Active Directory サービスとの間で、アクセス トークンに追加的なセキュリティ要求を生成する仮想ネットワーク サービス エンドポイント セキュリティが利用されています。 ご利用の ADLS Gen1 アカウントに対して仮想ネットワークを認証し、アクセスを許可するために、これらの要求が使用されます。

> [!NOTE]
> これはプレビュー段階のテクノロジであり、運用環境での使用は推奨されません。
>
> これらの機能を使用することに関して追加料金はかかりません。 ご利用のアカウントは、お客様が使用した ADLS Gen1 ([価格](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable)) およびすべての Azure サービス ([価格](https://azure.microsoft.com/pricing/#product-picker)) に対して、標準の料金で課金されます。

## <a name="scenarios-for-vnet-integration-for-adls-gen1"></a>ADLS Gen1 における VNET 統合のシナリオ

ADLS Gen1 VNET 統合では、ご利用の ADLS Gen1 アカウントへのアクセスを、指定した仮想ネットワークおよびサブネットからに制限することができます。  アカウントが指定の VNET サブネットにロックされると、Azure のその他の VNET または VM からそのアカウントにアクセスできなくなります。  機能上、ADLS Gen1 VNET 統合は、[仮想ネットワーク サービス エンドポイント](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)と同じシナリオを実現します。  以下のセクションでは、いくつかの主な違いについて詳しく説明します。 

![ADLS Gen1 VNET 統合のシナリオ図](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> オンプレミス ネットワークからのアクセスの許可には、VNET ルールに加えて、既存の IP ファイアウォール ルールも使用できます。 

## <a name="optimal-routing-with-adls-gen1-vnet-integration"></a>ADLS Gen1 VNET 統合を使用した最適なルーティング

VNET サービス エンドポイントの主な利点は、ご利用の仮想ネットワークからの[最適なルーティング](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview#key-benefits)です。  ADLS Gen1 アカウントに対して同じルート最適化を実施するには、ご利用の VNET から ADLS Gen1 アカウントまで次の[ユーザー定義ルート](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined)を使用します。

- **ADLS パブリック IP アドレス** - ターゲットの ADLS Gen1 アカウントのパブリック IP アドレスを使用します。  ADLS Gen1 アカウントの IP アドレスは、その [DNS 名を解決](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-connectivity-from-vnets#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity)することで特定できます。  アドレスごとに個別のエントリを作成してください。

```azurecli
# Create a Route table for your resource group
az network route-table create --resource-group $RgName --name $RouteTableName

# Create Route Table Rules for ADLS Public IP Addresses
# There will be one rule per ADLS Public IP Addresses 
az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet

# Update the VNet and apply the newly created Route Table to it
az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
```

## <a name="data-exfiltration-from-the-customer-vnet"></a>カスタマー VNET からのデータ流出

Virtual Network からのアクセスに関して ADLS アカウントを保護することに加え、承認されていないアカウントへの流出を確実に防ぐことについても関心がおありかもしれません。

Microsoft では、VNET でファイアウォール ソリューションを使用し、宛先アカウントの URL に基づいてアウトバウンド トラフィックをフィルタリングして、承認された ADLS Gen1 アカウントにのみアクセスできるようにすることをお勧めします。

考えられる選択肢としては、以下のようなものがあります。
- [Azure Firewall](https://docs.microsoft.com/azure/firewall/overview): ご利用の VNET に [Azure ファイアウォールをデプロイして構成](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)し、アウトバウンド ADLS トラフィックをセキュリティで保護して、承認されている既知のアカウントの URL にトラフィックをロックできます。
- [ネットワーク仮想アプライアンス](https://azure.microsoft.com/solutions/network-appliances/) ファイアウォール: 管理者が特定の商用ファイアウォール ベンダーの利用のみを承認している場合は、Azure Marketplace で提供されている NVA ファイアウォール ソリューションを使用して同じ機能を実行できます。

> [!NOTE]
> データ パス内でファイアウォールを使用すると、データ パスのホップ数が増えるため、エンド ツー エンドのデータ交換という点でネットワーク パフォーマンス (利用可能なスループット、接続の待ち時間など) に影響が生じる場合があります。 

## <a name="limitations"></a>制限事項
1.  HDInsight クラスターは、プレビューへの追加後、新しく作成する必要があります。  ADLS Gen1 VNET 統合のサポート提供前に作成されたクラスターは、この新しい機能がサポートされるように作成し直す必要があります。 
2.  新しい HDInsight クラスターを作成するとき、VNET 統合が有効な ADLS Gen1 アカウントを選択すると、プロセスが失敗します。 最初に VNET ルールを無効にしておく必要があります。または、ADLS アカウントの **[ファイアウォールと仮想ネットワーク]** ブレードで、**すべてのネットワークおよびサービスからのアクセスを許可**してください。  詳細については、「[例外](##Exceptions)」セクションを参照してください。
3.  ADLS Gen1 VNET 統合プレビューは、[Azure リソースのマネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) に対応していません。  
4.  VNET を有効にした ADLS Gen1 アカウント内のファイルおよびフォルダーのデータにポータルからアクセスすることはできません。  これには、VNET 内の VM からのアクセスやアクティビティ (Data Explorer の使用など) が該当します。  アカウント管理アクティビティは、引き続きご利用いただけます。  VNET を有効にした ADLS アカウント内のファイルおよびフォルダーのデータには、ポータル以外のすべてのリソース経由でアクセスできます。これには、SDK アクセスや PowerShell スクリプトのほか、アクセス元がポータルでない各種 Azure サービスが該当します。 

## <a name="configuration"></a>構成

### <a name="step1-configure-your-vnet-to-use-aad-service-endpoint"></a>手順 1: AAD サービス エンドポイントを使用するように VNET を構成する
1.  Azure portal に移動して自分のアカウントにログインします。 
2.  ご利用のサブスクリプションに[新しい仮想ネットワークを作成](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)するか、または既存の仮想ネットワークに移動します。  現時点では、VNET が ADLS Gen 1 アカウントと同じリージョンに存在している必要があります。 
3.  仮想ネットワーク ブレードで **[サービス エンドポイント]** を選択します。 
4.  **[追加]** をクリックして、新しいサービス エンドポイントを追加します。
![VNET のサービス エンドポイントを追加](media/data-lake-store-network-security/config-vnet-1.png)
5.  エンドポイントのサービスとして **[Microsoft.AzureActiveDirectory]** を選択します。
![[Microsoft.AzureActiveDirectory] サービス エンドポイントを選択](media/data-lake-store-network-security/config-vnet-2.png)
6.  接続を許可するサブネットを選択して、**[追加]** をクリックします。
![サブネットを選択](media/data-lake-store-network-security/config-vnet-3.png)
7.  サービス エンドポイントが追加されるまでに最大 15 分かかることがあります。 追加されると、リストに表示されます。 リストに表示されていること、また詳細がすべて構成済みであることを確認してください。 
![サービス エンドポイントの追加に成功](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-vnetsubnet-for-your-adls-gen1-account"></a>手順 2: ADLS Gen1 アカウントに対して許可する VNET とサブネットを設定する
1.  VNET の構成後、ご利用のサブスクリプションに[新しい Azure Data Lake Storage Gen1 アカウントを作成](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account)するか、または既存の ADLS Gen1 アカウントに移動します。 現時点では、ADLS Gen 1 アカウントが VNET と同じリージョンに存在している必要があります。 
2.  **[ファイアウォールと仮想ネットワーク]** を選択します。

  > [!NOTE]
  > **[ファイアウォールと仮想ネットワーク]** が設定に表示されない場合は、ポータルからログオフしてください。 ブラウザーを閉じます。 ブラウザーのキャッシュをクリアします。 マシンを再起動して再試行します。

  ![VNET ルールを ADLS アカウントに追加](media/data-lake-store-network-security/config-adls-1.png)
3.  **[選択されたネットワーク]** を選択します。 
4.  **[既存の仮想ネットワークを追加]** を選択します。
  ![既存の仮想ネットワークを追加](media/data-lake-store-network-security/config-adls-2.png)
5.  接続を許可する VNET とサブネットを選択し、**[追加]** をクリックします。
  ![VNET とサブネットを選択](media/data-lake-store-network-security/config-adls-3.png)
6.  VNET とサブネットが正しくリストに表示されていることを確認して、**[保存]** を選択します。
  ![新しいルールを保存](media/data-lake-store-network-security/config-adls-4.png)

  > [!NOTE]
  > 保存後、設定が反映されるまでに最大 5 分かかることがあります。

7.  (省略可) VNET とサブネットに加え、特定の IP アドレスからの接続を許可したい場合は、同じページの **[ファイアウォール]** セクションをご利用ください。 

## <a name="exceptions"></a>例外
**[ファイアウォールと仮想ネットワーク]** ブレードの [例外] 領域には、Azure 上の一連のサービスおよび仮想マシンからの接続を有効にする 2 つのチェック ボックスがあります。
![ファイアウォールと仮想ネットワークの例外](media/data-lake-store-network-security/firewall-exceptions.png)
- **[すべての Azure サービスによるこの Data Lake Storage Gen1 アカウントへのアクセスを許可します]** をオンにすると、Azure Data Factory、Event Hubs、全 Azure VM など、すべての Azure サービスによる  ADLS アカウントとの通信が許可されます。

- **[Azure Data Lake Analytics によるこの Data Lake Storage Gen1 アカウントへのアクセスを許可します]** をオンにすると、Azure Data Lake Analytics サービスによるこの ADLS アカウントへの接続が許可されます。 

これらの例外はオフにしておき、ご利用の VNET の外側にあるこれらの他のサービスからの接続が必要な場合にのみ、オンにすることをお勧めします。
