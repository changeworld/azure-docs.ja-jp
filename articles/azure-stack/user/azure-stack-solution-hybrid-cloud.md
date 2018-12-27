---
title: Azure と Azure Stack を使用してハイブリッド クラウドをデプロイする | Microsoft Docs
description: Azure と Azure Stack を使用してハイブリッド クラウドをデプロイする方法について説明します。
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
ms.date: 10/18/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: e4da34be5b1e9eb2675f540fe4069cfd3dc1d056
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2018
ms.locfileid: "49409950"
---
# <a name="tutorial-deploy-a-hybrid-cloud-solution-with-azure-and-azure-stack"></a>チュートリアル: Azure と Azure Stack を使用してハイブリッド クラウド ソリューションをデプロイする

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

このチュートリアルでは、Azure パブリック クラウドと Azure Stack プライベート クラウドを使用するハイブリッド クラウド ソリューションのデプロイ方法について説明します。

ハイブリッド クラウド ソリューションを使用することで、プライベート クラウドが持つコンプライアンス面でのメリットとパブリック クラウドが持つスケーラビリティとを融合することができます。 加えて開発者は、Microsoft デベロッパーのエコシステムを活用し、クラウド環境とオンプレミス環境にそのスキルを活かすことができます。

## <a name="overview-and-assumptions"></a>概要と前提条件

このチュートリアルに従うことにより、開発者がパブリック クラウドとプライベート クラウドにまったく同じ Web アプリケーションをデプロイできるようにするワークフローを構築できます。 このアプリケーションは、プライベート クラウド上にホストされた、インターネットを介したルーティングが不可能なネットワークにアクセスできるようになります。 これらの Web アプリケーションは監視され、トラフィックが急増すると、トラフィックをパブリック クラウドにリダイレクトするように DNS レコードがプログラムによって書き換えられます。 急増前の水準までトラフィックが減少すると、トラフィックは再びプライベート クラウドへルーティングされます。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> - ハイブリッド接続の SQL Server データベース サーバーをデプロイする。
> - グローバル Azure 内の Web アプリをハイブリッド ネットワークに接続する。
> - クラウド間スケーリング向けに DNS を構成する。
> - クラウド間スケーリング向けに SSL 証明書を構成する。
> - Web アプリケーションを構成してデプロイする。
> - Traffic Manager プロファイルを作成し、クラウド間スケーリング向けに構成する。
> - トラフィックの増加に関して Application Insights の監視とアラートを設定する。
> - グローバル Azure と Azure Stack の間で自動トラフィック切り替えを構成する。

### <a name="assumptions"></a>前提条件

