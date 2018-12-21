---
title: Azure でクラウド間スケーリング ソリューションを作成する | Microsoft Docs
description: Azure でクラウド間スケーリング ソリューションを作成する方法について説明します。
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
ms.date: 09/24/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 57624133b249a8ec2ece90eac4a64729e4d15151
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968205"
---
# <a name="tutorial-create-cross-cloud-scaling-solutions-with-azure"></a>チュートリアル: Azure でクラウドの間スケーリング ソリューションを作成する

*適用対象: Azure Stack 統合システムと Azure Stack Development Kit*

Azure Stack でホストされる Web アプリから、Traffic Manager を介した自動スケーリングを備える Azure でホストされる Web アプリに切り替えて、負荷時に柔軟でスケーラブルなクラウド ユーティリティを実現する手動トリガー プロセスを提供するクラウド間ソリューションを作成する方法について説明します。

このパターンでは、パブリック クラウドでアプリケーションを実行するようにテナントが準備されていない可能性があります。 ただし、アプリの需要の急増に対処するためにオンプレミス環境で必要になる容量を維持することは、企業にとって経済的に見合わない場合があります。 テナントは、パブリック クラウドの弾力性をオンプレミス ソリューションとともに活用できます。

このチュートリアルでは、以下を実現するためのサンプル環境を構築します。

