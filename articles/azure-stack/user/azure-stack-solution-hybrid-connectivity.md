---
title: Azure と Azure Stack でハイブリッド クラウド接続を構成する | Microsoft Docs
description: Azure と Azure Stack でハイブリッド クラウド接続を構成する方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/28/2018
ms.author: mabrigg
ms.reviewer: anajod
ms.openlocfilehash: a219ec1797ff74bd1a9ff458e34650d1545f1172
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585783"
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>チュートリアル: Azure と Azure Stack でハイブリッド クラウド接続を構成する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

ハイブリッド接続パターンを使用して、グローバル Azure および Azure Stack 内のリソースに安全な方法でアクセスできます。

このチュートリアルでは、以下を実現するためのサンプル環境を構築します。

> [!div class="checklist"]
> - プライバシーまたは規制要件を満たすためにデータをオンプレミスで保管する一方で、グローバル Azure のリソースにアクセスする。
> - グローバル Azure のクラウド スケールのアプリのデプロイとリソースを使用しながら、レガシー システムを維持する。

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack は Azure の拡張機能です。 Azure Stack は、オンプレミスの環境にクラウド コンピューティングの俊敏性とイノベーションを提供します。これにより、どこでもハイブリッド アプリをビルドしてデプロイできる唯一のハイブリッド クラウドが実現されます。  
> 
> ホワイト ペーパー「[Design Considerations for Hybrid Applications](https://aka.ms/hybrid-cloud-applications-pillars)」では、ハイブリッドアプリケーションの設計、デプロイ、および操作に関するソフトウェア品質の重要な要素 (配置、スケーラビリティ、可用性、回復性、管理の容易性、セキュリティ) について概説しています。 これらの設計の考慮事項は、ハイブリッド アプリケーションの設計を最適化したり、運用環境での課題を最小限に抑えたりするのに役立ちます。


## <a name="prerequisites"></a>前提条件

ハイブリッド接続のデプロイを構築するにはいくつかのコンポーネントが必要です。 これらのコンポーネントの一部は準備に時間がかかるため、適切に計画する必要があります。

**Azure Stack**

運用 Azure Stack は、Azure OEM/ハードウェア パートナーがデプロイできます。Azure Stack Development Kit (ASDK) は、すべてのユーザーがデプロイできます。

**Azure Stack のコンポーネント**

Azure Stack オペレーターが、App Service のデプロイ、プランとオファーの作成、テナント サブスクリプションの作成、および Windows Server 2016 イメージの追加を行う必要があります。 これらのコンポーネントの一部を既に持っている場合は、このチュートリアルを開始する前にそれらが要件を満たしていることをご確認ください。

このチュートリアルは、Azure と Azure Stack について一定の基本知識があることを前提にしています。 チュートリアルを開始する前に詳細を確認するには、次の記事をお読みください。

 - [Azure 入門](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure Stack の主要概念](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
 - Azure で [Web アプリ](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal)を作成します。 このチュートリアルで必要になるため、Web アプリの URL を書き留めておきます。

### <a name="azure-stack"></a>Azure Stack

 - 運用 Azure Stack を使用するか、 https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 から Azure Stack Development Kit をデプロイします。
   >[!Note]
   >ASDK のデプロイには最大 7 時間がかかることがあるため、適切に計画してください。

 - [App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS サービスを Azure Stack にデプロイします。
 - Azure Stack 環境で [プランとオファーを作成](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview)します。
 - Azure Stack 環境内で[テナント サブスクリプションを作成](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)します。

### <a name="before-you-begin"></a>開始する前に

ハイブリッド クラウド接続の構成を開始する前に、次の条件を満たしていることを確認します。

 - VPN デバイスの外部接続用パブリック IPv4 アドレスが必要です。 この IP アドレスを NAT の内側に割り当てることはできません。
 - すべてのリソースが同じリージョン/場所にデプロイされていること。

#### <a name="tutorial-example-values"></a>チュートリアルの例の値

このチュートリアルの例では、次の値を使用します。 これらの値を使用して、テスト環境を作成できます。また、この値を参考にすると、例をよく理解できます。 VPN ゲートウェイの一般的な設定の詳細については、[VPN Gateway の設定](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings)に関するページを参照してください。

接続の仕様:

 - **VPN の種類**: ルート ベース
 - **接続の種類:** サイト対サイト (IPsec)
 - **ゲートウェイの種類**: VPN
 - **Azure 接続名**: Azure-Gateway-AzureStack-S2SGateway (この値はポータルによって自動入力されます)
 - **Azure Stack 接続名**: AzureStack-Gateway-Azure-S2SGateway (この値はポータルによって自動入力されます)
 - **共有キー**: VPN ハードウェアと互換性があり、接続の両側で値が一致していること
 - **サブスクリプション**: 任意のサブスクリプション
 - **リソース グループ**: Test-Infra

ネットワークとサブネットの IP アドレス:

| Azure/Azure Stack 接続 | Name | サブネット | IP アドレス |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Azure vNet | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Azure Stack vNet | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Azure 仮想ネットワーク ゲートウェイ | Azure-Gateway |  |  |
| Azure Stack 仮想ネットワーク ゲートウェイ | AzureStack-Gateway |  |  |
| Azure パブリック IP | Azure-GatewayPublicIP |  | 作成時に決定されます |
| Azure Stack パブリック IP | AzureStack-GatewayPublicIP |  | 作成時に決定されます |
| Azure ローカル ネットワーク ゲートウェイ | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Azure Stack パブリック IP 値 |
| Azure Stack ローカル ネットワーク ゲートウェイ | Azure-S2SGateway<br>10.100.102.0/23 |  | Azure パブリック IP 値 |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>グローバル Azure および Azure Stack に仮想ネットワークを作成する

ポータルを使用して仮想ネットワークを作成するには、次の手順を使用します。 この記事をチュートリアルとして使用している場合は、これらの[例の値](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values)を使用できます。 ただし、この記事を使用して運用環境を構成する場合は、例の設定値を独自の値に置き換えます。

> [!IMPORTANT]
> Azure または Azure Stack の vNet アドレス空間に IP アドレスの重複がないことを確認する必要があります。

Azure で vNet を作成するには:

1. ブラウザーを使用して [Azure portal](http://portal.azure.com/) に接続し、Azure アカウントでサインインします。
2. **[リソースの作成]** を選択します。 **[Marketplace を検索]** フィールドに「`virtual network`」と入力します。 結果リストで **[仮想ネットワーク]** を探して、**[仮想ネットワーク]** を選択します。
3. **[デプロイ モデルの選択]** の一覧から **[リソース マネージャー]** を選択し、**[作成]** を選択します。
4. **[仮想ネットワークの作成]** で、VNet の設定を構成します。 必須フィールドの名前の先頭には、赤いアスタリスクが付きます。  有効な値を入力すると、アスタリスクは緑のチェック マークに変わります。

Azure Stack で vNet を作成するには:

* Azure Stack **テナント ポータル**を使用して前の手順 (1 から 4) を繰り返します。

## <a name="add-a-gateway-subnet"></a>ゲートウェイ サブネットの追加

仮想ネットワークをゲートウェイに接続する前に、接続先の仮想ネットワークのゲートウェイ サブネットを作成する必要があります。 ゲートウェイ サービスでは、ゲートウェイ サブネット内に指定した IP アドレスが使用されます。

[Azure portal](http://portal.azure.com/) で、仮想ネットワーク ゲートウェイを作成する Resource Manager 仮想ネットワークに移動します。

1. vNet を選択して **[仮想ネットワーク]** ページを開きます。
2. **[設定]** で、**[サブネット]** を選択します。
3. **[サブネット]** ページで **[+ゲートウェイ サブネット]** を選択して **[サブネットの追加]** ページを開きます。

    ![ゲートウェイ サブネットを追加する](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. サブネットの **[名前]** には、"GatewaySubnet" という値が自動的に入力されます。 この値は、Azure がゲートウェイ サブネットとしてこのサブネットを認識するために必要になります。
5. 提供されている **[アドレス範囲]** を構成要件に一致するように変更し、**[OK]** を選択します。

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Azure および Azure Stack に仮想ネットワーク ゲートウェイを作成する

Azure に仮想ネットワーク ゲートウェイを作成するには、次の手順を使用します。

1. ポータル ページの左側にある **[+]** を選択し、検索フィールドに「仮想ネットワーク ゲートウェイ」と入力します。
2. **[結果]** で **[仮想ネットワーク ゲートウェイ]** を選択します。
3. **[仮想ネットワーク ゲートウェイ]** で、**[作成]** を選択して **[仮想ネットワーク ゲートウェイの作成]** ページを開きます。
4. **[仮想ネットワーク ゲートウェイの作成]** で、「**チュートリアルの例の値**」で示したようにネットワーク ゲートウェイの値を指定し、次の追加の値を指定します。

    - **[SKU]**: Basic
    - **[仮想ネットワーク]**: 前に作成した仮想ネットワークを選択します。 作成したゲートウェイ サブネットが自動的に選択されます。
    - **[最初の IP 構成]**: これが、ゲートウェイのパブリック IP です。
        - **[ゲートウェイ IP 構成の作成]** を選択します。これにより、**[パブリック IP アドレスの選択]** ページが開きます。
        - **[+ 新規作成]** を選択して **[パブリック IP アドレスの作成]** ページを開きます。
        - パブリック IP アドレスの**名前**を入力します。 SKU は **[Basic]** のままにし、**[OK]** を選択して変更を保存します。

       > [!Note]
       > 現在、VPN Gateway ではパブリック IP アドレスの動的割り当てのみがサポートされます。 ただし、VPN ゲートウェイに割り当てられた IP アドレスが後から変わることは基本的にありません。 パブリック IP アドレスが変わるのは、ゲートウェイが削除され、再度作成されたときのみです。 VPN ゲートウェイのサイズ変更、リセット、その他の内部メンテナンス/アップグレードでは、IP アドレスは変わりません。

4. ゲートウェイ設定を確認します。
5. **[作成]** を選択して VPN ゲートウェイを作成します。 ゲートウェイ設定が検証され、ダッシュボードに [Deploying Virtual network gateway]\(仮想ネットワーク ゲートウェイのデプロイ\) タイルが表示されます。

   >[!Note]
   >ゲートウェイの作成には、最大で 45 分かかる場合があります。 完了状態を確認するために、ポータル ページの更新が必要な場合があります。

    ゲートウェイの作成後は、ポータルの仮想ネットワークを調べることで、ゲートウェイに割り当てられている IP アドレスを確認できます。 ゲートウェイは、接続されたデバイスとして表示されます。 ゲートウェイの詳細を表示するには、デバイスを選択します。

6. Azure Stack デプロイでこれらの手順 (1 から 5) を繰り返します。

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Azure および Azure Stack にローカル ネットワーク ゲートウェイを作成する

ローカル ネットワーク ゲートウェイは通常、オンプレミスの場所を指します。 サイトに Azure または Azure Stack が参照できる名前を付け、以下を指定します。

- 接続を作成しているオンプレミスの VPN デバイスの IP アドレス。
- VPN ゲートウェイを介して VPN デバイスにルーティングされる IP アドレスのプレフィックス。 指定するアドレスのプレフィックスは、オンプレミス ネットワークのプレフィックスです。

  >[!Note]
  >オンプレミスのネットワークが変更された場合、または VPN デバイスのパブリック IP アドレスを変更する必要がある場合、これらの値を後で簡単に更新できます。

1. ポータルで、**[+リソースの作成]** を選択します。
2. 検索ボックスに「**ローカル ネットワーク ゲートウェイ**」と入力し、**Enter** キーを押して検索します。 これにより、結果の一覧が返されます。
3. **[ローカル ネットワーク ゲートウェイ]** を選択し、**[作成]** を選択して **[ローカル ネットワーク ゲートウェイの作成]** ページを開きます。
4. **[ローカル ネットワーク ゲートウェイの作成]** で、**チュートリアルの例の値**を使用してローカル ネットワーク ゲートウェイの値を指定します。 次の追加の値を含めます。

    - **[IP アドレス]:** これは、Azure または Azure Stack が接続する VPN デバイスのパブリック IP アドレスです。 NAT の内側にない有効なパブリック IP アドレスを指定するので、Azure はアドレスに到達することができます。 現時点で IP アドレスを持っていない場合は、例に示されている値をプレースホルダーとして使用できますが、プレースホルダーを後で VPN デバイスのパブリック IP アドレスに置き換える必要があります。 Azure は、有効なアドレスを指定するまでデバイスに接続できません。
    - **[アドレス空間]**: このローカル ネットワークが表すネットワークのアドレス範囲を参照します。 複数のアドレス領域の範囲を追加することができます。 指定した範囲が、接続先となる他のネットワークの範囲と重複しないようにしてください。 指定したアドレス範囲が、Azure によってオンプレミスの VPN デバイスの IP アドレスにルーティングされます。 オンプレミスのサイトに接続する場合は、例の値を使用せず、独自の値を使用してください。
    - **[BGP 設定の構成]:** BGP を構成する場合にのみ使用します。 それ以外の場合は選択しないでください。
    - **[サブスクリプション]:** 正しいサブスクリプションが表示されていることを確認します。
    - **[リソース グループ]:** 使用するリソース グループを選択します。 新しいリソース グループを作成することも、作成済みのリソース グループを選択することもできます。
    - **[場所]:** このオブジェクトが作成される場所を選択します。 VNet が存在するのと同じ場所を選択することもできますが、必須ではありません。
5. 必要な値の指定が終了したら、**[作成]** を選択してローカル ネットワーク ゲートウェイを作成します。
6. Azure Stack デプロイでこれらの手順 (1 から 5) を繰り返します。

## <a name="configure-your-connection"></a>接続を構成する

オンプレミス ネットワークとのサイト間接続には VPN デバイスが必要です。 構成する VPN デバイスは接続と呼ばれます。 接続を構成するには、以下が必要です。

- 共有キー。 サイト間 VPN 接続を作成するときにも、これと同じ共有キーを指定します。 ここで紹介している例では、基本的な共有キーを使用しています。 実際には、もっと複雑なキーを生成して使用することをお勧めします。
- 仮想ネットワーク ゲートウェイのパブリック IP アドレス。 パブリック IP アドレスは、Azure Portal、PowerShell、または CLI を使用して確認できます。 Azure portal を使用して VPN ゲートウェイのパブリック IP アドレスを調べるには、[仮想ネットワーク ゲートウェイ] に移動し、該当するゲートウェイの名前を選択します。

次の手順を使用して、仮想ネットワーク ゲートウェイとオンプレミス VPN デバイスとの間にサイト間 VPN 接続を作成します。

1. Azure portal で、**[+リソースの作成]** を選択します。
2. **接続**を検索します。
3. **[結果]** で、**[接続]** を選択します。
4. **[接続]** で、**[作成]** を選択します。
5. **[接続の作成]** で、次の設定を構成します。

    - **[接続の種類]:** [サイト対サイト (IPSec)] を選択します。
    - **[リソース グループ]**: テスト リソース グループを選択します。
    - **[仮想ネットワーク ゲートウェイ]**: 作成した仮想ネットワーク ゲートウェイを選択します。
    - **[ローカル ネットワーク ゲートウェイ]**: 作成したローカル ネットワーク ゲートウェイを選択します。
    - **[接続名]**: 2 つのゲートウェイの値を使用して自動的に入力されます。
    - **[共有キー]:** この値は、ローカルのオンプレミス VPN デバイスで使用している値と一致させる必要があります。 このチュートリアルの例では "abc123" を使用していますが、より複雑な値を使用することもできます (推奨)。 重要なのは、この値は、VPN デバイスを構成する際に指定する値と同じにする必要があることです。
    - **[サブスクリプション]**、**[リソース グループ]**、**[場所]** の値は固定されています。

6. **[OK]** を選択して、接続を作成します。

仮想ネットワーク ゲートウェイの **[接続]** ページで接続を確認できます。 状態は、*[不明]* から *[接続中]* に変わり、その後 *[成功]* に変わります。

## <a name="next-steps"></a>次の手順

- Azure のクラウド パターンの詳細については、「[Cloud Design Pattern (クラウド設計パターン)](https://docs.microsoft.com/azure/architecture/patterns)」を参照してください。