このチュートリアルは、グローバル Azure と Azure Stack についての基本知識があることを前提にしています。 チュートリアルを開始する前に、より詳しい情報を確認しておきたい場合は、以下の記事をお読みください。

 - [Azure 入門](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure Stack の主要概念](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

このチュートリアルは、Azure サブスクリプションをお持ちであることも前提としています。 サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、次の要件を満たせるようにしておいてください。

- Azure Stack 統合システムのサブスクリプションまたは Azure Stack Development Kit (ASDK)。 Azure Stack Development Kit をデプロイするには、[インストーラーを使用した ASDK のデプロイ](../asdk/asdk-deploy.md)に関するページの手順に従ってください。
- ご利用の Azure Stack 環境に次のものがインストールされている必要があります。
  - Azure App Service。 Azure Stack のオペレーターと協力して、Azure App Service をご自分の環境にデプロイし、構成してください。 このチュートリアルでは、利用可能な専用 worker ロールが App Service に少なくとも 1 つ必要です。
  - Windows Server 2016 イメージ
  - Microsoft SQL Server を含んだ Windows Server 2016 のイメージ
  - 適切なプランとオファー
 - Web アプリケーションのドメイン名。 ドメイン名がない場合は、GoDaddy、Bluehost、InMotion などのドメイン プロバイダーから購入できます。
- 信頼のおける証明機関 (LetsEncrypt など) から取得したドメインの SSL 証明書。
- SQL Server データベースと通信し、Application Insights をサポートする Web アプリケーション。 GitHub から [dotnetcore-sqldb-tutorial](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial) サンプル アプリをダウンロードできます。
- Azure 仮想ネットワークと Azure Stack 仮想ネットワークの間のハイブリッド ネットワーク。 詳細な手順については、[Azure と Azure Stack を使用したハイブリッド クラウド接続の構成](azure-stack-solution-hybrid-connectivity.md)に関するページを参照してください。

- Azure Stack にプライベート ビルド エージェントが存在する、継続的インテグレーション/継続的デプロイ (CI/CD) のハイブリッド パイプライン。 詳細な手順については、[Azure および Azure Stack アプリケーション向けのハイブリッド クラウド ID の構成](azure-stack-solution-hybrid-identity.md)に関するページを参照してください。

## <a name="deploy-a-hybrid-connected-sql-server-database-server"></a>ハイブリッド接続の SQL Server データベース サーバーをデプロイする

1. Azure Stack ユーザー ポータルにサインインします。

2. **[ダッシュボード]** の **[Marketplace]** を選択します。

    ![Auzure Stack Marketplace](media/azure-stack-solution-hybrid-cloud/image1.png)

3. **[Marketplace]** で **[Compute]\(計算\)** を選択し、**[More]\(その他\)** を選択します。 **[More]\(その他\)** から **[Free SQL Server License: SQL Server 2017 Developer on Windows Server]** イメージを選択します。

    ![仮想マシン イメージの選択](media/azure-stack-solution-hybrid-cloud/image2.png)

4. **[Free SQL Server License: SQL Server 2017 Developer on Windows Server]** で **[作成]** を選択します。

5. **[基本] > [基本設定の構成]** で、仮想マシン (VM) の**名前**、SQL Server SA の**ユーザー名**、その SA の**パスワード**を指定します。  **[サブスクリプション]** ボックスの一覧から、デプロイ先のサブスクリプションを選択します。 **[リソース グループ]** では **[Choose existing]\(既存の選択\)** を使用し、Azure Stack Web アプリと同じリソース グループに VM を配置します。

    ![VM の基本設定を構成する](media/azure-stack-solution-hybrid-cloud/image3.png)

6. **[サイズ]** で VM のサイズを選択します。 このチュートリアルでは、A2_Standard または DS2_V2_Standard をお勧めします。

7. **[設定] > [オプション機能の構成]** で、次の設定を構成します。

    - **[ストレージ アカウント]**。 新しいアカウントが必要な場合は、作成します。
    - **Virtual Network**

      > [!Important]  
      > SQL Server VM が VPN ゲートウェイと同じ仮想ネットワーク上にデプロイされていることを確認してください。

    - **パブリック IP アドレス**。 既定の設定を使用できます。
    - **ネットワーク セキュリティ グループ** (NSG)。 新しい NSG を作成します。
    - **拡張機能と監視**。 既定の設定のままにします。
    - **診断ストレージ アカウント**。 新しいアカウントが必要な場合は、作成します。
    - **[OK]** を選択して構成を保存します。

    ![オプション機能を構成する](media/azure-stack-solution-hybrid-cloud/image4.png)

1. **[SQL Server の設定]** で、次の設定を構成します。
   - **[SQL 接続]** で **[パブリック (インターネット)]** を選択します。
   - **[ポート]** は、既定値 (**1433**) のままにします。
   - **[SQL 認証]** には **[有効]** を選択します。

     > [!Note]  
     > SQL 認証を有効にすると、**[基本]** で構成した "SQLAdmin" の情報が自動設定されます。

   - その他の設定は、既定値のままにしてください。 **[OK]** を選択します。

    ![SQL Server の設定を構成する](media/azure-stack-solution-hybrid-cloud/image5.png)

9. **[概要]** で仮想マシンの構成を確認し、**[OK]** を選択してデプロイを開始します。

    ![構成の概要](media/azure-stack-solution-hybrid-cloud/image6.png)

10. 新しい VM の作成には多少時間がかかります。 VM の状態は、**[仮想マシン]** で確認できます。

    ![仮想マシン](media/azure-stack-solution-hybrid-cloud/image7.png)

## <a name="create-web-apps-in-azure-and-azure-stack"></a>Azure と Azure Stack に Web アプリを作成する

Azure App Service は、Web アプリケーションの実行と管理をシンプルにします。 Azure Stack は Azure と一貫性があるため、App Service はどちらの環境でも実行できます。 App Service を使用してアプリケーションをホストしてみましょう。

### <a name="create-web-apps"></a>Web アプリを作成する

1. 「[Azure で App Service プランを管理する](https://docs.microsoft.com/azure/app-service/app-service-plan-manage#create-an-app-service-plan)」の手順に従って、Azure で Web アプリを作成します。 Web アプリは、ご利用のハイブリッド ネットワークと同じサブスクリプションおよびリソース グループに配置してください。

2. 前の手順 (1) を Azure Stack でも行います。

### <a name="add-route-for-azure-stack"></a>Azure Stack 用のルートを追加する

Azure Stack 上の App Service は、ユーザーがアプリケーションにアクセスできるよう、パブリック インターネットからルーティングできなければなりません。 Azure Stack にインターネットからアクセスできる場合は、Azure Stack Web アプリの公開 IP アドレスまたは URL を書き留めておいてください。

ASDK を使用している場合は、[静的 NAT のマッピングを構成](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-vpn-connection-one-node#configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal)することで、仮想環境の外部に App Service を公開することができます。

### <a name="connect-a-web-app-in-azure-to-a-hybrid-network"></a>Azure 内の Web アプリをハイブリッド ネットワークに接続する

Azure 内の Web フロントエンドと Azure Stack 内の SQL Server データベースの間で接続性を確保するために、Azure と Azure Stack の間のハイブリッド ネットワークに Web アプリを接続します。 接続を有効にするためには、次の作業が必要となります。

- ポイント対サイト接続を構成する
- Web アプリを構成する
- Azure Stack 内のローカル ネットワーク ゲートウェイに変更を加える

### <a name="configure-the-azure-virtual-network-for-point-to-site-connectivity"></a>ポイント対サイト接続のために Azure 仮想ネットワークを構成する

Azure App Service と統合するためには、ハイブリッド ネットワークの Azure 側にある仮想ネットワーク ゲートウェイでポイント対サイト接続を許可する必要があります。

1. Azure の仮想ネットワーク ゲートウェイ ページに移動します。 **[設定]** で、**[ポイント対サイトの構成]** を選択します。

    ![ポイント対サイト オプション](media/azure-stack-solution-hybrid-cloud/image8.png)

2. **[今すぐ構成]** を選択して、ポイント対サイトの構成を行います。

    ![ポイント対サイトの構成を開始](media/azure-stack-solution-hybrid-cloud/image9.png)

3. **[ポイント対サイト]** 構成ページで、使用するプライベート IP アドレス範囲を **[アドレス プール]** に入力します。

   > [!Note]  
   > 指定する範囲が、ハイブリッド ネットワークのグローバル Azure コンポーネントまたは Azure Stack コンポーネントのサブネットによって既に使用されているアドレス範囲と重複しないようにしてください。

   **[トンネルの種類]** の **[IKEv2 VPN]** チェック ボックスをオフにします。 **[保存]** を選択して、ポイント対サイトの構成を完了します。

   ![ポイント対サイトの設定](media/azure-stack-solution-hybrid-cloud/image10.png)

### <a name="integrate-the-azure-app-service-application-with-the-hybrid-network"></a>Azure App Service アプリケーションをハイブリッド ネットワークと統合する

1. Azure VNet にアプリケーションを接続するために、「[VNet 統合の有効化](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet#enabling-vnet-integration)」の手順に従います。

2. Web アプリケーションをホストする App Service プランの **[設定]** に移動します。 **[設定]** の **[ネットワーク]** を選択します。

    ![ネットワークを構成する](media/azure-stack-solution-hybrid-cloud/image11.png)

3. **[VNET 統合]** の **[管理するにはここをクリック]** を選択します。

    ![VNET 統合を管理](media/azure-stack-solution-hybrid-cloud/image12.png)

4. 構成する VNET を選択します。 **[IP アドレスが VNET にルーティングされました]** で、Azure VNet、Azure Stack VNet、ポイント対サイトのアドレス空間に使用する IP アドレス範囲を入力します。 **[保存]** を選択し、これらの設定を確認して保存します。

    ![ルーティングする IP アドレス範囲](media/azure-stack-solution-hybrid-cloud/image13.png)

App Service と Azure VNet の統合方法の詳細については、「[アプリを Azure 仮想ネットワークに統合する](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)」を参照してください。

### <a name="configure-the-azure-stack-virtual-network"></a>Azure Stack 仮想ネットワークを構成する

App Service のポイント対サイト アドレス範囲からのトラフィックをルーティングするために、Azure Stack 仮想ネットワーク内のローカル ネットワーク ゲートウェイを構成する必要があります。

1. Azure Stack で **[ローカル ネットワーク ゲートウェイ]** に移動します。 **[設定]** で **[構成]** を選択します。

    ![ゲートウェイの構成オプション](media/azure-stack-solution-hybrid-cloud/image14.png)

2. Azure 内の仮想ネットワーク ゲートウェイに使用されるポイント対サイトのアドレス範囲を **[アドレス空間]** に入力します。**[保存]** を選択し、この構成を確認して保存します。

    ![ポイント対サイトのアドレス空間](media/azure-stack-solution-hybrid-cloud/image15.png)

## <a name="configure-dns-for-cross-cloud-scaling"></a>クラウド間スケーリング向けに DNS を構成する

クラウド間アプリケーション向けに DNS を適切に構成することで、ユーザーはグローバル Azure と Azure Stack の Web アプリ インスタンスにアクセスできます。 また、このチュートリアルの DNS 構成を使えば、負荷が増減したときに Azure Traffic Manager でトラフィックをルーティングすることも可能です。

このチュートリアルでは、Azure DNS を使用して DNS を管理します  (App Service ドメインは機能しません)。

### <a name="create-subdomains"></a>サブドメインを作成する

Traffic Manager は DNS の CNAME に依存しているため、エンドポイントに対して適切にトラフィックをルーティングするためには、サブドメインが必要となります。 DNS レコードとドメインのマッピングの詳細については、「[Traffic Manager を使用したドメインのマップ](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager-custom-domain-name)」を参照してください。

Azure エンドポイントについては、ユーザーが Web アプリにアクセスするために使用できるサブドメインを作成します。 このチュートリアルでは、**app.northwind.com** を使用できますが、この値はご自身のドメインに合わせてカスタマイズする必要があります。

また、Azure Stack エンドポイントについても、A レコードを使用してサブドメインを作成する必要があります。 **azurestack.northwind.com** を使用できます。

### <a name="configure-a-custom-domain-in-azure"></a>Azure でカスタム ドメインを構成する

1. [Azure App Service に CNAME をマップ](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record)して、**app.northwind.com** ホスト名を Azure Web アプリに追加します。

### <a name="configure-custom-domains-in-azure-stack"></a>Azure Stack でカスタム ドメインを構成する

1. [Azure App Service に A レコードをマップ](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-an-a-record)して、ホスト名 **azurestack.northwind.com** を Azure Stack Web アプリに追加します。 App Service アプリケーションには、インターネットを介したルーティングが可能な IP アドレスを使用します。

2. [Azure App Service に CNAME をマップ](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record)して、ホスト名 **app.northwind.com** を Azure Stack Web アプリに追加します。 CNAME のターゲットとして、前の手順 (1) で構成したホスト名を使用してください。

## <a name="configure-ssl-certificates-for-cross-cloud-scaling"></a>クラウド間スケーリング向けに SSL 証明書を構成する

Web アプリケーションによって収集される機密性の高いデータについては、SQL データベースへの転送中も、SQL データベースに保存されている間も、セキュリティを確保する必要があります。

すべての受信トラフィックについて SSL 証明書を使用するように、Azure の Web アプリケーションと Azure Stack の Web アプリケーションを構成します。

### <a name="add-ssl-to-azure-and-azure-stack"></a>Azure と Azure Stack に SSL を追加する

Azure に SSL を追加するには、次の手順に従います。

1. 作成したサブドメインに対し、取得した SSL 証明書が有効であることを確認します  (ワイルドカード証明書を使用してもかまいません)。

2. Azure で、[Azure Web Apps への既存のカスタム SSL 証明書のバインド](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl)に関する記事のセクション「**Web アプリの準備**」と「**SSL 証明書のバインド**」の手順に従います。 **[SSL の種類]** として **[SNI ベースの SSL]** を選択します。

3. すべてのトラフィックを HTTP ポートにリダイレクトします。 [Azure Web Apps への既存のカスタム SSL 証明書のバインド](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl)に関する記事のセクション「**HTTPS の適用**」の手順に従ってください。

Azure Stack に SSL を追加するには、次の手順に従います。

- Azure で使用した手順 1. から手順 3. を繰り返します。

## <a name="configure-and-deploy-the-web-application"></a>Web アプリケーションを構成してデプロイする

適切な Application Insights インスタンスにテレメトリをレポートするようアプリケーション コードを構成し、適切な接続文字列を使って Web アプリケーションを構成します。 Application Insights の詳細については、「[Application Insights とは何か?](https://docs.microsoft.com/azure/application-insights/app-insights-overview)」を参照してください。

### <a name="add-application-insights"></a>Application Insights を追加する

1. Microsoft Visual Studio で Web アプリケーションを開きます。

2. プロジェクトに [Application Insights を追加](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core#add-application-insights-telemetry)し、Web トラフィックが増減したときのアラートを生成するために Application Insights によって使用されるテレメトリが転送されるようにします。

### <a name="configure-dynamic-connection-strings"></a>動的接続文字列を構成する

Web アプリケーションの各インスタンスは、異なる方法で SQL データベースに接続することになります。 Azure 内のアプリケーションでは SQL Server 仮想マシン (VM) のプライベート IP アドレスが使用され、Azure Stack 内のアプリケーションでは SQL Server VM のパブリック IP アドレスが使用されます。

> [!Note]  
> Azure Stack 統合システムでは、インターネットを介したルーティングが可能なアドレスをパブリック IP アドレスとして使用しないようにしてください。 Azure Stack Development Kit (ASDK) では、パブリック IP アドレスを ASDK の外部でルーティングすることはできません。

App Service の環境変数を使用すれば、アプリケーションの各インスタンスに異なる接続文字列を渡すことができます。

1. Visual Studio でアプリケーションを開きます。

2. Startup.cs を開いて、次のコード ブロックを見つけます。

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
    ```

3. 前のコード ブロックを次のコードに置き換えます。このコードでは、appsettings.json ファイルで定義されている接続文字列が使用されます。

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
     // Automatically perform database migration
     services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
    ```

### <a name="configure-app-service-application-settings"></a>App Service アプリケーション設定を構成する

1. Azure と Azure Stack 用の接続文字列を作成します。 IP アドレス以外は、同じ文字列を使用してください。

2. Azure と Azure Stack で、Web アプリケーションの[アプリケーション設定として](https://docs.microsoft.com/azure/app-service/web-sites-configure)適切な接続文字列を追加します。名前のプレフィックスとしては `SQLCONNSTR\_` を使用してください。

3. Web アプリの設定を**保存**して、アプリケーションを再起動します。

## <a name="enable-automatic-scaling-in-global-azure"></a>グローバル Azure で自動スケーリングを有効にする

App Service 環境で Web アプリケーションを作成するときは、1 つのインスタンスから始めます。 自動的にスケールアウトしてインスタンスを追加することで、アプリ用のコンピューティング リソースを増やすことができます。 同様に、自動的にスケールインして、アプリに必要なインスタンスの数を減らすことができます。

> [!Note]  
> スケールアウトとスケールインを構成するには、App Service プランが必要です。 プランをお持ちでない場合は、作成したうえで次の手順を開始してください。

### <a name="enable-automatic-scale-out"></a>自動スケールアウトを有効にする

1. Azure で、スケールアウトしたいサイトの App Service プランを見つけて、**[スケールアウト (App Service プラン)]** を選択します。

    ![スケールアウト](media/azure-stack-solution-hybrid-cloud/image16.png)

2. **[自動スケールの有効化]** を選択します。

    ![自動スケールの有効化](media/azure-stack-solution-hybrid-cloud/image17.png)

3. **[自動スケール設定の名前]** に名前を入力します。 **既存**の自動スケール ルールで、**[メトリックに基づいてスケーリングする]** を選択します。 **[インスタンスの制限]** で、**[最小]** を 1、**[最大]** を 10、**[既定]** を 1 に設定します。

    ![自動スケールを構成](media/azure-stack-solution-hybrid-cloud/image18.png)

4. **[+ ルールの追加]** を選択します。

5. **[メトリック ソース]** で **[現在のリソース]** を選択します。 このルールには、次の条件とアクションを使用します。

**条件**

1. **[時間の集計]** で **[平均]** を選択します。

2. **[メトリック名]** で **[CPU の割合]** を選択します。

3. **[演算子]** で **[より大きい]** を選択します。

   - **[しきい値]** を **50** に設定します。
   - **[期間]** を **10** に設定します。

**アクション**

1. **[操作]** で **[カウントを増やす量]** を選択します。

2. **[インスタンス数]** を **2** に設定します。

3. **[クール ダウン]** を **5** に設定します。

4. **[追加]** を選択します。

5. **[+ ルールの追加]** を選択します。

6. **[メトリック ソース]** で **[現在のリソース]** を選択します。

   > [!Note]  
   > 現在のリソースには、ご利用の App Service プランの名前/GUID が表示され、**[リソースの種類]** と **[リソース]** ドロップダウン リストは淡色表示されます。

### <a name="enable-automatic-scale-in"></a>自動スケールインを有効にする

トラフィックが減少すると、Azure Web アプリケーションで自動的にアクティブ インスタンス数を減らして、コストを削減することができます。 このアクションはスケールアウトほど積極的には実行されません。アプリケーション ユーザーへの影響を最小限に抑えるためです。

1. **[既定]** のスケールアウト条件に移動し、**[+ ルールの追加]** を選択します。 このルールには、次の条件とアクションを使用します。

**条件**

1. **[時間の集計]** で **[平均]** を選択します。

2. **[メトリック名]** で **[CPU の割合]** を選択します。

3. **[演算子]** で **[より小さい]** を選択します。

   - **[しきい値]** を **30** に設定します。
   - **[期間]** を **10** に設定します。

**アクション**

1. **[操作]** で **[カウントを減らす量]** を選択します。

   - **[インスタンス数]** を **1** に設定します。
   - **[クール ダウン]** を **5** に設定します。

2. **[追加]** を選択します。

## <a name="create-a-traffic-manager-profile-and-configure-cross-cloud-scaling"></a>Traffic Manager プロファイルを作成し、クラウド間スケーリングを構成する

Azure で Traffic Manager プロファイルを作成し、エンドポイントを構成してクラウド間スケーリングを有効にします。

### <a name="create-traffic-manager-profile"></a>Traffic Manager プロファイルを作成する

1. **[リソースの作成]** を選択します。
2. **[ネットワーク]** を選択します。
3. **[Traffic Manager プロファイル]** を選択し、次を構成します。

   - **[名前]** に、プロファイルの名前を入力します。 この名前は、trafficmanager.net ゾーン内で一意であることが**必要**です。新しい DNS 名を作成するときに使用されます (例: northwindstore.trafficmanager.net)。
   - **[ルーティング方法]** で **[重み付け]** を選択します。
   - **[サブスクリプション]** で、このプロファイルを作成するサブスクリプションを選択します。
   - **[リソース グループ]** で、このプロファイルの新しいリソース グループを作成します。
   - **[リソース グループの場所]** で、リソース グループの場所を選択します。 これはリソース グループの場所を指定する設定であり、グローバルにデプロイされる Traffic Manager プロファイルには影響しません。

4. **作成**を選択します。

    ![Traffic Manager プロファイルを作成する](media/azure-stack-solution-hybrid-cloud/image19.png)

 Traffic Manager プロファイルのグローバル デプロイが完了すると、その作成先となったリソース グループのリソース一覧にそのプロファイルが表示されます。

### <a name="add-traffic-manager-endpoints"></a>Traffic Manager エンドポイントの追加

1. 作成した Traffic Manager プロファイルを検索します  (プロファイルのリソース グループに移動した場合は、プロファイルを選択してください)。

2. **[Traffic Manager プロファイル]** の **[設定]** で、**[エンドポイント]** を選択します。

3. **[追加]** を選択します。

4. Azure Stack について、**[エンドポイントの追加]** で次の設定を使用します。

   - **[Type] (種類)** で、**[外部エンドポイント]** を選択します。
   - このエンドポイントの **[名前]** を入力します。
   - **完全修飾ドメイン名 (FQDN) または IP** として、Azure Stack Web アプリの外部 URL を入力します。
   - **[重み]** は、既定値 (**1**) のままにします。 これにより、このエンドポイントが正常な状態である場合、すべてのトラフィックがそのエンドポイントに送信されるようになります。
   - **[無効として追加]** はオフのままにします。

5. **[OK]** を選択して、Azure Stack エンドポイントを保存します。

次に、Azure エンドポイントを構成します。

1. **[Traffic Manager プロファイル]** で **[エンドポイント]** を選択します。
2. **[+追加]** を選択します。
3. Azure について、**[エンドポイントの追加]** で次の設定を使用します。

   - **[Type] (種類)** で、**[Azure エンドポイント]** を選択します。
   - このエンドポイントの **[名前]** を入力します。
   - **[ターゲット リソースの種類]** で、**[App Service]** を選択します。
   - **[ターゲット リソース]** で **[アプリ サービスの選択]** を選択し、同じサブスクリプションにある Web アプリの一覧を表示します。
   - **[リソース]** で、最初のエンドポイントとして追加する App Service を選択します。
   - **[重み]** に **2** を選択します。 これにより、プライマリ エンドポイントが正常ではない場合や、トリガーされたらトラフィックをルーティングするルール/アラートがある場合、すべてのトラフィックがそのエンドポイントに送信されるようになります。
   - **[無効として追加]** はオフのままにします。

4. **[OK]** を選択して、Azure エンドポイントを保存します。

構成したエンドポイントはどちらも、**[Traffic Manager プロファイル]** で **[エンドポイント]** を選択すると表示されます。 次の画面キャプチャの例には、2 つのエンドポイントが、それぞれの状態および構成情報と共に表示されています。

![エンドポイント](media/azure-stack-solution-hybrid-cloud/image20.png)

## <a name="set-up-application-insights-monitoring-and-alerting"></a>Application Insights の監視とアラートを設定する

Azure Application Insights を使用すると、アプリケーションを監視し、構成した条件に応じてアラートを送信することができます。 たとえば、アプリケーションが利用できなくなった、障害が発生した、パフォーマンスの問題が生じたなどの例が間陰られます。

アラートの作成には、Application Insights のメトリックを使用します。 これらのアラートがトリガーされると、Web アプリケーション インスタンスが自動的に Azure Stack から Azure に切り替わってスケールアウトし、その後、Azure Stack に戻ってスケールインします。

### <a name="create-an-alert-from-metrics"></a>メトリックに基づくアラートを作成する

このチュートリアルのリソース グループに移動して Application Insights インスタンスを選択し、**[Application Insights]** を開きます。

![Application Insights](media/azure-stack-solution-hybrid-cloud/image21.png)

このビューを使用してスケールアウト アラートとスケールイン アラートを作成します。

### <a name="create-the-scale-out-alert"></a>スケールアウト アラートを作成する

1. **[構成]** で **[アラート (クラシック)]** を選択します。
2. **[メトリック アラートの追加 (クラシック)]** を選択します。
3. **[ルールの追加]** で、次を構成します。

   - **[名前]** に「**Burst into Azure Cloud**」と入力します。
   - **[説明]** は省略できます。
   - **[ソース]** の **[アラート対象]** で **[メトリック]** を選択します。
   - **[条件]** で、自分のサブスクリプション、Traffic Manager プロファイルのリソース グループ、リソースに使用する Traffic Manager プロファイルの名前を選択します。

4. **[メトリック]** で **[要求率]** を選択します。
5. **[条件]** で **[より大きい]** を選択します。
6. **[しきい値]** に「**2**」を入力します。
7. **[期間]** で **[直近 5 分]** を選択します。
8. **[通知手段]** で次のように設定します。
   - **[所有者、共同作成者、閲覧者に電子メールを送信]** のチェック ボックスをオンにします。
   - **[追加する管理者の電子メール]** にメール アドレスを入力します。

9. メニュー バーで **[保存]** を選択します。

### <a name="create-the-scale-in-alert"></a>スケールイン アラートを作成する

1. **[構成]** で **[アラート (クラシック)]** を選択します。
2. **[メトリック アラートの追加 (クラシック)]** を選択します。
3. **[ルールの追加]** で、次を構成します。

   - **[名前]** に「**Scale back into Azure Stack**」と入力します。
   - **[説明]** は省略できます。
   - **[ソース]** の **[アラート対象]** で **[メトリック]** を選択します。
   - **[条件]** で、自分のサブスクリプション、Traffic Manager プロファイルのリソース グループ、リソースに使用する Traffic Manager プロファイルの名前を選択します。

4. **[メトリック]** で **[要求率]** を選択します。
5. **[条件]** で **[より小さい]** を選択します。
6. **[しきい値]** に「**2**」を入力します。
7. **[期間]** で **[直近 5 分]** を選択します。
8. **[通知手段]** で次のように設定します。
   - **[所有者、共同作成者、閲覧者に電子メールを送信]** のチェック ボックスをオンにします。
   - **[追加する管理者の電子メール]** にメール アドレスを入力します。

9. メニュー バーで **[保存]** を選択します。

次の画面キャプチャには、スケールアウトとスケールインのアラートが示されています。

   ![アラート (クラシック)](media/azure-stack-solution-hybrid-cloud/image22.png)

## <a name="redirect-traffic-between-azure-and-azure-stack"></a>Azure と Azure Stack の間でトラフィックをリダイレクトする

Azure と Azure Stack の間で行われる Web アプリのトラフィック切り替えには、手動または自動の切り替えを構成できます。

### <a name="configure-manual-switching-between-azure-and-azure-stack"></a>Azure と Azure Stack の間で手動切り替えを構成する

Web サイトが構成済みのしきい値に達した場合、アラートが届きます。 トラフィックを手動で Azure にリダイレクトするには、次の手順を使用します。

1. Azure portal で、該当する Traffic Manager プロファイルを選択します。

    ![Traffic Manager エンドポイント](media/azure-stack-solution-hybrid-cloud/image20.png)

2. **[エンドポイント]** を選択します。
3. **[Azure エンドポイント]** を選択します。
4. **[状態]** で **[有効]** を選択し、**[保存]** を選択します。

    ![Azure エンドポイントを有効化](media/azure-stack-solution-hybrid-cloud/image23.png)

5. Traffic Manager プロファイルの **[エンドポイント]** で、**[外部エンドポイント]** を選択します。
6. **[状態]** で **[無効]** を選択し、**[保存]** を選択します。

    ![Azure Stack エンドポイントを無効化](media/azure-stack-solution-hybrid-cloud/image24.png)

エンドポイントの構成後、アプリケーション トラフィックは、Azure Stack Web アプリではなく、Azure スケールアウト Web アプリに送信されます。

 ![変更されたエンドポイント](media/azure-stack-solution-hybrid-cloud/image25.png)

フローを再び Azure Stack に戻すには、前の手順を使用して次の設定を行います。

- Azure Stack エンドポイントを有効にする
- Azure エンドポイントを無効にする

### <a name="configure-automatic-switching-between-azure-and-azure-stack"></a>Azure と Azure Stack の間で自動切り替えを構成する

Azure Functions によって実現される[サーバーレス](https://azure.microsoft.com/overview/serverless-computing/)環境で対象のアプリケーションが実行されている場合は、Application Insights の監視を使用することもできます。

このシナリオでは、関数アプリを呼び出す Webhook を使用するように Application Insights を構成することができます。 このアプリでは、アラートに応じてエンドポイントの有効と無効を自動的に切り替えます。

次の手順を参考にして、自動トラフィック切り替えを構成してください。

1. Azure 関数アプリを作成します。
2. HTTP によってトリガーされる関数を作成します。
3. Resource Manager、Web Apps、Traffic Manager 用の Azure SDK をインポートします。
4. 次の処理を行うコードを作成します。

   - Azure サブスクリプションに対して認証を行う。
   - Traffic Manager のエンドポイントを切り替えるパラメーターを使用して、Azure または Azure Stack にトラフィックを送信する。

5. 作成したコードを保存し、Application Insights のアラート ルール設定の **Webhook** セクションに、適切なパラメーターと共に関数アプリの URL を追加します。
6. Application Insights のアラートが発生すると、トラフィックが自動的にリダイレクトされます。

## <a name="next-steps"></a>次の手順

- Azure のクラウド パターンの詳細については、「[Cloud Design Pattern (クラウド設計パターン)](https://docs.microsoft.com/azure/architecture/patterns)」を参照してください。