> [!div class="checklist"]
> - マルチノードの Web アプリケーションを作成します。
> - 継続的配置 (CD) プロセスを構成および管理します。
> - Web アプリを Azure Stack に発行します。
> - リリースを作成します。
> - デプロイを監視し追跡するについて説明します。

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack は Azure の拡張機能です。 Azure Stack は、オンプレミスの環境にクラウド コンピューティングの俊敏性とイノベーションを提供します。これにより、どこでもハイブリッド アプリをビルドしてデプロイできる唯一のハイブリッド クラウドが実現されます。  
> 
> [ハイブリッド アプリケーションのための設計の考慮事項](https://aka.ms/hybrid-cloud-applications-pillars)に関するホワイト ペーパーでは、ハイブリッド アプリケーションを設計、デプロイ、および運用するためのソフトウェア品質の重要な要素 (配置、スケーラビリティ、可用性、回復性、管理容易性、およびセキュリティ) についてレビューしています。 これらの設計の考慮事項は、ハイブリッド アプリケーションの設計を最適化したり、運用環境での課題を最小限に抑えたりするのに役立ちます。

## <a name="prerequisites"></a>前提条件

-   Azure のサブスクリプション。 必要に応じて、開始前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。

- Azure Stack 統合システムまたは Azure Stack Development Kit のデプロイ。
    - Azure Stack のインストール手順は、[Azure Stack Development Kit のインストール](../asdk/asdk-install.md)に関するページで見つけることができます。
    - [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) このインストールが完了するまで数時間かかることがあります。

-   [App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS サービスを Azure Stack にデプロイします。

-   Azure Stack 環境内で[プラン/オファーを作成](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview)します。

-   Azure Stack 環境内で[テナント サブスクリプションを作成](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)します。

-   テナント サブスクリプション内で Web アプリを作成します。 後で使用できるように新しい Web アプリの URL を書き留めておきます。

-   テナント サブスクリプション内で、Azure Pipelines 仮想マシンをデプロイします。

-   .NET 3.5 がインストールされた Windows Server 2016 VM が必要です。 この VM は、プライベート ビルド エージェントとして Azure Stack 上のテナント サブスクリプションに構築されます。

-   [Windows Server 2016 with SQL 2017 VM Image](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image#add-a-vm-image-through-the-portal) は、Azure Stack Marketplace で入手できます。 このイメージを入手できない場合は、Azure Stack のオペレーターと協力して、環境に追加されていることを確認してください。

## <a name="issues-and-considerations"></a>問題と注意事項

### <a name="scalability-considerations"></a>スケーラビリティに関する考慮事項

クラウド間スケーリングの主要コンポーネントは、パブリック クラウドとオンプレミス クラウド インフラストラクチャ間の迅速なオンデマンド スケーリングを実現する機能であり、需要により必要になる一貫性があり信頼性の高いサービスを提供します。

### <a name="availability-considerations"></a>可用性に関する考慮事項

オンプレミス ハードウェア構成およびソフトウェア デプロイを通じて高可用性をもたらすように、ローカルでデプロイされたアプリが構成されていることを確認します。

### <a name="manageability-considerations"></a>管理容易性に関する考慮事項

クラウド間ソリューションは、複数の環境にわたるシームレスな管理と使い慣れたインターフェイスを実現します。 クロスプラットフォームの管理には、PowerShell を使用することをお勧めします。

## <a name="cross-cloud-scaling"></a>クラウド間スケーリング

### <a name="obtain-a-custom-domain-and-configure-dns"></a>カスタム ドメインを取得し DNS を構成する

ドメインの DNS ゾーン ファイルを更新します。 Azure AD は、カスタム ドメイン名の所有権を確認します。 Azure 内の Azure/Office 365/外部 DNS レコードに [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) を使用するか、または[別の DNS レジストラー](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)で DNS エントリを追加します。

1.  パブリック レジストラーでカスタム ドメインを登録します。

2.  ドメインのドメイン名レジストラーにサインインします。 DNS の更新を行うには、承認された管理者が必要になることがあります。 

3.  Azure AD から提供された DNS エントリを追加して、ドメインの DNS ゾーン ファイルを更新します。 (DNS エントリは電子メール ルーティングや Web ホスティングの動作には影響しません)。 

### <a name="create-a-default-multi-node-web-app-in-azure-stack"></a>Azure Stack で既定の複数ノード Web アプリを作成する

ハイブリッドの継続的インテグレーションおよび継続的配置 (CI/CD) を設定して、Web アプリを Azure および Azure Stack にデプロイし、両方のクラウドに変更を自動プッシュします。

> [!Note]  
> (Windows Server と SQL の) 実行および App Service のデプロイには、適切なイメージがシンジケート化された Azure Stack が必要です。 App Service ドキュメントの「[Azure Stack 上の App Service を開始する前に](../azure-stack-app-service-before-you-get-started.md)」の Azure Stack オペレーター用セクションを参照してください。

### <a name="add-code-to-azure-repos"></a>Azure Repos にコードを追加する

Azure Repos

1. Azure Repos でのプロジェクト作成権限が付与されているアカウントを使用して、Azure Repos にサインインします。

    ハイブリッド CI/CD は、アプリケーション コードとインフラストラクチャ コードの両方に適用できます。 プライベート クラウド開発とホステッド クラウド開発の両方に、[Azure Resource Manager テンプレート](https://azure.microsoft.com/resources/templates/)を使用します。

    ![Alt text](media/azure-stack-solution-cloud-burst/image1.JPG)

2. 既定の Web アプリを作成して開くことで、**リポジトリを複製**します。

    ![Alt text](media/azure-stack-solution-cloud-burst/image2.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>両方のクラウドで App Services の自己完結型 Web アプリ デプロイを作成する

1.  **WebApplication.csproj** ファイルを編集します。 **Runtimeidentifier** を選択し、**win10 x64** を追加します。 (「[自己完結型デプロイ](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)」に関するドキュメントを参照してください)。 

    ![Alt text](media/azure-stack-solution-cloud-burst/image3.png)

2.  チーム エクスプローラーを使用して、コードを Azure Repos にチェックインします。

3.  アプリケーション コードが Azure Repos にチェックインされたことを確認します。

## <a name="create-the-build-definition"></a>ビルド定義を作成する

1. Azure Pipelines にログインして、ビルド定義を作成する機能を確認します。

2. **-r win10-x64** コードを追加します。 これは、.Net Core を使用して自己完結型のデプロイをトリガーするために必要です。

    ![Alt text](media/azure-stack-solution-cloud-burst/image4.png)

3. ビルドを実行します。 [自己完結型のデプロイ ビルド](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)のプロセスにより、Azure および Azure Stack 上で実行できる成果物が発行されます。

## <a name="use-an-azure-hosted-agent"></a>Azure でホストされるエージェントを使用する

Web アプリをビルドおよびデプロイする場合、Azure Pipelines でホステッド エージェントを使用すると便利です。 Microsoft Azure によってメンテナンスやアップグレードが自動的に実施され、開発、テスト、デプロイについても、継続的に、かつ中断されることなく実行できます。

### <a name="manage-and-configure-the-cd-process"></a>CD プロセスを管理および構成する

Azure Pipelines および Azure DevOps Server が提供するパイプラインは自由に構成でき、管理性にも優れ、開発、ステージング、QA、運用など、さまざまな環境へのリリースに使用できます。また、特定のステージで承認を要求することもできます。

## <a name="create-release-definition"></a>リリース定義の作成

![Alt text](media/azure-stack-solution-cloud-burst/image5.png)

1.  VSO の [ビルドとリリース] ページの **[リリース] タブ**で **[+]** ボタンを選択して、新しいリリースを追加します。

    ![Alt text](media/azure-stack-solution-cloud-burst/image6.png)

2. Azure App Service Deployment テンプレートを適用します。

    ![Alt text](media/azure-stack-solution-cloud-burst/image7.png)

3. [成果物の追加] で、Azure Cloud ビルド アプリに対して成果物を追加します。

    ![Alt text](media/azure-stack-solution-cloud-burst/image8.png)

4. [パイプライン] タブで、環境の**フェーズ、タスク** リンクを選択し、Azure のクラウド環境の値を設定します。

    ![Alt text](media/azure-stack-solution-cloud-burst/image9.png)

5. **環境名**を設定し、Azure クラウド エンドポイントに対して Azure **サブスクリプション**を選択します。

    ![Alt text](media/azure-stack-solution-cloud-burst/image10.png)

6. [環境名] で、必須の **Azure アプリ サービス名**を設定します。

    ![Alt text](media/azure-stack-solution-cloud-burst/image11.png)

7. Azure クラウドでホストされる環境のエージェント キューで、「**Hosted VS2017**」と入力します。

    ![Alt text](media/azure-stack-solution-cloud-burst/image12.png)

8. [Azure App Service 配置] メニューで、環境に対して有効な**パッケージまたはフォルダー**を選択します。 **[OK]** を選択して、**フォルダーの場所**を選択します。

    ![Alt text](media/azure-stack-solution-cloud-burst/image13.png)

    ![Alt text](media/azure-stack-solution-cloud-burst/image14.png)

9. すべての変更を保存し、**リリース パイプライン**に戻ります。

    ![Alt text](media/azure-stack-solution-cloud-burst/image15.png)

10. Azure Stack アプリのビルドを選択して、新しい成果物を追加します。

    ![Alt text](media/azure-stack-solution-cloud-burst/image16.png)

11. Azure App Service の配置を適用して、環境をもう 1 つ追加します。

    ![Alt text](media/azure-stack-solution-cloud-burst/image17.png)

12. 新しい環境に Azure Stack という名前を付けます。

    ![Alt text](media/azure-stack-solution-cloud-burst/image18.png)

13. **[タスク]** タブで Azure Stack 環境を見つけます。

    ![Alt text](media/azure-stack-solution-cloud-burst/image19.png)

14. Azure Stack エンドポイントのサブスクリプションを選択します。

    ![Alt text](media/azure-stack-solution-cloud-burst/image20.png)

15. [App Service の名前] として、Azure Stack Web アプリの名前を設定します。

    ![Alt text](media/azure-stack-solution-cloud-burst/image21.png)

16. Azure Stack エージェントを選択します。

    ![Alt text](media/azure-stack-solution-cloud-burst/image22.png)

17. [Azure App Service 配置] セクションで、環境に対して有効な**パッケージまたはフォルダー**を選択します。 **[OK]** を選択して、フォルダーの場所を選択します。

    ![Alt text](media/azure-stack-solution-cloud-burst/image23.png)

    ![Alt text](media/azure-stack-solution-cloud-burst/image24.png)

18. [変数] タブで、`VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS` という名前の変数を追加し、その値を **true** に設定し、スコープを Azure Stack に設定します。

    ![Alt text](media/azure-stack-solution-cloud-burst/image25.png)

19. 両方の成果物で**継続的**配置トリガー アイコンを選択し、**継続的**配置トリガーを有効にします。

    ![Alt text](media/azure-stack-solution-cloud-burst/image26.png)

20. Azure Stack 環境で**配置前**条件アイコンを選択し、トリガーを**リリース後**に設定します。

21. すべての変更を保存します。

> [!Note]  
> タスクの一部の設定は、テンプレートからリリース定義を作成したときに、[環境変数](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables)として自動的に定義されている可能性があります。 こうした設定は、タスクの設定では変更できません。これらの設定を編集するには、親環境項目を選択する必要があります

## <a name="publish-to-azure-stack-via-visual-studio"></a>Visual Studio 経由で Azure Stack に発行する

Visual Studio Online (VSTO) のビルドでは、エンドポイントを作成することにより、Azure サービス アプリを Azure Stack にデプロイすることができます。 Azure Pipelines がビルド エージェントに接続し、エージェントが Azure Stack に接続します。

1.  VSTO にサインインし、アプリの設定ページに移動します。

2.  **[設定]** の **[セキュリティ]** を選択します。

3.  **[VSTS グループ]** の **[エンドポイント作成者]** を選択します。

4.  **[メンバー]** タブで **[追加]** を選択します。

5.  **[ユーザーとグループの追加]** にユーザー名を入力し、そのユーザーをユーザー一覧から選択します。

6.  **[変更の保存]** を選択します。

7.  **[VSTS グループ]** の一覧で、**[エンドポイント管理者]** を選択します。

8.  **[メンバー]** タブで **[追加]** を選択します。

9.  **[ユーザーとグループの追加]** にユーザー名を入力し、そのユーザーをユーザー一覧から選択します。

10. **[変更の保存]** を選択します。

これでエンドポイント情報が存在するので、Azure Pipelines から Azure Stack への接続を使用する準備ができました。 Azure Stack のビルド エージェントは、Azure Pipelines から命令を受け取った後、Azure Stack との通信のためのエンドポイント情報を伝達します。

## <a name="develop-the-application-build"></a>アプリケーション ビルドを開発する

> [!Note]  
> (Windows Server と SQL の) 実行および App Service のデプロイには、適切なイメージがシンジケート化された Azure Stack が必要です。 App Service ドキュメントの「[Azure Stack 上の App Service を開始する前に](../azure-stack-app-service-before-you-get-started.md)」の Azure Stack オペレーター用セクションを参照してください。

Azure Repos から [Azure Resource Manager テンプレート](https://azure.microsoft.com/resources/templates/)を Web アプリ コードのように使用して両方のクラウドに対してデプロイします。

### <a name="add-code-to-a-azure-repos-project"></a>Azure Repos プロジェクトにコードを追加する

1.  Azure Stack でのプロジェクト作成権限が付与されているアカウントを使用して、Azure Repos にサインインします。 次のキャプチャ画面は、HybridCICD プロジェクトへの接続方法を示しています。

2.  既定の Web アプリを作成して開くことで、**リポジトリを複製**します。

#### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>両方のクラウドで App Services の自己完結型 Web アプリ デプロイを作成する

1.  **WebApplication.csproj** ファイルを編集します。**Runtimeidentifier** を選択し、win10 x64 を追加します。 詳細については、「[自己完結型デプロイ](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)」に関するドキュメントを参照してください。

2.  チーム エクスプローラーを使用して、コードを Azure Repos にチェックインします。

3.  アプリケーション コードが Azure Repos にチェックインされたことを確認します。

### <a name="create-the-build-definition"></a>ビルド定義を作成する

1.  ビルド定義を作成できるアカウントで Azure Pipelines にサインインします。

2.  プロジェクトの **[Build Web Application]\(Web アプリケーションのビルド\)** ページに移動します。

3.  **[引数]** に **-r win10-x64** コードを追加します。 これは、.Net Core を使用して自己完結型のデプロイをトリガーするために必要です。

4.  ビルドを実行します。 [自己完結型のデプロイ ビルド](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)のプロセスにより、Azure および Azure Stack 上で実行できる成果物が発行されます。

#### <a name="use-an-azure-hosted-build-agent"></a>Azure ホスト ビルド エージェントを使用する

Web アプリをビルドしてデプロイする場合、Azure Pipelines でホスト ビルド エージェントを使用すると便利です。 エージェントのメンテナンスやアップグレードは Microsoft Azure によって自動的に実施されるので、中断のない継続的な開発サイクルが実現します。

### <a name="configure-the-continuous-deployment-cd-process"></a>継続的配置 (CD) プロセスを構成する

Azure Pipelines および Azure DevOps Server が提供するパイプラインは自由に構成でき、管理性にも優れ、開発、ステージング、品質保証 (QA)、運用など、さまざまな環境へのリリースに使用できます。 このプロセスの一環として、アプリケーション ライフ サイクルの特定のステージで承認を要求することもできます。

#### <a name="create-release-definition"></a>リリース定義の作成

アプリケーション ビルド プロセスの最後の手順は、リリース定義の作成です。 このリリース定義を使用してリリースを作成し、ビルドをデプロイします。

1.  Azure Pipelines にサインインして、プロジェクトの **[ビルドとリリース]** に移動します。

2.  **[リリース]** タブで **[+]** を選択し、**[リリース定義の作成]** を選択します。

3.  **[テンプレートの選択]** で **[Azure App Service の配置]** を選択し、**[適用]** を選択します。

4.  **[成果物の追加]** の **[ソース (ビルド定義)]** から Azure Cloud ビルド アプリを選択します。

5.  **[パイプライン]** タブで、**1 フェーズ**、**1 タスク**のリンクを選択し、**環境のタスクを表示**します。

6.  **[タスク]** タブで **[環境名]** に「Azure」と入力し、**[Azure サブスクリプション]** リストから [AzureCloud Traders-Web EP] を選択します。

7.  **Azure App Service の名前**として「`northwindtraders`」と入力します。次のキャプチャ画面を参照してください。

8.  [エージェント フェーズ] で、**[エージェント キュー]** リストから **[Hosted VS2017]** を選択します。

9.  **[Azure App Service 配置]** で、環境に対して有効な**パッケージまたはフォルダー**を選択します。

10. **[ファイルまたはフォルダーの選択]** で、**保存先**に対して **[OK]** を選択します。

11. すべての変更を保存し、**[パイプライン]** に戻ります。

12. **[パイプライン]** タブで **[成果物の追加]** を選択し、**[ソース (ビルド定義)]** リストから **[NorthwindCloud Traders-Vessel]** を選択します。

13. **[テンプレートの選択]** で、もう 1 つ環境を追加します。 **[Azure App Service の配置]** を選択し、**[適用]** を選択します。

14. **[環境名]** として「`Azure Stack`」と入力します。

15. **[タスク]** タブで、[Azure Stack] を見つけて選択します。

16. **[Azure サブスクリプション]** リストから、Azure Stack のエンドポイントに使用する **[AzureStack Traders-Vessel EP]** を選択します。

17. **[App Service の名前]** として、Azure Stack Web アプリの名前を入力します。

18. **[エージェントの選択]** で、**[エージェント キュー]** リストから **[AzureStack -bDouglas Fir]** を選択します。

19. **[Azure App Service 配置]** で、環境に対して有効な**パッケージまたはフォルダー**を選択します。 **[ファイルまたはフォルダーの選択]** で、フォルダーの**保存先**に対して **[OK]** を選択します。

20. **[変数]** タブで、`VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS` という名前の変数を見つけます。 変数の値を **[true]** に設定し、そのスコープを **[Azure Stack]** に設定します。

21. **[パイプライン]** タブで、NorthwindCloud Traders-Web の成果物に使用する **[継続的配置トリガー]** アイコンを選択し、**[継続的配置トリガー]** を **[有効]** に設定します。 **NorthwindCloud Traders-Vessel** の成果物についても同じ操作を行います。

22. Azure Stack 環境で**配置前**条件アイコンを選択し、トリガーを**リリース後**に設定します。

23. すべての変更を保存します。

> [!Note]  
> リリース タスクの一部の設定は、テンプレートからリリース定義を作成したときに、[環境変数](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables)として自動的に定義されます。 これらの設定は、タスク設定では変更できませんが、親環境項目で変更できます。

## <a name="create-a-release"></a>リリースを作成する

1.  **[パイプライン]** タブの **[リリース]** リストを開いて、**[リリースの作成]** を選択します。

2.  リリースの説明を入力し、正しい成果物が選択されていることを確認して、**[作成]** を選択します。 しばらくすると、新しいリリースが作成されたことを示すバナーが表示され、そのリリース名がリンクとして表示されます。 リンクを選択すると、リリース概要ページが表示されます。

3.  リリースの概要ページに、リリースに関する詳細が表示されます。 次の "Release-2" のキャプチャ画面で、**[環境]** セクションを見ると、Azure については**デプロイ状態**が "進行中" として表示され、Azure Stack については "成功" と表示されていることがわかります。 Azure 環境のデプロイ状態が "成功" に変わると、リリースの承認準備が完了したことを示すバナーが表示されます。 デプロイが保留中か、失敗した場合は、青い **(i)** 情報アイコンが表示されます。 このアイコンにマウス ポインターを合わせると、遅延または失敗の理由を示すポップアップが表示されます。

4.  リリースの一覧など、その他のビューにも、承認待ちであることを示すアイコンが表示されます。 このアイコンのポップアップには、環境の名前のほか、デプロイに関連するさらに詳しい情報が表示されます。 管理者は、リリースの全体的な進行状況を見て、どのリリースが承認待ちになっているかを簡単に確認できます。

## <a name="monitor-and-track-deployments"></a>デプロイを監視および追跡する

1.  **Release-2** の概要ページで、**[ログ]** を選択します。 デプロイ時には、エージェントからリアルタイムのログがこのページに表示されます。 左側のウィンドウには、デプロイに伴う各操作の状態が環境ごとに表示されます。

2.  デプロイ前またはデプロイ後の承認の **[アクション]** 列で人アイコンを選択して、デプロイを承認 (または拒否) したユーザーと、そのユーザーからのメッセージを表示します。

3.  デプロイ完了後、ログ ファイル全体が右側のウィンドウに表示されます。 左側のウィンドウでいずれかの**ステップ**を選択して、**ジョブの初期化** など、単一ステップのログ ファイルを表示します。 ログを個別に表示できることから、デプロイ全体を構成する各要素の追跡とデバッグがしやすくなっています。 特定のステップのログ ファイルを**保存**するか、**すべてのログを zip としてダウンロード**します。

4.  **[概要]** タブを開くと、そのリリースの概要情報が表示されます。 ビルドとそのデプロイ先となった環境の詳細、デプロイ状態など、リリースに関する各種の情報がこのビューに表示されます。

5.  環境リンク (**Azure** または **Azure Stack**) を選択すると、特定の環境に対する既存のデプロイと保留中のデプロイについての情報が表示されます。 これらのビューを使って簡単に、同じビルドが両方の環境にデプロイされていることを確認します。

6.  **デプロイされた運用アプリ**をブラウザーで開きます。 たとえば、Azure アプリケーション サービス Web サイトの URL http://[your-app-name\].azurewebsites.net](http:// [your-app-name].azurewebsites.net) を開きます。

**Azure と Azure Stack を統合することにより、スケーラブルなクラウド間ソリューションが実現する**

柔軟で堅牢なマルチ クラウド サービスは、データ セキュリティ、バックアップおよび冗長性、一貫性のある迅速な可用性、スケーラブルなストレージおよび分散、地理的準拠ルーティングを提供します。 この手動でトリガーされるプロセスにより、ホストされる Web アプリ間での、信頼性が高く、効率的な負荷の切り替えが実現し、重要なデータがすぐに使用できるようになります。 

## <a name="next-steps"></a>次の手順
- Azure のクラウド パターンの詳細については、「[Cloud Design Pattern (クラウド設計パターン)](https://docs.microsoft.com/azure/architecture/patterns)」を参照してください。
