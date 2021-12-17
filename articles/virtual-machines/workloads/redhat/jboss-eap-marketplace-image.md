---
title: Azure Red Hat Enterprise Linux 仮想マシン (VM) と仮想マシン スケール セットでの Red Hat JBoss EAP 向けの Azure Marketplace プラン
description: Azure Marketplace プランを使用して、Red Hat JBoss EAP を Azure Red Hat Enterprise Linux (RHEL) VM と仮想マシン スケール セットにデプロイする方法について説明します。
author: theresa-nguyen
ms.author: bicnguy
ms.topic: article
ms.service: virtual-machines
ms.subservice: redhat
ms.assetid: 9b37b2c4-5927-4271-85c7-19adf33d838b
ms.date: 05/25/2021
ms.openlocfilehash: 6682c5301861732189d532641060e136250957b9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261483"
---
# <a name="deploy-red-hat-jboss-enterprise-platform-eap-on-azure-vms-and-virtual-machine-scale-sets-using-the-azure-marketplace-offer"></a>Azure Marketplace プランを使用して Azure VM と仮想マシン スケール セットに Red Hat JBoss Enterprise Platform (EAP) をデプロイする

Azure [Red Hat Enterprise Linux (RHEL)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) 上の [Red Hat JBoss Enterprise Application Platform](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) 用の Azure Marketplace プランは、[Red Hat](https://www.redhat.com/) と Microsoft の共同ソリューションです。 Red Hat は、[Java](https://www.java.com/) 標準、[OpenJDK](https://openjdk.java.net/)、[MicroProfile](https://microprofile.io/)、[Jakarta EE](https://jakarta.ee/)、[Quarkus](https://quarkus.io/) などの主要なオープンソース ソリューション プロバイダーであり貢献者です。 JBoss EAP は、Java EE 認定の主要な Java アプリケーション サーバー プラットフォームで、Web プロファイルと Full Platform の両方で、Jakarta EE に準拠しています。 すべての JBoss EAP リリースは、市場をリードするさまざまなオペレーティング システム、Java 仮想マシン (JVM)、データベースの組み合わせでテストおよびサポートされています。  JBoss EAP と RHEL には、あらゆる環境でエンタープライズ Java アプリケーションをビルド、実行、デプロイ、管理するために必要なすべてのものが含まれています。  オンプレミス、仮想環境、また、プライベート、パブリック、ハイブリッドのクラウドが含まれます。 Red Hat と Microsoft による共同ソリューションには、統合サポートとソフトウェア ライセンスの柔軟性が含まれています。

## <a name="jboss-eap-on-azure-integrated-support"></a>JBoss EAP on Azure - 統合サポート

JBoss EAP on Azure Marketplace プランは、Red Hat と Microsoft による共同ソリューションであり、統合サポートとソフトウェア ライセンスの柔軟性が含まれます。 Microsoft と Red Hat の両方に連絡して、サポート チケットを作成することができます。  各ベンダーに対して複数のチケットを作成する必要がないように、問題を共同で共有して解決します。 統合サポートでは、すべての Azure インフラストラクチャと Red Hat アプリケーション サーバー レベルのサポートの問題が対象となります。    

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションを持つ Azure アカウント - Azure サブスクリプションをお持ちでない場合は、[Visual Studio Subscription サブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) (旧 MSDN) をアクティブ化するか、[無料でアカウントを作成](https://azure.microsoft.com/pricing/free-trial)できます。

* JBoss EAP のインストール - JBoss EAP の Red Hat Subscription Management (RHSM) エンタイトルメントを有する Red Hat アカウントが必要です。 このエンタイトルメントにより、Red Hat によるテストと認定が済んだ JBoss EAP バージョンをダウンロードすることができます。  EAP エンタイトルメントをお持ちでない場合は、[Red Hat Developer Subscription for Individuals](https://developers.redhat.com/register) を使用して無料の開発者サブスクリプションにサインアップします。 登録されると、[Red Hat カスタマーポータル](https://access.redhat.com/management/)で必要な資格情報 (プール ID) を確認できます。

* RHEL のオプション - 従量課金制 (PAYG) またはサブスクリプション持ち込み (BYOS) のどちらかを選択してください。 BYOS を選択した場合、ソリューション テンプレートを使用して Marketplace プラン をデプロイする前に [Red Hat Cloud Access](https://access.redhat.com/) [RHEL Gold Image](https://azure.microsoft.com/updates/red-hat-enterprise-linux-gold-images-now-available-on-azure/) をアクティブにする必要があります。 Microsoft Azure で使用するために、[これらの手順](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)に従って RHEL Gold Image を有効にします。

* Azure コマンド ライン インターフェイス (CLI)。

## <a name="azure-marketplace-offer-subscription-options"></a>Azure Marketplace プランのサブスクリプション オプション

Azure Marketplace の JBoss EAP on RHEL プランを使用すると、Azure VM または仮想マシン スケール セット デプロイ のインストールとプロビジョニングを 10 未満で行えます。 これらのプランには、[Azure Marketplace](https://azuremarketplace.microsoft.com/) からアクセスできます。

この Marketplace プランには、お客様の要件をサポートするために、EAP と RHEL のバージョンのさまざまな組み合わせが用意されています。 JBoss EAP は常に BYOS ですが、RHEL オペレーティング システム (OS) の場合は、BYOS または PAYG を選択できます。 プランには、スタンドアロン、クラスター化 VM、またはクラスター化された仮想マシン スケール セットとしての JBoss EAP on RHEL のプラン構成オプションが含まれています。 

次の 6 つのプランを使用できます。 

- Boss EAP 7.3 on RHEL 8.3 スタンドアロン VM (PAYG)
- JBoss EAP 7.3 on RHEL 8.3 スタンドアロン VM (BYOS)
- JBoss EAP 7.3 on RHEL 8.3 クラスター化 VM (PAYG)
- JBoss EAP 7.3 on RHEL 8.3 クラスター化 VM (BYOS)
- JBoss EAP 7.3 on RHEL 8.3 クラスター化仮想マシン スケール セット (PAYG)
- JBoss EAP 7.3 on RHEL 8.3 クラスター化仮想マシン スケール セット (BYOS)

## <a name="using-rhel-os-with-payg-model"></a>PAYG モデルでの RHEL OS の使用

Azure Marketplace プランでは、RHEL をオンデマンドの PAYG VM または仮想マシン スケール セットとしてデプロイできます。 PAYG プランでは、Azure インフラストラクチャのコンピューティング、ネットワーク、ストレージの標準コストとは別に、時間単位の RHEL サブスクリプション料金がかかります。

PAYG モデルの詳細については、[Red Hat Enterprise Linux の価格](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/)に関するページを参照してください。 PAYG モデルで RHEL を使用するには、Azure サブスクリプションが必要です。 ([Azure Marketplace の RHEL](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.rhel-20190605) プランの支払方法が Azure サブスクリプションで指定されている必要があります)。

## <a name="using-rhel-os-with-byos-model"></a>BYOS モデルでの RHEL OS の使用

RHEL を BYOS VM または仮想マシン スケール セットとして使用するには、Azure で RHEL を使用するエンタイトルメントを持つ有効な Red Hat サブスクリプションが必要です。 これらの JBoss EAP on RHEL BYOS プランは、[Azure プライベート プラン](../../../marketplace/private-offers.md)として提供されています。 Azure Marketplace から RHEL BYOS オファー プランをデプロイするには、次の前提条件を満たす必要があります。 

1. ご利用の Red Hat サブスクリプションに RHEL OS と JBoss EAP のエンタイトルメントが付与されていることを確認します。
2. ご利用の Azure サブスクリプション ID で RHEL BYOS イメージを使用することを承認します。 [Red Hat Subscription Management (RHSM) のドキュメント](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)に従って、これらの手順が含まれているプロセスを実行します。
    1. Red Hat Cloud Access ダッシュボードで、Microsoft Azure をプロバイダーとして有効にします。
    2. Azure サブスクリプション ID を追加します。
    3. Microsoft Azure で新しい製品の Cloud Access を有効にします。
    4. Azure サブスクリプションの Red Hat Gold Image をアクティブにします。 詳細については、「[Cloud Access 用のサブスクリプションの有効化と管理](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/understanding-gold-images_cloud-access#using-gold-images-on-azure_cloud-access#using-gold-images-on-azure_cloud-access)」の章を参照してください。 
    5. Azure サブスクリプションで Red Hat Gold Image が使用できる状態になるまで待ちます。 通常これらの Gold Image は、申請から 3 時間以内に、Azure Private プランとして利用できるようになります。

3. RHEL BYOS イメージに対する Azure Marketplace の使用条件 (T&C) に同意します。 受け入れるには、次に示すように、[Azure コマンド ライン インターフェイス (CLI)](/cli/azure/install-azure-cli) コマンドを実行します。 詳細については、[Azure における RHEL BYOS Gold Image](./byos.md) に関するドキュメントを参照してください。 必ず最新バージョンの Azure CLI を実行してください。
    1. Azure CLI セッションを起動し、ご利用の Azure アカウントで認証を行います。 詳細については、[Azure CLI でのサインイン](/cli/azure/authenticate-azure-cli)に関するページを参照してください。 最新バージョンの Azure CLI が実行されていることを確認してから先に進んでください。
    2. 次の CLI コマンドを実行して、ご利用のサブスクリプションで RHEL BYOS プランが使用できることを確認します。 ここで結果が得られない場合は、手順 2 を参照してください。 JBoss EAP on RHEL BYOS プランのエンタイトルメントを使用して、Azure サブスクリプションがアクティブ化されていることを確認します。

    ```cli
    az vm image list --offer rhel-byos --all #use this command to verify the availability of RHEL BYOS images
    ```

    3. 次のコマンドを実行して、JBoss EAP on RHEL BYOS プランで必要な Azure Marketplace T&C を受け入れます。

    ```cli
    az vm image terms accept --publisher redhat --offer jboss-eap-rhel --plan $PLANID
    ```

    4. ここで `$PLANID` は、次のいずれかを指定します (使用する Marketplace オファー プランごとに手順 3 を繰り返します)。

    ```cli
    jboss-eap-73-byos-rhel-80-byos
    jboss-eap-73-byos-rhel-8-byos-clusteredvm
    jboss-eap-73-byos-rhel-80-byos-vmss
    jboss-eap-73-byos-rhel-80-payg
    jboss-eap-73-byos-rhel-8-payg-clusteredvm
    jboss-eap-73-byos-rhel-80-payg-vmss
    ```

4. これで、サブスクリプションで EAP on RHEL BYOS プランをデプロイする準備ができました。 デプロイ中に、サブスクリプションは、`subscription-manager` とデプロイ中に指定された資格情報を使用して、自動的にアタッチされます。

## <a name="using-jboss-eap-with-byos-model"></a>BYOS モデルでの JBoss EAP の使用

Azure では、JBoss EAP が BYOS モデルでのみ提供されます。 JBoss EAP on RHEL プランをデプロイするときは、有効な EAP エンタイトルメントが付与された RHSM プール ID と共に、RHSM の資格情報を指定する必要があります。 EAP のエンタイトルメントを持っていない場合は、[個人用 Red Hat Developer サブスクリプション](https://developers.redhat.com/register)を入手してください。 登録されると、サブスクリプションのナビゲーション メニューで必要な資格情報 (プール ID) を確認できます。 デプロイ中に、サブスクリプションは、`subscription-manager` とデプロイ中に指定された資格情報を使用して、自動的にアタッチされます。

## <a name="template-solution-architectures"></a>テンプレート ソリューションのアーキテクチャ

これらのオファー プランでは、RHEL で JBoss EAP セットアップを実行するためのすべての Azure コンピューティング リソースが作成されます。 テンプレート ソリューションによって次のリソースが作成されます。

- RHEL VM
- 1 つの Load Balancer (クラスター化された構成用)
- Virtual Network と 1 つのサブネット
- RHEL VM または仮想マシン スケール セットでの JBoss EAP のセットアップ (スタンドアロンまたはクラスター化)
- サンプル Java アプリケーション
- ストレージ アカウント

## <a name="after-a-successful-deployment"></a>正常なデプロイの後

1. 別の Virtual Network で [VNet ピアリングを使用してジャンプ VM を作成](../../windows/quick-create-portal.md#create-virtual-machine)し、サーバーにアクセスして、[仮想ネットワーク ピアリング](../../../virtual-network/tutorial-connect-virtual-networks-portal.md#peer-virtual-networks)を使用してアプリケーションを公開します。
2. サーバーとアプリケーションにアクセスするための[パブリック IP を作成](../../../virtual-network/ip-services/virtual-network-public-ip-address.md#create-a-public-ip-address)します。
3. 同じ Virtual Network (VNet) 内の別のサブネット (新しいサブネット) に、[同じ Virtual Network 内のジャンプ VM を作成](../../windows/quick-create-portal.md#create-virtual-machine)し、ジャンプ VM 経由でサーバーにアクセスします。 ジャンプ VM は、アプリケーションを公開するために使用できます。
4. [Application Gateway](../../../application-gateway/quick-create-portal.md#create-an-application-gateway) を使用してアプリケーションを公開します。
5. 外部 Load Balancer (ELB) を使用してアプリケーションを公開します。
6. ブラウザーと Azure portal を使用して RHEL VM にアクセスするために、[Azure Bastion を使用](../../../bastion/bastion-overview.md)します。 

### <a name="1-create-a-jump-vm-in-a-different-virtual-network-and-access-the-rhel-vm-using-virtual-network-peering-recommended-method"></a>1. 別の Virtual Network にジャンプ VM を作成し、仮想ネットワーク ピアリングを使用して RHEL VM にアクセスする (推奨される方法)

1. [Windows 仮想マシンを作成する](../../windows/quick-create-portal.md#create-virtual-machine) - 新しい Azure リソース グループで Windows VM を作成します。これは、RHEL VM と同じリージョンに存在する必要があります。 新しい Virtual Network にジャンプ VM を作成するため、必要な詳細を入力し、他の構成は既定値のままにしておきます。
2. [Virtual Network のピアリング](../../../virtual-network/tutorial-connect-virtual-networks-portal.md#peer-virtual-networks) - ピアリングは、RHEL VM をジャンプ VM に関連付ける方法です。 仮想ネットワーク ピアリングが成功すると、両方の VM が相互に通信できるようになります。
3. ジャンプ VM の詳細ページに移動し、パブリック IP をコピーします。 パブリック IP を使用してジャンプ VM にログインします。
4. 出力ページから RHEL VM のプライベート IP をコピーし、それを使用して、ジャンプ VM から RHEL VM にログインします。
5. 出力ページからコピーしたアプリの URL を、ジャンプ VM 内のブラウザーに貼り付けます。 このブラウザーで JBoss EAP on Azure の Web ページを表示します。
6. JBoss EAP 管理コンソールにアクセス - ジャンプ VM 内のブラウザーで出力ページからコピーした管理コンソールの URL を貼り付け、JBoss EAP のユーザー名とパスワードを入力してログインします。

### <a name="2-create-a-public-ip-to-access-the-rhel-vm-and-jboss-eap-admin-console"></a>2. RHEL VM と JBoss EAP 管理コンソールにアクセスするためのパブリック IP を作成する。

1. 作成した RHEL VM には、パブリック IP が関連付けられていません。 VM にアクセスするための[パブリック IP を作成](../../../virtual-network/ip-services/virtual-network-public-ip-address.md#create-a-public-ip-address)し、[パブリック IP を VM に関連付ける](../../../virtual-network/ip-services/associate-public-ip-address-vm.md)ことができます。 パブリック IP の作成は、Azure portal または [Azure PowerShell](/powershell/) コマンドまたは [Azure CLI](/cli/azure/install-azure-cli) コマンドを使用して行うことができます。
2. VM のパブリック IP を取得する - VM の詳細ページにアクセスして、パブリック IP をコピーします。 パブリック IP を使用して、VM と JBoss EAP 管理コンソールにアクセスします。
3. JBoss EAP on Azure Web ページを表示する - Web ブラウザーを開き、*http://<PUBLIC_HOSTNAME>:8080/* にアクセスすると、既定の EAP ようこそページが表示されます。
4. JBoss EAP 管理コンソールへのログイン - Web ブラウザーを開き、*http://<PUBLIC_HOSTNAME>:9990* にアクセスします。 JBoss EAP ユーザー名とパスワードを入力してログインします。

### <a name="3-create-a-jump-vm-in-a-different-subnet-new-subnet-in-the-same-vnet-and-access-the-rhel-vm-via-a-jump-vm"></a>3. 同じ VNet 内の別のサブネット (新しいサブネット) にジャンプ VM を作成し、ジャンプ VM 経由で RHEL VM にアクセスする。

1. RHEL VM を含む既存の Virtual Network に[新しいサブネットを追加](../../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)します。
2. Azure で RHEL VM と同じリソース グループ (RG) に [Windows 仮想マシンを作成](../../windows/quick-create-portal.md#create-virtual-machine)します。 必要な詳細を入力し、VNet とサブネット以外の構成は既定値のままにします。 RG 内の既存の VNet を選択し、前の手順で作成したサブネットを選択して、ジャンプ VM とします。
3. ジャンプ VM パブリック IP へのアクセス - 正常にデプロイされたら、VM の詳細ページに移動し、パブリック IP をコピーします。 パブリック IP を使用してジャンプ VM にログインします。
4. RHEL VM にログインする - 出力ページから RHEL VM のプライベート IP をコピーし、それを使用して、ジャンプ VM から RHEL VM にログインします。
5. JBoss EAP ウェルカム ページにアクセス - ジャンプ VM で、ブラウザーを開き、デプロイの出力ページからコピーしたアプリの URL を貼り付けます。
6. JBoss EAP 管理コンソールにアクセス - ジャンプ VM 内のブラウザーで出力ページからコピーした管理コンソールの URL を貼り付けて、JBoss EAP 管理コンソールにアクセスし、JBoss EAP のユーザー名とパスワードを入力してログインします。

### <a name="4-expose-the-application-using-an-external-load-balancer"></a>4. 外部 Load Balancer を使用してアプリケーションを公開する

1. [Application Gateway を作成](../../../application-gateway/quick-create-portal.md#create-an-application-gateway)する - RHEL VM のポートにアクセスするには、別のサブネットに Application Gateway を作成します。 サブネットには、Application Gateway のみが含まれている必要があります。
2. "*フロントエンド*" パラメーターを設定する - パブリック IP または両方を選択して、必要な詳細を指定します。 *[バックエンド]* セクションで、 **[バックエンド プールの追加]** オプションを選択し、Application Gateway のバックエンド プールに RHEL VM を追加します。
3. アクセスポートを設定する - *[構成]* セクションで、RHEL VM のポート 8080 および 9990 にアクセスするためのルーティング規則を追加します。
4. Application Gateway のパブリック IP をコピーする - 必要な構成で Application Gateway を作成したら、概要ページにアクセスして、Application Gateway のパブリック IP をコピーします。
5. JBoss EAP on Azure Web ページを表示する - Web ブラウザーを開き、*http://<PUBLIC_IP_AppGateway>:8080* にアクセスすると、既定の EAP ウェルカム ページが表示されます。
6. JBoss EAP 管理コンソールにログインする - Web ブラウザーを開き、*http://<PUBLIC_IP_AppGateway>:9990* にアクセスします。 JBoss EAP ユーザー名とパスワードを入力してログインします。

### <a name="5-use-an-external-load-balancer-elb-to-access-your-rhel-vmvirtual-machine-scale-sets"></a>5. 外部 Load Balancer (ELB) を使用して RHEL VM または仮想マシン スケール セットにアクセスする

1. RHEL VM のポートにアクセスするための [Load Balancer を作成](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard#create-load-balancer-resources)します。 外部 Load Balancer のデプロイに必要な詳細を入力し、他の構成は既定値のままにします。 ELB 構成の場合は、SKU を Basic のままにしておきます。
2. Load Balancer 規則を追加する - Load Balancer が正常に作成されたら、[Load Balancer リソースを作成](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard#create-load-balancer-resources)し、RHEL VM のポート 8080 および 9990 にアクセスするための Load Balancer 規則を追加します。
3. RHEL VM を Load Balancer のバックエンド プールに追加する - 設定セクションで *[バックエンド プール]* をクリックし、前の手順で作成したバックエンド プールを選択します。 *[関連付け先]* オプションに対応する VM を選択し、RHEL VM を追加します。
4. Load Balancer のパブリック IP を取得する - Load Balancer の概要ページにアクセスし、Load Balancer のパブリック IP をコピーします。
5. JBoss EAP on Azure Web ページを表示する - Web ブラウザーを開き、*http://<PUBLIC_IP_LoadBalancer>:8080/* にアクセスすると、既定の EAP ウェルカム ページが表示されます。
6. JBoss EAP 管理コンソールにログインする - Web ブラウザーを開き、*http://<PUBLIC_IP_LoadBalancer>:9990* にアクセスします。 JBoss EAP ユーザー名とパスワードを入力してログインします。

### <a name="6-use-azure-bastion-to-connect"></a>6. Azure Bastion を使用して接続する 

1. RHEL VM が配置されている VNet の Azure Bastion ホストを作成します。 Bastion サービスは、SSH を使用して RHEL VM に自動的に接続します。
2. VM に閲覧者ロールを作成し、VM のプライベート IP を使用して NIC を作成し、Azure Bastion リソースを作成します。
3. RHEL 受信ポートは SSH (22) 用に開かれています。 
4. Azure portal でユーザー名とパスワードを使用して接続します。 [接続] をクリックし、ドロップダウンから [Bastion] を選択します。 次に、[接続] を選択して RHEL VM に接続します。 秘密キー ファイルまたは Azure Key Vault に格納されている秘密キーを使用して接続できます。

## <a name="azure-platform"></a>Azure プラットフォーム

- **検証エラー** - パラメーターの条件が満たされない場合 (たとえば、管理者パスワードの条件が満たされなかった場合) や、パラメーター セクションに必須のパラメーターが指定されていない場合は、デプロイが開始されません。 *[作成]* をクリックする前に、パラメーターの詳細を確認することができます。
- **リソース プロビジョニング エラー** - デプロイが開始されると、デプロイされているリソースがデプロイ ページに表示されます。 パラメーターの検証プロセス後にデプロイ エラーが発生した場合は、より詳細なエラーメッセージが表示されます。 
- **VM プロビジョニング エラー** - **VM カスタム スクリプト拡張機能** リソース ポイントでデプロイが失敗した場合は、VM ログ ファイルにより詳細なエラー メッセージが表示されます。 詳細なトラブルシューティングについては、次のセクションを参照してください。

## <a name="troubleshooting-eap-deployment-extension"></a>EAP デプロイ拡張機能のトラブルシューティング

これらのプランでは、VM のカスタム スクリプト拡張機能を使用して JBoss EAP をデプロイし、JBoss EAP 管理ユーザーを構成します。 次のようないくつかの理由により、デプロイがこの時点で失敗する可能性があります。

- RHSM または EAP のエンタイトルメントが無効である
- JBoss EAP または RHEL OS エンタイトルメント プール ID が無効である
- Azure Marketplace T&C が RHEL VM について受け入れられていない

問題のトラブルシューティングを行うには、次の手順に従います。

1. SSH を使用して、プロビジョニングされた仮想マシンにログインします。 Azure portal の VM 概要ページで VM のパブリック IP を取得できます。
1. root ユーザーに切り替えます

    ```cli
    sudo su -
    ```

1. ダイアログが表示されたら、VM 管理者のパスワードを入力します。
1. ディレクトリをログ ディレクトリに変更します

    ```cli
    cd /var/lib/waagent/custom-script/download/0 #for EAP on RHEL stand-alone VM
    ```

    ```cli
    cd /var/lib/waagent/custom-script/download/1 #for EAP on RHEL clustered VM
    ```

1. eap.log ログ ファイルのログを確認します。

    ```cli
    more eap.log
    ```

ログ ファイルには、デプロイ エラーの理由や考えられるソリューションなどの詳細が含まれています。 RHSM のアカウントまたはエンタイトルメントが原因でデプロイが失敗した場合は、「サポートとサブスクリプションに関する注記」セクションを参照して、前提条件を満たす必要があります。 その後、やり直してください。 EAP on RHEL クラスター化プランをデプロイするときは、デプロイがクォータ制限に達していないことを確認してください。 デプロイのインスタンス数を指定する前に、リージョンの vCPU と VM シリーズの vCPU クォータを確認することが重要です。 サブスクリプションまたはリージョンに十分なクォータ制限がない場合は、Azure portal から[クォータを要求](../../../azure-portal/supportability/regional-quota-requests.md)してください。

VM カスタム スクリプト拡張機能のトラブルシューティングの詳細については、「[Linux 仮想マシンで Azure カスタム スクリプト拡張機能 v2 を使用する](../../extensions/custom-script-linux.md)」を参照してください。

## <a name="resource-links-and-support"></a>リソースのリンクとサポート

サポート関連の質問、問題、またはカスタマイズの要件については、[Red Hat サポート](https://access.redhat.com/support)または [Microsoft Azure サポート](https://ms.portal.azure.com/?quickstart=true#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)にお問い合わせください。

* [JBoss EAP](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/) の詳細を確認する
* [JBoss EAP on Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [Azure App Service 上の JBoss EAP](/azure/developer/java/ee/jboss-on-azure) 
* [Azure ハイブリッド特典](../../windows/hybrid-use-benefit-licensing.md)
* [Red Hat Subscription Management](https://access.redhat.com/products/red-hat-subscription-management)
* [Red Hat on Azure に関する Microsoft ドキュメント](./overview.md)

## <a name="next-steps"></a>次のステップ

* [Azure Marketplace](https://aka.ms/AMP-JBoss-EAP) から RHEL の VM または仮想マシン スケール セットに JBoss EAP をデプロイする
* [Azure クイック スタート](https://aka.ms/Quickstart-JBoss-EAP)から RHEL の VM または仮想マシン スケール セットに JBoss EAP をデプロイする
* [Azure App Service](../../../app-service/configure-language-java.md) 向けの Java アプリを構成する
* [JBoss EAP を Azure App Service にデプロイする方法](https://github.com/JasonFreeberg/jboss-on-app-service)のチュートリアル
* Azure [App Service Migration Assistance](https://azure.microsoft.com/services/app-service/migration-assistant/) を使用する
* Red Hat [Migration Toolkit for Applications](https://developers.redhat.com/products/mta) を使用する