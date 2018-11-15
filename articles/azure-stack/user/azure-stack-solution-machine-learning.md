---
title: Azure および Azure Stack を使用してエッジ機械学習ソリューションを作成する | Microsoft Docs
description: Python を Azure および Azure Stack と共に使用してエッジ機械学習ソリューションを作成する方法について説明します。
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
ms.date: 09/26/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 8a5ca4f94a6f1186b6d1a26b1c7e12357cd9e799
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616369"
---
# <a name="tutorial-create-an-edge-machine-learning-solution-with-azure-and-azure-stack"></a>チュートリアル: Azure および Azure Stack を使用してエッジ機械学習ソリューションを作成する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure および Azure Stack を使用してエッジ機械学習ソリューションを作成する方法について説明します。

このチュートリアルでは、以下を実現するためのサンプル環境を構築します。

> [!div class="checklist"]
> - クリーン データを格納するためのストレージ アカウントとコンテナーを作成します。
> - Azure Portal で Ubuntu データ サイエンス仮想マシン (DSVM) を作成します。
> - Azure で Azure Machine Learning サービスをデプロイして、モデルを構築およびトレーニングします。
> - Azure Machine Learning サービス アカウントを作成します。
> - Azure Container Registry をデプロイして使用します。
> - Kubernetes クラスターを Azure Stack にデプロイします。
> - データのための Azure Stack ストレージ アカウントとストレージ キューを作成します。
> - クリーン データを Azure Stack から Azure に移動する新しい Azure Stack 関数を作成します。

**このソリューションを使用する状況**

 -  組織が DevOps アプローチを使用しているか、または近い将来にその使用を予定している場合。
 -  Azure Stack 実装およびパブリック クラウド全体にわたって CI/CD プラクティスを実装したいと考えている場合。
 -  クラウドおよびオンプレミス環境全体にわたって CI/CD パイプラインを統合したいと考えている場合。
 -  クラウドまたはオンプレミス サービスを使用してアプリケーションを開発する機能が必要な場合。
 -  クラウドおよびオンプレミス アプリケーション全体にわたって一貫した開発者スキルを活用したいと考えている場合。

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack は Azure の拡張機能です。 Azure Stack は、オンプレミスの環境にクラウド コンピューティングの俊敏性とイノベーションを提供します。これにより、どこでもハイブリッド アプリをビルドしてデプロイできる唯一のハイブリッド クラウドが実現されます。  
> 
> [ハイブリッド アプリケーションのための設計の考慮事項](https://aka.ms/hybrid-cloud-applications-pillars)に関するホワイト ペーパーでは、ハイブリッド アプリケーションを設計、デプロイ、および運用するためのソフトウェア品質の重要な要素 (配置、スケーラビリティ、可用性、回復性、管理容易性、およびセキュリティ) についてレビューしています。 これらの設計の考慮事項は、ハイブリッド アプリケーションの設計を最適化したり、運用環境での課題を最小限に抑えたりするのに役立ちます。

## <a name="prerequisites"></a>前提条件

このユースケースを構築するにはいくつかのコンポーネントが必要であり、その準備には少し時間がかかることがあります。

 -  Azure OEM/ハードウェア パートナーは運用 Azure Stack をデプロイでき、すべてのユーザーが ASDK をデプロイできます。

 -  また、Azure Stack オペレーターが App Service をデプロイし、プランとオファーを作成し、テナント サブスクリプションを作成し、さらに Windows Server 2016 イメージを追加することも必要です。

 -  ハイブリッド ネットワークと App Service のセットアップが必要です (詳細については、[VNet とのアプリ統合](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)に関するページを参照)。

 -  デプロイの前に、VSTS 統合のためのプライベート [ビルドとリリース エージェント](https://docs.microsoft.com/vsts/pipelines/agents/agents?view=vsts)を設定する必要があります (開始する前に、使用される既存のすべてのコンポーネントが要件を満たしていることを確認してください)。

Azure と Azure Stack の事前の知識が必要です。 先に進む前に詳細を調べるには、次のトピックから始めてください。

 -  [Azure 入門](https://azure.microsoft.com/overview/what-is-azure/)

 -  [Azure Stack の主要概念](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

 -  [Azure Stack ハイブリッド CI/CD ソリューション ガイド](/azure/azure-stack/user/azure-stack-solution-pipeline)

**Azure**

 -  Azure サブスクリプション ([無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します)

 -  Azure で [Web アプリ](https://docs.microsoft.com/azure/app-service/app-service-web-overview)によって作成された新しい Web アプリ URL

 -  [Azure 上への Azure Container Services (ACS) Kubernetes](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy) のデプロイ

 -  Azure Machine Learning サービス (プレビュー) [4 部構成のチュートリアル](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/tutorial-classifying-iris-part-1)のデプロイ

 -  Azure Machine Learning 実験[アカウント](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/experimentation-service-template)

**Azure Stack**

 -  Azure Stack 統合システムまたは Azure Stack Development Kit のデプロイ。

    - Azure Stack のインストール手順は、[Azure Stack Development Kit のインストール](/articles/azure-stack/asdk/asdk-install)に関するページで見つけることができます。
     - [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) このインストールが完了するまで数時間かかることがあります。

 -  Azure Stack への [App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS サービスのデプロイ

 -  Azure Stack 環境内の[プラン/オファー](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview)

 -  Azure Stack 環境内の[テナント サブスクリプション](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)

 -  Ubuntu Server VM イメージ ([Azure Stack Marketplace](https://buildazure.com/2016/05/04/azure-marketplace-ubuntu-server-16-04-lts/)) で使用可能 この VM は、Kubernetes VM だけでなく、プライベート ビルド エージェントとして Azure Stack 上のテナント サブスクリプションに組み込まれます。 このイメージが使用できない場合は、Azure Stack オペレーターが、これが確実に環境に追加されるように支援できます。 (Ubuntu の 18.04 ビルドは、現在サポートされていないため使用しないでください。)

 -  テナント サブスクリプション内の Web アプリ (新しい Web アプリ URL を後の使用のためにメモしてください。)

 -  テナント サブスクリプション内での、VSTS Linux ベースのプライベート ビルド エージェント仮想マシンのデプロイ

 -  [Azure Stack 上への Azure Container Services (ACS) Kubernetes](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy) のデプロイ

**開発者ツール**

 -  [VSTS ワークスペース](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services) (サインアップ プロセスによって "MyFirstProject" という名前のプロジェクトが作成されます)

 -  [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) のインストールと [VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services) サインオン

 -  プロジェクトの[ローカル複製](https://www.visualstudio.com/docs/git/gitquickstart)

 -  [Linux Subsystem for Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) のインストール (BASH および SSH の場合)

 -  [Docker for Windows](https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe) のインストール

 -  [Azure Machine Learning Workbench (プレビュー)](https://aka.ms/azureml-wb-msi) のインストール

 -  [Python](https://www.python.org/ftp/python/3.7.0/python-3.7.0rc1-amd64.exe) 環境のインストール

**VSTS**

 -  **VSTS アカウント。** ビルド、テスト、およびデプロイのための継続的な統合を迅速に設定します。 VSTS アカウントの詳細については、[VSTS アカウントの作成](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student?view=vsts)に関するページを参照してください。

 -  **コード リポジトリ。** VSTS プラットフォームでは、GitHub、BitBucket、DropBox、One Drive、および TFS 内の既存のコード リポジトリを使用して、複数のコード リポジトリを活用して開発パイプラインを効率化できます。 コード リポジトリの詳細については、[Git および VSTS の概要](https://docs.microsoft.com/vsts/git/gitquickstart?view=vsts&tabs=visual-studio)に関するチュートリアルを参照してください。

 -  **サービス接続。** テスト、ビルド、またはデプロイのためのタスクを実行するには、外部のリモート サービスに接続します。 VSTS サービス接続の詳細については、[ビルドとリリースのためのサービス エンドポイント](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints?view=vsts)に関するページを参照してください。

 -  **ビルド定義。** 自動化されたビルド プロセスを定義し、タスク カタログから一連のタスクを構成します。 ビルド定義の詳細については、[ビルド定義の作成](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1?view=vsts)に関するドキュメントを参照してください。

 -  **リリース定義。** アプリケーション アーティファクトがデプロイされる環境のコレクションのためのデプロイ プロセスを定義します。 リリース定義の詳細については、[リリース定義の作成](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1?view=vsts)に関するドキュメントを参照してください。

 -  **ホストされた VSTS Linux ビルド エージェント プール。** Microsoft によって管理および保守されるホストされたエージェントを使用して、アプリケーションを迅速にビルド、テスト、およびデプロイします。 ホストされた VSTS ビルド エージェントの詳細については、[ホストされたエージェント](https://docs.microsoft.com/vsts/build-release/concepts/agents/hosted?view=vsts)に関するドキュメントを参照してください。

## <a name="step-1-create-a-storage-account"></a>ステップ 1: ストレージ アカウントを作成する

クリーン データを格納するためのストレージ アカウントとコンテナーを作成します。

1.  [*Azure Portal*](https://portal.azure.com/) にサインインします。

2.  Azure Portal で、左側にあるメニューを展開してサービスのメニューを開き、**[すべてのサービス]** を選択します。 **[ストレージ]** まで下へスクロールし、**[ストレージ アカウント]** を選択します。 [**ストレージ アカウント**] ウィンドウで、**[追加]** を選択します。

3.  ストレージ アカウントの名前を入力します。

    > [!Note]  
    > ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。 ストレージ アカウント名は、Azure 内で一意である必要があります。 Azure Portal には、選択されたストレージ アカウント名が既に使用されているかどうかが示されます。

4.  使用されるデプロイメント モデル (**[Resource Manager]**) を指定します。

5.  ストレージ アカウントの種類 (**[汎用 V1]**) を選択し、パフォーマンス レベル (**[標準]**) を指定します。

6.  ストレージ アカウントのレプリケーション オプション (**[GRS]**) を選択します。

7.  新しいストレージ アカウントのサブスクリプションを選択します。

8.  新しいリソース グループを指定するか、既定のリソース グループを選択します。

9.  ストレージ アカウントの地理的な場所を選択します。

10. **[作成]** をクリックしてストレージ アカウントを作成します。

    ![Alt text](\media\azure-stack-solution-machine-learning\image1.png)

11.  最近作成されたストレージ アカウントを選択します。

12.  **[BLOB]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image2.png)

13.  **[+ コンテナー]** を選択し、**[コンテナー]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image3.png)

14.  コンテナーに **uploadeddata** という名前を付け、アクセスの種類として **[コンテナー]** を選択します。

15.  **[作成]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image4.png)

## <a name="step-2-create-a-data-science-virtual-machine"></a>手順 2: データ サイエンス仮想マシンを作成する

Azure Portal で Ubuntu データ サイエンス仮想マシン (DSVM) を作成します。

1.  [https://portal.azure.com](https://portal.azure.com/) から Azure Portal にログオンします。

2.  **[+ 新規]** リンクを選択し、「Linux Ubuntu CSP 用データ サイエンス仮想マシン」を検索します。

    ![Alt text](media\azure-stack-solution-machine-learning\image5.png)

1.  一覧で **[Linux 用データ サイエンス仮想マシン (Ubuntu)]** を選択し、画面の指示に従って DSVM を作成します。

    ![Alt text](media\azure-stack-solution-machine-learning\image6.png)

> ![重要]  
> *[認証の種類]* として [パスワード]** を**選択してください**。

新しい DSVM を、新しく作成されたストレージ アカウントと同じリソース グループに配置します。 すべての Edge ML オブジェクトがこのリソース グループ内の Azure にデプロイされます。

1.  [設定] > [構成] オプション機能で

    a.  以前に作成された **[ストレージ アカウント]** を選択します。

    b.  新しい **[Virtual Network] (仮想ネットワーク)**、**[サブネット]**、および **[パブリック IP]** を作成します。これにより、既定ではリソース グループ名に基づいた名前が作成されます。

    c.  新しい **[NSG]** を作成します。これにより、既に適用されている正しい規則でこれが自動的に作成されます。

    d.  **[Diagnostics Storage Account] (診断ストレージ アカウント)** として、以前に作成されたストレージ アカウントを選択します。

    e.  注: Azure サブスクリプションに対して AAD が有効で、構成されている場合は、Azure リソースのマネージド ID も有効にすることができます。

2.  **[OK]** を選択します。

### <a name="connect-to-the-dsvm"></a>DSVM に接続する

DSVM が作成されたら、Windows Subsystem for Linux から VM に接続します。

```Bash  
    ssh <user>@<DSVM Public IP>
```

1.  VM 接続を確認するよう求められたら、[YES] と入力します。

2.  DSVM のために作成されたパスワードを入力します。

### <a name="update-the-dsvm"></a>DSVM を更新する 

```Bash  
sudo su 
apt-get update 
apt-get -y upgrade 
apt-get -y dist-upgrade 
apt-get -y autoremove
```

## <a name="step-3-deploy-azure-machine-learning-services"></a>手順 3: Azure Machine Learning サービスをデプロイする

Azure で Azure Machine Learning サービスをデプロイします。

Azure Machine Learning サービス (プレビュー) は、統合されたエンドツーエンドのデータ サイエンスおよび高度な分析ソリューションです。 プロフェッショナルなデータ サイエンティストは、これを使用してデータの準備、実験の開発、モデルのデプロイをクラウド規模で行うことができます。

このセクションでは、次の内容について説明します。

> [!div class="checklist"]
> - Azure Machine Learning サービスのサービス アカウントを作成する
> - Azure Machine Learning Workbench をインストールしてログインする
> - Workbench でプロジェクトを作成する
> - そのプロジェクトでスクリプトを実行する
> - コマンド ライン インターフェイス (CLI) にアクセスする

Azure Machine Learning サービスは Microsoft Azure のポートフォリオの一部であるため、ご利用には Azure サブスクリプションが必要です。 Azure サブスクリプションを取得するには、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。

リソース グループ、仮想マシンなどの資産を作成するには、十分なアクセス許可が必要です。

Azure Machine Learning Workbench アプリケーションは、次のオペレーティング システムにインストールできます。

 -  Windows 10 または Windows Server 2016
 -  macOS Sierra または High Sierra

## <a name="step-4-create-azure-machine-learning-services"></a>手順 4: Azure Machine Learning サービスを作成する

Azure Machine Learning サービス アカウントを作成します。

Azure Portal を使用して、Azure Machine Learning アカウントをプロビジョニングします。

1.  使用される Azure サブスクリプションの資格情報を使用して [Azure Portal](https://portal.azure.com/) にサインインします。 Azure サブスクリプションを取得するには、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。

    ![Alt text](media\azure-stack-solution-machine-learning\image7.png)

1.  ポータルの左上隅にある **[リソースの作成]** ボタン (+) を選択します。

    ![Azure Portal でのリソースの作成](media\azure-stack-solution-machine-learning\image8.png)

1.  検索バーに「**Machine Learning**」と入力します。 **[Machine Learning Experimentation (preview)]\(Machine Learning 実験 (プレビュー)\)** という検索結果を選択します。

    ![Azure Machine Learning の検索](media\azure-stack-solution-machine-learning\image9.png)

1.  **[Machine Learning 実験]** ウィンドウで、一番下までスクロールし、**[作成]** を選択して実験アカウントの定義を開始します。

    ![Azure Machine Learning - 実験アカウントの作成](media\azure-stack-solution-machine-learning\image10.png)

1.  **[ML 実験]** ウィンドウで、Machine Learning 実験アカウントを構成します。

    | Setting | チュートリアルの推奨値 | 説明 |
    |---------------------------------------|----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | [Experimentation account name]\(実験アカウント名\) | 一意の名前 | アカウントを識別する一意の名前を入力します。 実験を最もよく識別する部門またはプロジェクト名を使用します。 この名前は 2 ～ 32 文字で指定します。 これには、英数字とダッシュ (-) 文字のみを使用してください。 |
    | サブスクリプション | サブスクリプション | 実験に使用する Azure サブスクリプションを選択します。 複数のサブスクリプションが存在する場合は、リソースが課金される適切なサブスクリプションを選択します。 |
    | リソース グループ | リソース グループ | サブスクリプションの既存のリソース グループを使用するか、または名前を入力して、この実験アカウントの新しいリソース グループを作成します。 |
    | Location | ユーザーに最も近いリージョン | ユーザーやデータ リソースに最も近い場所を選択します。 |
    | [Number of seats]\(シート数\) | 2 | 接続クライアント数を入力します。 [シートあたりの価格](https://azure.microsoft.com/pricing/details/machine-learning/)を参照してください。<br><br>このクイック スタートでは、2 つのシートだけが必要です。 シートは、必要に応じて Azure Portal から追加したり削除したりすることができます。 |
    | ストレージ アカウント | 一意の名前 | **[新規作成]** を選択し、[Azure Storage アカウント](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal)を作成するための名前を指定します。 名前は 3 文字以上 24 文字以下にする必要があり、英数字だけを使用することができます。 あるいは、**[既存のものを使用]** を選択し、一覧から既存のストレージ アカウントを選択します。 このストレージ アカウントは、プロジェクト アーティファクトと実行履歴データを保持するために必要であり、そのために使用されます。 |
    | [Workspace for Experimentation account]\(実験アカウントのワークスペース\) | IrisGarden<br>(チュートリアルで使用する名前) | このアカウントのワークスペースの名前を指定します。 この名前は 2 ～ 32 文字で指定します。 これには、英数字とダッシュ (-) 文字のみを使用してください。 このワークスペースには、実験を作成、管理、および発行するために必要なツールが含まれています。 |
    | [Assign owner for the workspace]\(ワークスペースの所有者の割り当て\) | アカウント | ワークスペース所有者として自分のアカウントを選択します。 |
    | [Create Model Management account]\(モデル管理アカウントを作成する\) | **check** | モデル管理アカウントを作成します。 これは、モデルをリアルタイムの Web サービスとしてデプロイおよび管理するために使用されます。 <br><br>オプションではありますが、モデル管理アカウントを実験アカウントと同時に作成することが推奨されます。 |
    | アカウント名 | 一意の名前 | モデル管理アカウントを識別する一意の名前を選択します。 実験を最もよく識別する部門またはプロジェクト名を使用します。 この名前は 2 ～ 32 文字で指定します。 これには、英数字とダッシュ (-) 文字のみを使用してください。 |
    | [Model Management pricing tier]\(モデル管理の価格レベル\) | **DEVTEST** | 新しいモデル管理アカウントの価格レベルを指定するには、**[価格レベルが選択されていません]** を選択します。 コスト削減のために、サブスクリプションで使用可能な場合は DEVTEST 価格レベルを選択します (使用には制限があります)。 利用できない場合は、S1 価格レベルを選択します。 [選択] を選択して、価格レベルの選択を保存します。 |
    | [ダッシュボードにピン留めする] | チェック | Azure Portal のフロント ダッシュボード ページで Machine Learning 実験アカウントを追跡しやすくするには、**[ダッシュボードにピン留めする]** オプションを選択します。 |

    ![Machine Learning 実験アカウントの構成](media\azure-stack-solution-machine-learning\image11.png)

1.  **[作成]** を選択して、実験アカウントとモデル管理アカウントの作成プロセスを開始します。

    ![Machine Learning 実験アカウントの構成](media\azure-stack-solution-machine-learning\image12.png)

    アカウントを作成するには、しばらく時間がかかることがあります。 Azure Portal ツールバーの [通知] アイコン (ベル) を選択することによって、デプロイ プロセスの状態を確認します。

    ![Azure Portal の通知](media\azure-stack-solution-machine-learning\image13.png)

### <a name="install-and-log-in-to-workbench"></a>Workbench をインストールしてログインする 

Azure Machine Learning Workbench には Windows 版と macOS 版があります。 [サポートされているプラットフォーム](https://docs.microsoft.com/azure/machine-learning/service/quickstart-installation)の一覧を参照してください。

**警告:** このインストールは、完了するまでに約 1 時間かかることがあります。

1.  最新の Workbench インストーラーをダウンロードして起動します。

    > [!Important]  
    > インストーラーは、ディスク上に完全にダウンロードした後、そこから実行します。 ブラウザーのダウンロード ウィジェットから直接実行しないでください。<br>**Windows の場合:<br>** a. [AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi) をダウンロードします。<br>b. エクスプローラーで、ダウンロードしたインストーラーをダブルクリックします。

1.  インストーラーの画面の指示に従って完了します。

    **このインストールは、完了するまでに 30 分かかることがあります。 **
    
    `Windows: C:\\Users\\<user>\\AppData\\Local\\AmlWorkbench`
    
    インストーラーは、必要なすべての依存関係 (Python、Miniconda、その他の関連ライブラリなど) をダウンロードして設定します。 このインストールには、Azure のクロスプラットフォーム コマンドライン ツール (Azure CLI) も含まれます。

1.  インストーラーの最後の画面で **[Launch Workbench]\(Workbench の起動\)** ボタンを選択し、Workbench を起動します。

    インストーラーが閉じられた場合は、**Machine Learning Workbench** のデスクトップ ショートカットを使用して起動します。

1.  Azure Machine Learning Workbench に対して認証するには、**[Microsoft アカウントでサインイン]** を選択します。 Azure Portal で実験およびモデル管理アカウントを作成するために使用されたものと同じ資格情報を使用します。

    サインインすると、Workbench は Azure サブスクリプションで見つかった最初の実験アカウントを使用し、そのアカウントに関連付けられたすべてのワークスペースとプロジェクトを表示します。

    > [!Tip]  
    > Workbench アプリケーション ウィンドウの左下隅にあるアイコンを使用して、別の実験アカウントに切り替えます。

### <a name="create-a-new-project-in-workbench"></a>Workbench で新しいプロジェクトを作成する

1.  Azure Machine Learning Workbench アプリを開き、必要に応じてサインインします。

    - Windows では、**Machine Learning Workbench** のデスクトップ ショートカットを使用して起動します。

    - macOS の場合は、Launchpad で **[Azure ML Workbench]** を選択します。

1.  **[プロジェクト]** ウィンドウの正符号 (+) を選択し、**[新しいプロジェクト]** を選択します。

    ![新しいワークスペース](media\azure-stack-solution-machine-learning\image14.png)

1.  フォームの各フィールドに必要事項を入力し、**[作成]** ボタンを選択すると、Workbench で新しいプロジェクトが作成されます。

    | フィールド | チュートリアルの推奨値 | 説明 |
    |-------------------------------------|------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | プロジェクト名 | myIris | アカウントを識別する一意の名前を入力します。 実験を最もよく識別する部門またはプロジェクト名を使用します。 この名前は 2 ～ 32 文字で指定します。 これには、英数字とダッシュ (-) 文字のみを使用してください。 |
    | プロジェクト ディレクトリ | c:\Temp\ | プロジェクトの作成先となるディレクトリを指定します。 |
    | プロジェクトの説明 | 空白のままにする | 省略可能なフィールド。プロジェクトの説明を入力することができます。 |
    | Visualstudio.com の GIT リポジトリの URL | 空白のままにする | 省略可能なフィールド。 ソース管理とコラボレーションのために、Visual Studio Team Services でプロジェクトを Git リポジトリに関連付けます。 [リポジトリを設定する方法について](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/using-git-ml-project)。 |
    | 選択したワークスペース | IrisGarden (存在する場合) | Azure Portal で、実験アカウントのために作成されたワークスペースを選択します。 <br>クイック スタートを使用すると、ワークスペースが IrisGarden の名前で一覧表示されます。 そうでない場合は、ワークスペースを実験アカウントの名前または推奨されるアカウント名と共に使用します。 |
    | プロジェクト テンプレート | あやめの分類 | テンプレートには、製品を調査するために使用されるスクリプトとデータが含まれています。 このテンプレートには、このクイック スタートやこのドキュメント サイト内のその他のチュートリアルに必要なスクリプトとデータが含まれています。 |

    ![新しいプロジェクト](media\azure-stack-solution-machine-learning\image15.png)

1.  新しいプロジェクトが作成されて、プロジェクト ダッシュボードにそのプロジェクトが表示されます。 プロジェクトのホーム ページ、データ ソース、ノートブック、およびソース コード ファイルを調査します。

    ![プロジェクトを開く](media\azure-stack-solution-machine-learning\image16.png)

### <a name="attach-a-dsvm-compute-target"></a>DSVM コンピューティング ターゲットをアタッチする

DSVM が作成されたら、それを Azure ML プロジェクトにアタッチします。

1.  Azure ML Workbench アプリ内から、**[ファイル]**->**[PowerShell を開きます]** を選択することによって Azure ML Workbench CLI を起動します。

    ![Alt text](media\azure-stack-solution-machine-learning\image17.png)

1.  PowerShell プロンプトが開いたら、次のコマンドを使用します。

    ```PowerShell  
        az login
    ```

1.  次のプロンプトを受信します。

     ![Alt text](media\azure-stack-solution-machine-learning\image18.png)

1.  プロンプトで詳細に示されているサイトを参照し、提供されたコードを入力します。

    ![Alt text](media\azure-stack-solution-machine-learning\image19.png)

1.  メッセージが表示されたら [続行] を選択し、Azure ML 試験アカウントが関連付けられている Azure アカウントを選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image20.png)

1.  Azure ML Workbench CLI は次に、次のプロンプトを送信します。

    ![Alt text](media\azure-stack-solution-machine-learning\image21.png)

1.  ML アカウントとワークスペースのログインが成功として表示されたら、DSVM をアタッチします。

    ```PowerShell  
        az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password>
    ```

    次の通知が表示されます。

    ![Alt text](media\azure-stack-solution-machine-learning\image22.png)

    ```PowerShell  
        # prepare the Docker image on the DSVM 
        az ml experiment prepare -c <compute target name>
    ```

このプロセスは少し時間を要するうえ、さまざまな Docker イメージを取得し、それらを登録してから、必要なコードやアプリケーションをそれらに適用するため、膨大な量のテキストが生成されます。

これで、この DSVM 上で実験を実行できます。

### <a name="create-a-data-preparation-package"></a>データ準備パッケージを作成する

次に、Azure Machine Learning Workbench でデータの準備を開始します。 Workbench で実行された各変換は、ローカルのデータ準備パッケージ (\*.dprep ファイル) 内に JSON 形式で格納されます。 このデータ準備パッケージは、Workbench でのデータ準備作業のための主要なコンテナーです。

このデータ準備パッケージは、後でランタイム (ローカル C\#/CoreCLR、Scala/Spark、Scala/HDI など) に渡すことができます。

1.  フォルダー アイコンを選択してファイル ビューを開き、**iris.csv** を選択して、そのファイルを開きます。

    このファイルには、5 つの列と 50 個の行を含んだテーブルがあります。 4 つの列は数値特徴列です。 5 つ目の列は文字列ターゲット列です。 どの列にもヘッダー名はありません。

    ![iris.csv](media\azure-stack-solution-machine-learning\image23.png)

1.  **データ ビュー**で、新しいデータ ソースを追加するために、プラス記号 (**+**) を選択します。 **[データ ソースの追加]** ページが開きます。

    ![Azure Machine Learning Workbench のデータ ビュー](media\azure-stack-solution-machine-learning\image24.png)

1.  **[テキスト ファイル (\*.csv、\*.json、\*.txt、...)]** を選択します。

    ![Azure Machine Learning Workbench でのデータ ソース](media\azure-stack-solution-machine-learning\image25.png)

1.  **[次へ]** を選択します。

2.  ファイル **iris.csv** を参照し、**[完了]** を選択します。 区切り記号やデータ型などのパラメーターには、既定値が使用されます。

    > [!Important]  
    > この演習用の現在のプロジェクト ディレクトリ内から **iris.csv** ファイルを選択します。 そうしないと、以降の手順が失敗する可能性があります。

    ![iris を選択する](media\azure-stack-solution-machine-learning\image26.png)

1.  `*iris-1.dsource` という名前の新しいファイルが作成されます。 サンプル プロジェクトには番号なしの **iris.dsource** ファイルが既に付属しているため、このファイルには `-1` が付いた一意の名前が付けられます。

    ファイルが開き、データが表示されます。 **Column1** から **Column5** までの一連の列ヘッダーが、このデータ セットに自動的に追加されます。 一番下までスクロールし、データ セットの最後の行が空であることに注意してください。 この行が空になっているのは、CSV ファイルに余分な改行があるためです。

    ![iris データ ビュー](media\azure-stack-solution-machine-learning\image27.png)

1.  **[メトリック]** ボタンを選択します。 ヒストグラムが生成され、表示されます。

    **[データ]** ボタンを選択することによって、元のデータ ビューに切り替えます。

    ![iris データ ビュー](media\azure-stack-solution-machine-learning\image28.png)

1.  ヒストグラムを観察します。 各列に対して統計情報の完全なセットが計算されています。

    ![iris データ ビュー](media\azure-stack-solution-machine-learning\image29.png)

1.  **[準備]** ボタンを選択して、データ準備パッケージの作成を開始します。 **[準備]** ダイアログ ボックスが開きます。

    サンプル プロジェクトには、既定で選択される **iris.dprep** データ準備ファイルが含まれています。

    ![iris データ ビュー](media\azure-stack-solution-machine-learning\image30.png)

1.  メニューから **[+ 新しいデータ準備パッケージ]** を選択することによって、新しいデータ準備パッケージを作成します。

    ![iris データ ビュー](media\azure-stack-solution-machine-learning\image31.png)

1.  パッケージの名前に新しい値 (**iris-1** を使用) を入力して、**[OK]** を選択します。

    **iris-1.dprep** という名前の新しいデータ準備パッケージが作成され、データ準備エディターで開かれます。

    ![iris データ ビュー](media\azure-stack-solution-machine-learning\image32.png)

    次に、データ準備が必要です。

1.  それぞれの列ヘッダーを選択して、ヘッダーのテキストを編集可能にします。 次に、各列の名前を以下のように変更します。

    5 つの列にそれぞれ「**Sepal Length**」、「**Sepal Width**」、「**Petal Length**」、「**Petal Width**」、「**Species**」と順に入力します。

    ![列名を変更する](media\azure-stack-solution-machine-learning\image33.png)

1.  個別の値をカウントします。

    1.  **Species** 列を選択します

    2.  右クリックしてそれを選択します。

    3.  メニューから **[値のカウント]** を選択します。

        データの下に **[Inspectors]\(インスペクター\)** ウィンドウが表示されます。 4 つのバーを含むヒストグラムが表示されます。 ターゲット列には、**Iris-virginica**、**Iris-versicolor**、**Iris-setosa**、および **(null)** 値という 4 つの個別の値が含まれています。

    ![[Value Counts]\(値のカウント\) を選択する](media\azure-stack-solution-machine-learning\image34.png)

    ![値のカウントのヒストグラム](media\azure-stack-solution-machine-learning\image35.png)

1.  null 値を除外するために、"(null)" 棒グラフを選択し、マイナス記号 (**-**) を選択します。

    (null) の行が灰色に変わり、除外されていることがわかります。

    ![null 値をフィルター処理する](media\azure-stack-solution-machine-learning\image36.png)

1.  **[ステップ]** ウィンドウに列挙されている個々のデータ準備ステップに注目します。 列の名前が変更され、null 値の行がフィルター処理されると、各アクションがデータ準備手順として記録されます。 各設定を調整したり、手順を並べ替えたり、手順を削除したりするには、個々の手順を編集します。

    ![Alt text](media\azure-stack-solution-machine-learning\image37.png)

1.  データ準備エディターを閉じます。 グラフ アイコンが付いた **[iris-1]** タブの **X** アイコンを選択して、このタブを閉じます。この作業は、**[データ準備]** 見出しの下に表示されている **iris-1.dprep** ファイルに自動的に保存されます。

    ![閉じます](media\azure-stack-solution-machine-learning\image38.png)

### <a name="generate-python-code-to-invoke-a-data-preparation-package"></a>データ準備パッケージを呼び出す Python コードを生成する

データ準備パッケージの出力は、Python または Jupyter Notebook で直接調査できます。 パッケージは、ローカル Python、Spark (Docker 内を含む)、HDInsight などの複数のランタイムにまたがって実行できます。

1.  [Data Preparations]\(データ準備\) タブで **iris-1.dprep** ファイルを特定します。

2.  **[iris-1.dprep]** ファイルを右クリックし、コンテキスト メニューから **[Generate Data Access Code File]\(データ アクセス コード ファイルの生成\)** を選択します。

    ![コードを生成する](media\azure-stack-solution-machine-learning\image39.png)

    **iris-1.py** という名前の新しいファイルが開き、そこには、データ準備パッケージとして作成されたロジックを呼び出す次のコード行が含まれています。

    ```Python
    # Use the Azure Machine Learning data preparation package
    from azureml.dataprep import package

    # Use the Azure Machine Learning data collector to log various metrics
    from azureml.logging import get_azureml_logger
    logger = get_azureml_logger()

    # This call will load the referenced package and return a DataFrame.
    # If run in a PySpark environment, this call returns a
    # Spark DataFrame. If not, it will return a Pandas DataFrame.
    df = package.run('iris-1.dprep', dataflow_idx=0)
    # Remove this line and add code that uses the DataFrame
    df.head(10)
    ```

    このコードが実行されるコンテキストに応じて、drep は異なる種類のデータフレームを表します。

    -  Python ランタイムで実行すると、[pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) が使用されます。

    -  Spark コンテキストで実行すると、[Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html) が使用されます。

### <a name="review-irissklearnpy-and-the-configuration-files"></a>iris_sklearn.py と構成ファイルを確認する

1.  開いたプロジェクトで、左端のウィンドウにある **[ファイル]** ボタン (フォルダー アイコン) を選択して、プロジェクト フォルダー内のファイル一覧を開きます。

    ![Azure Machine Learning Workbench プロジェクトを開く](media\azure-stack-solution-machine-learning\image40.png)

1.  **iris_sklearn.py** Python スクリプト ファイルを選択します。

    ![スクリプトを選択](media\azure-stack-solution-machine-learning\image41.png)

    Workbench の新しいテキスト エディター タブにコードが表示されます。

    > [!Note]  
    > このサンプル プロジェクトは頻繁に更新されるため、表示されるコードが前のコードとまったく同じにならない可能性があります。

    ![ファイルを開く](media\azure-stack-solution-machine-learning\image42.png)

1.  Python スクリプト コードを観察して、コーディングのスタイルを把握します。

    スクリプト **iris_sklearn.py** は、次のタスクを実行します。  

    -  既定のデータ準備パッケージ (**iris.dprep**) を読み込んで [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) を作成します。

    -   問題を解きづらくするためにランダム機能を追加します  あやめデータセットはほぼ 100% の精度で簡単に分類できるほどに小さいため、ランダム性が必要となります。

    -  ロジスティック回帰モデルを構築するには、scikit-learn 機械学習ライブラリを使用します。 このライブラリには、Azure Machine Learning Workbench が既定で付属しています。

    -  [pickle](https://docs.python.org/3/library/pickle.html) ライブラリを使用して、モデルを `outputs` フォルダーのファイルにシリアル化します。

    -  シリアル化されたモデルを読み込み、逆シリアル化してメモリに展開します。

    -  逆シリアル化されたモデルを使用して、新しいレコードに対する予測を行います。

    -  [matplotlib](https://matplotlib.org/) ライブラリを使用して、混同行列と多クラス ROC (Receiver Operating Characteristic: 受信者動作特性) 曲線という 2 つのグラフをプロットし、それらを出力フォルダーに保存します。 明確でない場合は、このライブラリを環境内にインストールしてください。

    -  実行履歴内の正則化項とモデル精度を自動的にプロットします。 `run_logger` オブジェクトは、正則化項とモデル精度をログに記録する目的で終始使用されます。

### <a name="run-irissklearnpy-in-the-local-environment"></a>ローカル環境で iris_sklearn.py を実行する

1.  Azure Machine Learning のコマンド ライン インターフェイス (CLI) を起動します。

    1.  Azure Machine Learning Workbench を起動します。

    2.  [ワークベンチ] メニューの **[ファイル]**> **[コマンド プロンプトを開く]** を選択します。

    Azure Machine Learning コマンド ライン インターフェイス (CLI) ウィンドウは、Windows 上のプロジェクト フォルダー `C:\Temp\\myIris\` で起動します。 このプロジェクトは、このチュートリアルのパート 1 で作成されたものと同じです。

    > [!Important]  
    > 次の手順を実行するには、この CLI ウィンドウを使用します。

1.  CLI ウィンドウで、Python プロット ライブラリ **matplotlib** がまだインストールされてない場合はインストールします。

    **iris_sklearn.py** スクリプトは、**scikit-learn** と **matplotlib** の 2 つの Python パッケージに依存しています。 **scikit-learn** パッケージは、便宜のために Azure Machine Learning Workbench によってインストールされます。 ただし、**matplotlib** のインストールが必要になる可能性があります。

    **matplotlib** をインストールせずに先に進んでも、このチュートリアル内のコードはそのまま正常に実行されます。 ただし、履歴で視覚化されるような混同行列の出力や多クラス ROC 曲線のプロットをコードから生成することはできません。

    ```CLI
    pip install matplotlib
    python -m pip install --upgrade pip
    ```

    このインストールの所要時間は約 1 分です。

1.  Workbench アプリケーションに戻ります。

2.  **[iris_sklearn.py]** というタブを見つけます。

    ![スクリプトのタブを見つける](media\azure-stack-solution-machine-learning\image43.png)

1.  そのタブのツールバーで、実行環境として **[ローカル]** を、実行するスクリプトとして [iris_sklearn.py] を選択します。 そのように選択されている場合もあります。

    ![Alt text](media\azure-stack-solution-machine-learning\image44.png)

1.  ツールバーの右側に移動し、**[引数]** フィールドに「0.01」と入力します。

    この値は、ロジスティック回帰モデルの正則化項に対応しています。

    ![ローカルとスクリプトの選択](media\azure-stack-solution-machine-learning\image45.png)

1.  **[実行]** ボタンを選択します。 ジョブが直ちにスケジュールされます。 Workbench ウィンドウの右側の **[ジョブ]** ウィンドウにジョブが一覧表示されます。

    ![ローカルとスクリプトの選択](media\azure-stack-solution-machine-learning\image46.png)

    しばらくすると、ジョブの状態が **[送信しています]** から **[実行中]** に、最終的には **[完了]** に移行します。

1.  **[ジョブ]** ウィンドウで、ジョブの状態テキストの **[完了]** をクリックします。

    ![sklearn の実行](media\azure-stack-solution-machine-learning\image47.png)

    ポップアップ ウィンドウが開き、実行の標準出力 (stdout) テキストが表示されます。 stdout テキストを閉じるには、ポップアップ ウィンドウの右上にある **[閉じる]** (**x**) ボタンを選択します。

    ![標準出力](media\azure-stack-solution-machine-learning\image48.png)

1.  **[ジョブ]** ウィンドウの同じジョブの状態で、**[完了]** 状態と開始時刻のすぐ上にある青色のテキスト **iris_sklearn.py \[n\] * *(* n* は実行回数) を選択します。 **[Run Properties]\(実行プロパティ\)** ウィンドウが開き、その特定の実行について、次の情報が表示されます。

    -  **実行プロパティ**情報

    -  **Outputs**

    -  **メトリック**

    -  **視覚化** (存在する場合)

    -  **ログ**

    実行が完了すると、ポップアップ ウィンドウに次の結果が表示されます。

    > [!Note]  
    > このチュートリアルでは、以前に設定されたトレーニングに何らかのランダム化を導入しているため、正確な結果がここに示されている結果と異なる可能性があります。

    ```Python  
        Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]

        Iris dataset shape: (150, 5)
        Regularization rate is 0.01
        LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
            intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
            penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
            verbose=0, warm_start=False)
        Accuracy is 0.6792452830188679

        ==========================================
        Serialize and deserialize using the outputs folder.

        Export the model to model.pkl
        Import the model from model.pkl
        New sample: [[3.0, 3.6, 1.3, 0.25]]
        Predicted class is ['Iris-setosa']
        Plotting confusion matrix...
        Confusion matrix in text:
        [[50  0  0]
        [ 1 37 12]
        [ 0  4 46]]
        Confusion matrix plotted.
        Plotting ROC curve....

        ROC curve plotted.
        Confusion matrix and ROC curve plotted. See them in Run History details pane.

    ```

1.  **[Run Properties]\(実行プロパティ\)** タブを閉じて **[iris_sklearn.py]** タブに戻ります。

1.  追加の実行を繰り返します。

**[引数]** フィールドには、`0.001` から `10` の範囲で一連の値を入力してください。 **[実行]** を選択して、さらに何度かコードを実行します。 毎回変更される引数の値をコード内のロジスティック回帰モデルに渡すことにより、そのたびに異なる結果が得られます。

### <a name="review-the-run-history-in-detail"></a>実行履歴を詳しく確認する

Azure Machine Learning Workbench では、すべてのスクリプト実行が実行履歴レコードとしてキャプチャされます。 特定のスクリプトの実行履歴は、**[実行]** ビューを開くことによって表示します。

1.  **実行**の一覧を開くには、左側のツール バーにある **[実行]** ボタン (時計のアイコン) を選択します。 次に、**iris_sklearn.py** を選択して iris_sklearn.py の **[実行ダッシュボード]** を表示します。

    ![実行ビュー](media\azure-stack-solution-machine-learning\image49.png)

1.  **[Run Dashboard]\(実行ダッシュボード\)** タブが表示されます。

    複数回の実行を通じてキャプチャされた統計情報を確認します。 このタブの一番上にはグラフが表示されます。各実行には連続した番号が付けられ、実行の詳細が画面の一番下にあるテーブルに一覧表示されます。

    ![実行ダッシュボード](media\azure-stack-solution-machine-learning\image50.png)

1.  テーブルをフィルター処理し、いずれかのグラフを選択すると、各実行の状態、期間、精度、正則化項が表示されます。

2.  **Runs** テーブルで、いくつかの実行の横にあるチェック ボックスをオンにします。 **[比較]** ボタンを選択すると、詳細な比較ウィンドウが表示されます。 横並びに表示される比較結果を確認してください。

3.  **[比較]** ウィンドウの左上にある、**実行の一覧**に戻るボタンを選択し、**[Run Dashboard]\(実行ダッシュボード\)** に戻ります。

    ![実行の一覧に戻る](media\azure-stack-solution-machine-learning\image51.png)

1.  個々の実行を選択して、実行の詳細ビューを表示します。 **[Run Properties]\(実行プロパティ\)** セクションに、選択した実行の統計情報が一覧表示されることに注目してください。 出力フォルダーに書き込まれたファイルは **[出力]** セクションに一覧表示され、そこからファイルをダウンロードします。

    ![実行の詳細](media\azure-stack-solution-machine-learning\image52.png)

**[Visualizations]\(視覚化\)** セクションには、混同行列と多クラス ROC 曲線の 2 つのプロットが表示されます。 ログ ファイルはすべて **[ログ]** セクションでも確認できます。

### <a name="run-scripts-in-the-azure-machine-learning-ml-workbench-cli-window"></a>Azure Machine Learning (ML) Workbench CLI ウィンドウでスクリプトを実行する

1.  Azure Machine Learning のコマンド ライン インターフェイス (CLI) を起動します。

    1.  Azure Machine Learning Workbench を起動します。

    2.  [ワークベンチ] メニューの **[ファイル]** > **[コマンド プロンプトを開く]** を選択します。

    CLI プロンプトは、Windows 上のプロジェクト フォルダー `C:\\Temp\\myIris` で起動します。 これは、このチュートリアルのパート 1 で作成されたプロジェクトです。

    > [!Important]  
    > 次の手順を実行するには、この CLI ウィンドウを使用します。

1.  CLI ウィンドウで Azure にログインします。 az login の詳細については、[こちら](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)を参照してください。

    既にログインしている場合は、この手順を省略します。

1.  コマンド プロンプトに、次のコマンドを入力します。

    ```CLI
        az login
    ```

    このコマンドは、ブラウザーの [https://aka.ms/devicelogin](https://aka.ms/devicelogin) で使用するコードを返します。

1.  ブラウザー内の [https://aka.ms/devicelogin](https://aka.ms/devicelogin) に移動します。 CLI で受信したコードを入力します。

    Workbench アプリと CLI は、Azure リソースに対して認証するときに、独立した資格情報キャッシュを使用します。 キャッシュされたトークンの期限が切れるまで、再度の認証は必要ありません。

1.  組織に複数の Azure サブスクリプションがある場合は (エンタープライズ環境)、使用されるサブスクリプションを設定します。 そのサブスクリプションを見つけ、サブスクリプション ID を使用して設定してから、それをテストします。

1.  このコマンドを使用して、アクセスされるすべての Azure サブスクリプションを一覧表示します。

    ```CLI
        az account list -o table 
    ```

    **az account list** コマンドは、ログインに使用できるサブスクリプションの一覧を返します。 複数存在する場合は、使用されるサブスクリプションのサブスクリプション ID 値を識別します。

1.  既定のアカウントとして使用される Azure サブスクリプションを設定します。

    ```CLI
        az account set -s <the-subscription-id
    ```

    ここで、<-subscription-id> は、使用されるサブスクリプションの ID 値です。 かっこは含めないでください。

1.  現在のサブスクリプションに関する詳細情報を要求して、新しいサブスクリプション設定を確認します。

    ```CLI
        az account show
    ```

1.  CLI ウィンドウで、**iris_sklearn.py** スクリプトを実験として送信します。

    iris_sklearn.py の実行は、ローカル コンピューティング コンテキストに対して実行されます。

1.  Windows の場合:

    ```CLI
        az ml experiment submit -c local .\\iris_sklearn.py
    ```

1.  macOS の場合:

    ```CLI
        az ml experiment submit -c local iris_sklearn.py
    ```

    出力は次のようになります。 

    ```
        RunId: myIris_1521077190506

    Executing user inputs .....
    ===========================

    Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:52:12) 
    [GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)]

    Iris dataset shape: (150, 5)
    Regularization rate is 0.01
    LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
            intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
            penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
            verbose=0, warm_start=False)
    Accuracy is 0.6792452830188679

    ==========================================
    Serialize and deserialize using the outputs folder.

    Export the model to model.pkl
    Import the model from model.pkl
    New sample: [[3.0, 3.6, 1.3, 0.25]]
    Predicted class is ['Iris-setosa']
    Plotting confusion matrix...
    Confusion matrix in text:
    [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
    Confusion matrix plotted.
    Plotting ROC curve....
    ROC curve plotted.
    Confusion matrix and ROC curve plotted. See them in Run History details page.

    Execution Details
    =================
    RunId: myIris_1521077190506

    ```

6.  出力結果を確認します。 すべてが同じ出力で、Workbench がこのスクリプトを実行したときと同じ結果になる必要があります。

7.  Workbench に戻って次の手順を実行します。

    左側のウィンドウにあるフォルダー アイコンを選択してプロジェクト ファイルを一覧表示します。  **run.py** という名前の Python スクリプトを開きます。 このスクリプトは、さまざまな正則化項をループ処理するのに役立ちます。 

    ![実行の一覧に戻る](media\azure-stack-solution-machine-learning\image53.png)

1.  さまざまな正則化項で実験を複数回実行してください。

    このスクリプトは、`10.0` の正則化項 (極端に大きな数値) で ` aniris_sklearn.pyjob` を起動します。 このスクリプトはその後、次の実行でこの率を次々に半分に減らしていき、この率が `0.005` 未満になる直前まで続けます。 このスクリプトには次のコードが記述されています。

    ![実行の一覧に戻る](media\azure-stack-solution-machine-learning\image54.png)

1.  コマンド ラインから次のように **run.py** スクリプトを実行します。

    ```CLI
        python run.py
    ```
このコマンドは、`iris_sklearn.py` を異なる正則化項で複数回送信します。

`run.py` が完了すると、Workbench の実行履歴リスト ビューにさまざまなメトリックのグラフが表示されます。

### <a name="run-scripts-in-an-ubuntu-based-data-science-virtual-machine-dsvm-on-azure"></a>Azure 上の Ubuntu ベースのデータ サイエンス仮想マシン (DSVM) でスクリプトを実行する

リモート Linux コンピューター上の Docker コンテナーでスクリプトを実行するには、そのリモート コンピューターを実行するために SSH アクセス (ユーザー名とパスワード) が必要です。 また、マシンには Docker エンジンがインストールされ、実行されている必要があります。

1.  aml_config の下の生成された <your DSVM Name>.run 構成ファイルを編集し、フレームワークを既定値の PySparktoPython から変更します。

    ```yaml  
    Framework: Python
    ```
1.  CLI ウィンドウで前と同じコマンドを発行します。今度は、ターゲット *<DSVM>* を使用して、リモート Docker コンテナーで iris_sklearn.py を実行します (<DSVM> をデータ サイエンス VM の名前 (かっこはなし) に置き換えてください)。

    ```CLI
        az ml experiment submit -c <DSVM> iris_sklearn.py
    ```

このコマンドは、リモート Linux VM 上で実行される点を除き、docker-python 環境にあるかのように実行されます。 CLI ウィンドウには同じ出力情報が表示されます。

### <a name="download-the-model-pickle-file"></a>モデルの pickle ファイルをダウンロードする

このチュートリアルの前のパートでは、ローカルの Machine Learning Workbench で **iris_sklearn.py** スクリプトを実行しました。 その際、Python オブジェクトのシリアル化パッケージとして広く使われている [pickle](https://docs.python.org/3/library/pickle.html) を使ってロジスティック回帰モデルをシリアル化しました。

1.  Machine Learning Workbench アプリケーションを開きます。 次に、チュートリアル シリーズの前のパートで作成された **myIris** プロジェクトを開きます。

2.  プロジェクトが開いたら、左側のウィンドウにある **[ファイル]** ボタン (フォルダー アイコン) を選択して、プロジェクト フォルダー内のファイル一覧を開きます。

3.  **iris_sklearn.py** ファイルを選択します。 Workbench の新しいテキスト エディター タブに Python コードが表示されます。

4.  pickle ファイルが生成されている場所を **iris_sklearn.py** ファイルで確認します。 Ctrl + F キーを押して **[検索]** ダイアログ ボックスを開き、Python コード内で **pickle** という単語を探してください。

このコード スニペットを見ると、pickle の出力ファイルがどのように生成されているのかがわかります。 pickle の出力ファイルは、**model.pkl** という名前でディスクに保存されます。

    ```Python
        print("Export the model to model.pkl")
        f = open('./outputs/model.pkl', 'wb')
        pickle.dump(clf1, f)
        f.close()

    ```

1.  前回実行時の出力ファイルからモデルの pickle ファイルを探します。

    **iris_sklearn.py** スクリプトが実行されると、モデル ファイルは、**model.pkl** という名前で **outputs** フォルダーに書き込まれます。 このフォルダーは、ローカルのプロジェクト フォルダーではなく、このスクリプトを実行するために選択された実行環境内に存在します。 

    1. このファイルを見つけるには、左側のウィンドウにある **[実行]** ボタン (時計のアイコン) を選択して、**[すべての実行]** の一覧を開きます。  

    2. **[すべての実行]** タブが表示されます。 実行のテーブルで、ターゲットが **[ローカル]** であり、スクリプト名が **[iris_sklearn.py]** であった最近の実行のいずれかを選択します。  

    3. **[Run Properties]\(実行プロパティ\)** ウィンドウが開きます。 ウィンドウの右上のセクションにある **[出力]** セクションに注目してください。 d\. pickle ファイルをダウンロードするには、**model.pkl** ファイルの横のチェック ボックスをオンにし、**[ダウンロード]** を選択します。 このファイルをプロジェクト フォルダーのルートに保存します。 このファイルは、この後の手順で必要になります。  

    ![pickle ファイルをダウンロードする](media\azure-stack-solution-machine-learning\image55.png)

### <a name="get-scoring-script-and-schema-files"></a>スコア付けスクリプトとスキーマ ファイルを取得する

Web サービスをモデル ファイルと共にデプロイするには、スコア付けスクリプトが必要です。 オプションで、Web サービスの入力データ用のスキーマが必要です。 スコア付けスクリプトを実行すると、現在のフォルダーから **model.pkl** ファイルが読み込まれ、このファイルを使用して、新しい予測が生成されます。

1.  Machine Learning Workbench アプリケーションを開きます。 次に、チュートリアル シリーズの前のパートで作成された **myIris** プロジェクトを開きます。

2.  プロジェクトが開いたら、左側のウィンドウにある **[ファイル]** ボタン (フォルダー アイコン) を選択して、プロジェクト フォルダー内のファイル一覧を開きます。

3.  **score_iris.py** ファイルを選択します。 Python スクリプトが表示されます。 このファイルは、スコア付けファイルとして使用されます。

    ![スコア付けファイル](media\azure-stack-solution-machine-learning\image56.png)

1.  スキーマ ファイルを取得するには、このスクリプトを実行します。 コマンド バーで **[local]** 環境と **[score_iris.py]** スクリプトを選択し、**[実行]** を選択します。

    このスクリプトは、モデルに必要な入力データ スキーマをキャプチャする JSON ファイルを **[出力]** セクション内に作成します。

1.  **[プロジェクト ダッシュボード]** ウィンドウの右側の **[ジョブ]** ウィンドウに注目します。 最新の **score_iris.py** ジョブにより、緑色の **[完了]** 状態が表示されるまで待ちます。 次に、最新のジョブ実行の **score_iris.py** というハイパーリンクを選択すると、実行の詳細が表示されます。

2.  **[Run Properties]\(実行プロパティ\)** ウィンドウの **[出力]** セクションで、新しく作成された **service_schema.json** ファイルを選択します。 ファイル名の横のチェック ボックスをオンにし、**[ダウンロード]** を選択します。 このファイルをプロジェクトのルート フォルダーに保存します。

3.  以前のタブおよび **score_iris.py** スクリプトに戻ります。 データ収集を使用して、Web サービスからモデルの入力と予測をキャプチャできます。 以降の手順は、データ収集を目的としています。

4.  クラス **ModelDataCollector** をインポートする、ファイルの上部にあるコードを確認してください。 これには、モデルのデータ収集機能が含まれています。

    ```Python  
        from azureml.datacollector import ModelDataCollector
    ```

1.  **init()** 関数の次のコード行によって、**ModelDataCollector** がインスタンス化されることを確認します。

    ```Python  
        global inputs_dc, prediction_dc
        inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
        prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

1.  **run(input_df)** 関数の次のコード行によって、入力データと予測データが収集されることを確認します。

    ```Python  
        inputs_dc.collect(input_df)
        prediction_dc.collect(pred)
    ```

次に、モデルを操作できるように環境を準備します。

## <a name="step-5-deploy-and-use-azure-container-registry"></a>手順 5: Azure Container Registry をデプロイして使用する

Azure Container Registry をデプロイして使用します。

**az acr create** コマンドで Azure Container Registry を作成します。 レジストリの名前は Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 リソース グループは同じです。

    ```CLI
        az acr create --resource-group <ResourceGroup> --name  <acrName> --sku Basic
    ```

### <a name="container-registry-login"></a>Container Registry のログイン

**az acr login** コマンドで ACR インスタンスにログインします。 コンテナー レジストリの作成時に割り当てられた一意名を入力します。

    ```CLI
        az acr login --name <acrName>
    ```

このコマンドは、完了すると "ログインに成功しました" というメッセージを返します。

### <a name="prepare-to-operationalize-locally-for-development-and-testing-the-service"></a>サービスの開発およびテストのためにローカルで操作できるように準備する

開発およびテストのためにローカル コンピューター上の Docker コンテナーで実行するには、*ローカル モード* デプロイを使用します。

以降の手順でモデルを運用可能な状態にするためには、Docker エンジンがローカルで実行されている必要があります。 対応するヘルプ メッセージを表示するには、各コマンドの最後に `-h` フラグを使用します。

    > [!Note]  
    > If Docker engine is not locally available, proceed by creating a cluster in Azure for deployment and keep the cluster for re-use, or delete it after the tutorial to avoid ongoing charges.

    > [!Note]  
    > Web services deployed locally do not appear in Azure Portal's list of services. They will be running in Docker on the local machine.

1.  コマンド ライン インターフェイス (CLI) を開きます。 Machine Learning Workbench アプリケーションの **[ファイル]** メニューの **[コマンド プロンプトを開く]** を選択してください。

    現在のプロジェクト フォルダーの場所 **c:\\temp\\myIris** にコマンド ライン プロンプトが開きます。

1.  Azure リソース プロバイダー **Microsoft.ContainerRegistry** がサブスクリプションに登録されていることを確認してください。す。 手順 3. で環境を作成する前に、このリソース プロバイダーを登録します。 次のコマンドを使用して、これが既に登録されているかどうかを確認します。

    ```CLI
        az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
    ```

    この出力を表示します。

    ```CLI
        Provider                                    Status 
        --------                                    --------
        Microsoft.Authorization                     Registered 
        Microsoft.ContainerRegistry                 Registered 
        microsoft.insights                          Registered 
        Microsoft.MachineLearningExperimentation    Registered 
    ```

    **Microsoft.ContainerRegistry** が登録されていない場合は、次のコマンドを使用します。

    ```CLI
    az provider register --namespace Microsoft.ContainerRegistry
    ```

    登録には数分かかる場合があります。 前の **az provider list** コマンドまたは次のコマンドを使用して、登録の状態を確認します。

    ```CLI
    az provider show -n Microsoft.ContainerRegistry
    ```

    出力の 3 行目に **"registrationState": "Registering"** と表示されます。 しばらく待ってから、出力に **"registrationState": "Registered"** と表示されるまで **show** コマンドを繰り返します。

1.  環境を作成します。 この手順は、環境ごとに 1 回実行します。

    次の setup コマンドには、サブスクリプションへの共同作成者のアクセス権が必要です。 デプロイ先のリソース グループへの共同作成者のアクセス権が必要です。 -gflag を使用して、setup コマンドの一部としてリソース グループ名を指定します。

    ```CLI
    az ml env setup -n <new deployment environment name> --location <e.g. eastus2> -g <existing resource group name>
    ```

    画面の指示に従い、Docker イメージを格納するためのストレージ アカウント、Docker イメージをリストする Azure Container Registry、テレメトリを収集する Azure Application Insights アカウントをプロビジョニングします。 **-cswitch を使用している場合、このコマンドは、さらに Container Service クラスターを作成します**。

    クラスター名は、環境を識別するための方法です。 この場所は、Azure Portal から作成されたモデル管理アカウントの場所と同じである必要があります。

    環境が正しく設定されていることを確認するには、次のコマンドを使用して状態を確認します。

    ```CLI
    az ml env show -n <deployment environment name> -g <existing resource group name>
    ```

    手順 5. で環境を設定する前に、次に示すように、[プロビジョニング状態] の値が [成功] であることを確認してください。

    ![プロビジョニング状態](media\azure-stack-solution-machine-learning\image57.png)

1.  環境を設定します。

    設定が完了したら、環境の運用化に必要な環境変数を次のコマンドで設定します。 以前に手順 3. で使用されたのと同じ環境名を使用します。 リソース グループ名には、セットアップ プロセスが完了したときにコマンド ウィンドウに出力された名前を使用します。

    ```CLI
        az ml env set -n <deployment environment name> -g <existing resource group name>
    ```

1.  ローカル Web サービスのデプロイのために操作可能にした環境の正しい構成を確認するには、次のコマンドを入力します。

    ```CLI
    az ml env show
    ```

    次に、リアルタイムの Web サービスを作成します。

    > [!Note]  
    > モデル管理アカウントと環境は、以降の Web サービスのデプロイのために再利用します。 これらを Web サービスごとに作成する必要はありません。 1 つのアカウントまたは環境に複数の Web サービスを関連付けることができます。

### <a name="create-a-real-time-web-service-by-using-separate-commands"></a>個別のコマンドを使用してリアルタイム Web サービスを作成する

前に示した **az ml service create realtime** コマンドの代わりに、これらの手順を個別に実行することもできます。

まずモデルを登録します。 次にマニフェストを生成し、Docker イメージを作成して、Web サービスを作成します。 この段階的なアプローチにより、各手順での柔軟性が高まります。 さらに、前の手順で生成されたエンティティも再利用できます。

1. pickle ファイル名を指定してモデルを登録します。

    ```CLI
    az ml model register --model model.pkl --name model.pkl
    ```

    このコマンドにより、モデル ID が生成されます。

2.  マニフェストを作成します。

    マニフェストを作成するには、次のコマンドを使用し、前の手順で出力されたモデル ID を指定します。 
    
    ```CLI
    az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
    ```

    このコマンドにより、マニフェスト ID が生成されます。

3.  Docker イメージを作成します。

    Docker イメージを作成するには、次のコマンドを使用し、前の手順で出力されたマニフェスト ID 値を指定します。 また、`-c` スイッチで conda 依存関係を使用することもできます。 
    
    ```CLI
    az ml image create -n irisimage --manifest-id <manifest ID> -c aml_config\conda_dependencies.yml
    ```
    
    このコマンドにより、Docker イメージ ID が生成されます。

2.  サービスを作成します。

    サービスを作成するには、次のコマンドを使用し、前の手順で出力されたイメージ ID を指定します。 
    
    ```CLI
    az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
    ```
    
    このコマンドにより、Web サービス ID が生成されます。
    
    次に、Web サービスを実行します。

## <a name="step-6-deploy-a-kubernetes-cluster-to-azure-stack"></a>手順 6: Kubernetes クラスターを Azure Stack にデプロイする

Kubernetes クラスターを Azure Stack にデプロイします。

Azure Stack 上の Azure Container Services (ACS) エンジンによって生成された Azure Resource Manager テンプレートを使用して、Kubernetes をインストールできます。 [*Kubernetes*](https://kubernetes.io/) は、コンテナー内のアプリケーションのデプロイ、スケーリング、および管理を自動化するためのオープンソース システムです。 [*コンテナー*](https://www.docker.com/what-container)は、VM と同様に、イメージに含まれています。 VM とは異なり、コンテナー イメージには、コード、コードを実行するためのランタイム、特定のライブラリ、設定などの、アプリケーションを実行するために必要なリソースが含まれています。

Kubernetes は、次のために使用します。

 -  数秒でデプロイできる極めてスケーラブルかつアップグレード可能なアプリケーションを開発する。

 -  アプリケーションの設計を簡素化し、異なる Helm アプリケーションによって信頼性を向上させる。 [*Helm*](https://github.com/kubernetes/helm) は、Kubernetes アプリケーションのインストールやそのライフサイクルの管理に役立つ、オープンソースのパッケージ化ツールです。

 -  スケールおよびアップグレード機能により、アプリケーションの正常性を容易に監視および診断する。

> [!Note]  
> クラスターのインストールには約 1 時間かかるため、適切に計画してください。

### <a name="prerequisites-for-kubernetes-cluster-deployment"></a>Kubernetes クラスターのデプロイの前提条件

開始するには、アクセス許可と Azure Stack の対応性を確認してください。

1.  「**Kubernetes クラスターを作成する (プレビュー)**」の項目が Azure Stack Marketplace で入手できることを検証します。 この項目がない場合は、Azure Stack オペレーターと協力して、この項目を Azure Stack 環境に追加します。

2.  Azure Active Directory (Azure AD) テナントにアプリケーションを作成できることを確認します。 Kubernetes のデプロイにはアクセス許可が必要です。

    アクセス許可をチェックする手順については、「[*Azure Active Directory のアクセス許可を確認する*](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)」を参照してください。

3.  SSH 公開および秘密キー ペアを生成して Azure Stack 上の Linux VM にサインインします。 公開キーは、クラスターを作成するときに必要です。 手順については、「[SSH のための認証キーを生成する](#generate-an-authenticatio-key-for-ssh)」を参照してください。

4.  Azure Stack テナント ポータル内のサブスクリプションが有効であることと、新しいアプリケーションを追加するために使用できる十分なパブリック IP アドレスが存在することを確認します。

    クラスターを Azure Stack **管理者**サブスクリプションにデプロイすることはできません。 **ユーザー** サブスクリプションを使用してください。

###  <a name="generate-an-authentication-key-for-ssh"></a>SSH のための認証キーを生成する

Windows Subsystem for Linux セッション内から、次のコマンドを使用して認証キーを生成します。 

1. 次のコマンドを入力します: 

    ```Bash  
    ssh-keygen -t rsa
    ```
    
2. メッセージが表示されたら、`id_rsa` を選択します。 
3. メッセージが表示されたら、パスフレーズを作成します。 このパスワードを後で使用するためにメモしておくことが重要です。 
    出力は次のようになります。 
    
    ```Bash  
    Your identification has been saved in `id_rsa`.
    Your public key has been saved in `id_rsa.pub`. 
    The key fingerprint is: SHA256:lUtUUjzaqWqGeolEPKeBmsnrhcNGM9Dn2OxYatt05SE  <user>@<machine-name>
    The key's randomart image is:  
    ```
    ![Alt text](media\azure-stack-solution-machine-learning\image58.png)

4. キーを生成した後、次のコマンドを使用してキー情報を貼り付けます。 
    ```Bash
    cat id_rsa.pub
    ```
    出力は次のようになります。 
    ```Bash
    ssh-rsa  AAAAB3NzaC1yc2EAAAADAQABAAABAQDHaWrAktR3BlQ356T8Qvv8O2Q/U/hsXQwS9xJbuduuc9lkJwddzgmNCyM9PooZWYtGVXyHU6SC3YH1XkwqGtKhtPb03d24hmhX1euAaqIqHHSp4WgUS5s1gNsp37L8QCSGNCnF31FWavI8bnjOjccUkMowKl8iyGN++5UyQgNuynEVUbFJjrntoDL66HUu88xDxTcVB7rqDr2QKFwYJkg4HSoHYpezJd7W8kcmv33eh0xs8nlDA7Dzu7zXpyVc54bH9XtPR6EUXaQa+UqKaNlQNiJqEs+1X/zNuK9V6NLVNiO0h3jCHI3K8o4VnZyRKHCQProasiiPLUUJ6SF/RTLN  <user>@<machine-name>
    ```
    
5. 生成されたキーを [SSH Public Key] (SSH 公開キー) フィールドにコピーします。

### <a name="create-a-service-principal-in-azure-ad"></a>Azure AD でのサービス プリンシパルの作成

1.  グローバルな [*Azure Portal*](http://portal.azure.com/) にサインインします。

2.  Azure Stack インスタンスに関連付けられた Azure AD テナントを使用してサインインします。

3.  Azure AD アプリケーションを作成します。

    1. **[Azure Active Directory]** > **[+ アプリの登録]**> **[新しいアプリケーションの登録]** を選択します。
    2. アプリケーションの**名前** を入力します。 
    3. **[Web アプリ / API]** を選択します。 
    4. **[サインオン URL]** に `http://localhost` と入力します。 
    5. **作成**を選択します。

1.  **アプリケーション ID** をメモします。 この ID はクラスターを作成するときに必要であり、**サービス プリンシパル クライアント ID** として参照されます。

2.  **[設定]** > **[キー]** の順に選択します。

    1. **[説明]** を入力します。 
    2. **[有効期限]** で **[Never expires]\(有効期限なし\)** を選択します。 
    3. **[保存]** を選択します。 キー文字列をメモします。 このキー文字列はクラスターを作成するときに必要であり、**サービス プリンシパル クライアント シークレット**として参照されます。

### <a name="give-the-service-principal-access"></a>サービス プリンシパルへのアクセスの付与

リソースを作成できるように、そのサービス プリンシパルにサブスクリプションへのアクセスを付与します。

1.  [管理ポータル](https://adminportal.local.azurestack.external/)にサインインします。

2.  **[その他のサービス]** > [**User subscriptions **] (**ユーザー サブスクリプション**) > **[+ 追加]** を選択します。

3.  作成されたサブスクリプションを選択します。

4.  **[アクセス制御 (IAM)]** > **[+ 追加]** の順に選択します。

5.  **[所有者]** ロールを選択します

6.  サービス プリンシパルのために作成されたアプリケーション名を選択します。 必要に応じて、検索ボックスに名前を入力します。

7.  **[保存]** を選択します。

8.  [Azure Stack ポータル](https://portal.local.azurestack.external)を開きます。

9.  **[+新規]** > **[コンピューティング]** > **[Kubernetes Cluster]\(Kubernetes クラスター\)** の順に選択します。 **作成**を選択します。

    ![ソリューション テンプレートのデプロイ](media\azure-stack-solution-machine-learning\image59.png)

10\. [Kubernetes クラスターを作成] で **[基本]** を選びます。

    ![Deploy Solution Template](media\azure-stack-solution-machine-learning\image60.png)

11. **[Linux VM Admin Username]\(Linux VM 管理者ユーザー名\)** を入力します。 Kubernetes クラスターと DVM の一部である Linux Virtual Machines のユーザー名

12. Kubernetes クラスターと DVM の一部として作成されたすべての Linux マシンに対する承認に使用される **[SSH 公開キー]** を入力します。

13. リージョンで一意の **[Master Profile DNS Prefix]\(マスター プロファイル DNS プレフィックス\)** を入力します。 これは、`ask8s-12345` などの、リージョンで一意の名前である必要があります。 ベスト プラクティスとして、リソース グループ名と同じ名前を選択してみてください。

    > [!Note]  
    > 各クラスターに対して、新しい一意のマスター プロファイル DNS プレフィックスを使用してください。

14. **[Agent Pool Profile Count]\(エージェント プール プロファイル数\)** を入力します。 値には、クラスター内のエージェントの数が含まれます。 1 - 4 の値を使用できます。

15. **[Service Principal ClientId]\(サービス プリンシパル クライアント ID\)** を入力します。これは、Kubernetes Azure クラウド プロバイダーによって使用されます。

16. サービス プリンシパル アプリケーションを作成するときに作成された**サービス プリンシパル クライアント シークレット**を入力します。

17. **Kubernetes Azure クラウド プロバイダーのバージョン**を入力します。 これは、Kubernetes Azure プロバイダーのバージョンです。 Azure Stack は、各 Azure Stack バージョンに対してカスタムの Kubernetes ビルドをリリースします。

18. **サブスクリプション** ID を選択します。

19. 新しいリソース グループの名前を入力するか、既存のリソース グループを選択します。 リソース名は、英数字かつ小文字にする必要があります。

20. リソース グループの **[場所]** を選択します。 これは、Azure Stack のインストールのために選択されるリージョンです。

### <a name="specify-the-azure-stack-settings"></a>Azure Stack の設定を指定する

1.  **[Azure Stack Stamp Settings]\(Azure Stack スタンプの設定\)** を選択します。

    ![ソリューション テンプレートのデプロイ](media\azure-stack-solution-machine-learning\image61.png)

2.  **テナントの Azure Resource Manager エンドポイント**を入力します。 これは、Kubernetes クラスターのリソース グループを作成するために接続する Azure Resource Manager のエンドポイントです。 統合システムには、Azure Stack オペレーターからのエンドポイントが必要です。 Azure Stack Development Kit (ASDK) の場合は、`https://management.local.azurestack.external` を使用します。

3.  テナントの **[テナント ID]** を入力します。 テナント ID 値を見つけるには、「[*テナント ID を取得する*](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)」を参照してください。

### <a name="install-kubectl-on-windows-powershell-environment"></a>Windows PowerShell 環境に kubectl をインストールする

WSL 環境内から次のコマンドを実行して、WSL 環境に kubectl をインストールします。

```PowerShell  
Install-script -name install-kubectl -scope CurrentUser -force
Install-kubectl.ps1 -downloadlocation “C:\Users\<Current User>\Documents\Kube
```

### <a name="install-kubectl-on-the-windows-subsystem-for-linux-environment"></a>Windows Subsystem for Linux 環境に kubectl をインストールする

WSL 環境内から次のコマンドを実行して、WSL 環境に kubectl をインストールします。

```Bash  
    apt-get update && apt-get install -y apt-transport-https
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add
    sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
    apt-get update
    apt-get install -y kubectl
```

### <a name="configure-kubectl"></a>kubectl を構成する

kubectl が Kubernetes クラスターを見つけてアクセスするには、*kube 構成ファイル*が必要です。 これは、kube-up.sh を使用してクラスターが作成されるか、または Minikube クラスターをデプロイするときに自動的に作成されます。 クラスターの作成の詳細については、[*ファースト ステップ ガイド*](https://kubernetes.io/docs/setup/)を参照してください。 別のユーザーによって作成されたクラスターへのアクセスについては、[*クラスター アクセスの共有に関するドキュメント*](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)を参照してください。 既定では、kubectl 構成は **~.kube/config** に配置されます。

### <a name="check-the-kubectl-configuration"></a>kubectl 構成を確認する

クラスターの状態を取得することによって、kubectl が正しく構成されていることを確認します。

```Bash  
kubectl cluster-info
```

URL 応答が表示された場合、kubectl はクラスターにアクセスするように正しく構成されています。

次のメッセージが表示された場合、kubectl は正しく構成されていないか、または Kubernetes クラスターに接続できません。

```Bash  
The connection to the server <server-name:port> was refused -  did you specify the right host or port?
```

たとえば、ローカル ラップトップで Kubernetes クラスターを実行している場合は、上で説明したコマンドを再実行するためにツール (minikube など) が必要になることがあります。

kubectl クラスター情報で URL 応答が返されるが、クラスターにまだアクセスできない場合は、次を使用して正しい構成を確認してください。

```Bash  
> kubectl cluster-info dump
```

### <a name="enable-shell-autocompletion"></a>シェルのオートコンプリートを有効にする

kubectl にはオートコンプリートのサポートが含まれているため、シェルの有効化が迅速かつ容易になります。

コンプリート スクリプト自体は kubectl によって生成され、プロファイルからアクセスできます。

### <a name="connect-to-the-kubernetes-cluster"></a>Kubernetes クラスターに接続する

クラスターに接続するには、Kubernetes コマンド ライン クライアント (**kubectl**) が必要です。 クラスターへの接続および管理のための手順は、[Azure Container Services のドキュメント](https://docs.microsoft.com/azure/container-service/dcos-swarm/)に示されています。

Kubernetes クラスターの接続には、任意の SSH クライアントを使用できます。 Windows Subsystem for Linux (WSL) が推奨されます。 Kubernetes クラスターに接続するコンピューターは、そのクラスターのために作成されたリソース グループに属し、vmd-edge-ml-stack のプレフィックスで始まります。

```Bash  
ssh <user>@vmd-edge-ml-stack.<FQDN of Kubernetes Machine in  Azure Stack>
```

Kubernetes コンピューターに接続されたら、次のコンピューターを実行して Kubernetes 構成ファイルを取得します。

```Bash  
sudo find / -name \*kubeconfig\* -type f
```

出力は次のようになります。

```Bash  
/var/lib/waagent/custom-script/download/0/acs-engine/_output/edgemlstack/kubeconfig/kubeconfig.<regionname>.json
```

このファイル パス情報をコピーし、上からコピーされた kube 構成ファイル パス内に貼り付けて、次のコマンドを実行します。

```Bash  
sudo cat  /var/lib/waagent/custom-script/download/0/acs-engine/_output/edgemlstack/kubeconfig/kubeconfig.<regionname>.json
```

出力は、生の JSON コンテンツである大きなテキスト ブロックになります。 この出力テキストをコピーし、このコードを Visual Studio ファイルに貼り付けてから、JSON ファイルとして保存します。 これにより、ローカルに格納された kubeconfig.json ファイルが生成されます。 (/mnt/c/users/<current user>/documents/Kube ディレクトリに kubeconfig.json として保存します)

### <a name="configure-the-kubernetes-cluster"></a>Kubernetes クラスターを構成する

ローカルの JSON ファイルが取得されたら、新しい WSL セッションで、次のコマンドを使用してクラスターを構成します。

```Bash  
    cd /mnt/c/users/<current user>/documents/Kube
    kubectl proxy
    kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml
    kubectl proxy
    set KUBECONFIG=”/mnt/c/users/<current user>/documents/Kube/kubeconfig.json”
    kubectl.exe config view
```

Kubernetes の構成設定が定義されます (下の出力を参照)。

![Alt text](media\azure-stack-solution-machine-learning\image62.png)

ローカル プロキシ サービスを開始します。

```Bash  
kubectl proxy
```

アドレス `https://localhost:8001` にある kubernetes クラスターの UI を参照します。

![Alt text](media\azure-stack-solution-machine-learning\image63.png)

これで、コンテナーをデプロイする場所と、オンプレミスで表示できるクラウド内に存在するコンテナーが用意されました。

![Alt text](media\azure-stack-solution-machine-learning\image64.png)

任意のコード エディターを使用して、**webservicename** とコンテナー **Image** および **Name** がデプロイに一致するように **iris_deployment.yaml** ファイル (/*mnt/c/users/<current user>/documents/Kube ディレクトリ*に配置されています) をカスタマイズします。

![Alt text](media\azure-stack-solution-machine-learning\image65.png)

コンテナー ポートを **5001** に設定します。

![Alt text](media\azure-stack-solution-machine-learning\image66.png)

次に、**imagePullSecret** を作成します。

### <a name="create-a-secret-in-the-cluster-that-holds-the-authorization-token"></a>承認トークンを保持するクラスター内にシークレットを作成する

Kubernetes クラスターは、**docker-registry** タイプのシークレットを使用して、プライベート イメージを取得するためのコンテナー レジストリに対して認証します。

このシークレットを作成し、それに **azuremlcr** という名前を付けます。

```Bash  
kubectl create secret docker-registry azuremlcr --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password= "<your-pword>" --docker-email=<your-email>
```

次のことを確認してください。

- **<your-registry-server>** は、Azure Container Registry の**ログイン サーバー**です。
- **<your-name>** は、Azure Container Registry の **ユーザー名**です。
- **<your-pword>** は、Azure Container Registry の **パスワード**です。 パスワードが引用符内にあることを確認してください。
- **<your-email>** は、コンテナーへの R/W アクセス権を持つユーザーです。
- この情報は、**[Access Keys] (アクセス キー)** の下の **Azure コンテナー** **レジストリ**にあります。
- これで、Docker の資格情報が **azuremlcr** という名前のシークレットとしてクラスター内に設定されました。

**iris_deployment.yaml** ファイル (/*mnt/c/users/<current user>/documents/Kube ディレクトリ*に配置されています) を保存します。

### <a name="create-the-kubernetes-container"></a>Kubernetes コンテナーを作成する

```Bash  
kubectl.exe create -f /mnt/c/users/<current  user>/documents/Kube/iris_deployment.yaml
```

    ![Alt text](media\azure-stack-solution-machine-learning\image67.png)

デプロイの状態を確認します。

```Bash  
Kubectl get deployments
```

    ![Alt text](media\azure-stack-solution-machine-learning\image68.png)

デプロイには少し時間がかかることがあります。

### <a name="configure-visual-studio-team-services-to-deploy-automatically"></a>自動的にデプロイするように Visual Studio Team Services を構成する

#### <a name="create-a-team-project"></a>チーム プロジェクトを作成する

1.  [プロジェクト コレクション管理者グループのメンバーシップ](https://docs.microsoft.com/vsts/organizations/security/set-project-collection-level-permissions?view=vsts)を確認します。 チーム プロジェクトを作成するには、**[Create new projects] (新しいプロジェクトの作成)** アクセス許可が **[許可する]** に設定されている必要があります。

2.  [Projects] (プロジェクト) ページから、**[新しいプロジェクト]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image69.png)

1.  プロジェクトに **HybridMLIris** という名前を付けます。

2.  その最初のソース管理の種類を **[Git]** として選択します。

3.  プロセスを選択し、**[作成]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image70.png)

### <a name="import-some-code--create-repository"></a>リポジトリの作成のためのコードをインポートする

YAML コード用の Git リポジトリが必要です。

#### <a name="add-user-to-the-git-repo"></a>Git リポジトリにユーザーを追加する

1.  既定のプロジェクト ダッシュボードから、[Generate Git credentials] (Git 資格情報の生成) を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image71.png)

1.  必要に応じてパスワードを入力し、Git 資格情報を保存します。

    ![Alt text](media\azure-stack-solution-machine-learning\image72.png)

1.  **[Initialize] (初期化)** ボタンを選択し、**README** ファイルを作成することによって、リポジトリを初期化します。

    ![Alt text](media\azure-stack-solution-machine-learning\image73.png)

#### <a name="clone-the-git-repository-locally-and-upload-the-code"></a>Git リポジトリをローカルに複製し、コードをアップロードします。 

1.  コンピューターの `c:\\users\\<User>\\source\\repos\\hybridMLIris` にディレクトリを作成し、リポジトリを複製します。

    ```Bash  
    sudo mkdir /mnt/c/users/<User>/source sudo mkdir /mnt/c/users/<User>/source/repos sudo mkdir /mnt/c/users/<User>/source/repos/hybridMLIris cd /mnt/c/users/<User>/source/repos/hybridMLIris sudo git clone  https://<yourvstssite>.visualstudio.com/HybridMLIris/_git/HybridMLIris
    ```

    ![Alt text](media\azure-stack-solution-machine-learning\image74.png)

1.  新しく複製されたリポジトリに移動します。

    ```Bash  
    ls
    cd ./HybridMLIris
    ```
    
    ![Alt text](media\azure-stack-solution-machine-learning\image75.png)

1.  **iris_deployment.yaml** ファイルをリポジトリにコピーします。

    ```Bash  
    cp /mnt/c/users/<User>/documents/Kube/iris_deployment.yaml  /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/iris_deployment.yaml
    ``` 

1.  GIT の変更をコミットする

    ```Bash  
    git add . git commit -m Added Deployment YAML git push
    ```

    ![Alt text](media\azure-stack-solution-machine-learning\image76.png)

### <a name="prepare-the-private-build-and-release-agent-for-vsts-integration"></a>VSTS 統合用にプライベート ビルドとリリース エージェントを準備する

#### <a name="prerequisites"></a>前提条件

VSTS は、サービス プリンシパルを使用して Azure Resource Manager に対して認証します。 Azure Stack サブスクリプションで VSTS がリソースをプロビジョニングできるようにするには、共同作成者の状態が必要です。\ **このような認証を有効にするための構成に必要な手順の概要を次に示します。**

1.  サービス プリンシパルを作成します。または、既存のものを使用することもできます。

2.  サービス プリンシパル用に認証キーを作成する必要があります。

3.  SPN を共同作成者ロールに含めることができるように、ロールベースのアクセス制御を使用して、Azure Stack サブスクリプションを検証する必要があります。

4.  Azure Stack エンドポイントと SPN 情報を使用して、VSTS での新しいサービス定義を作成する必要があります。

#### <a name="service-principal-creation"></a>サービス プリンシパルの作成

[サービス プリンシパルの作成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)の手順を参照してサービス プリンシパルを作成し、アプリケーションの種類として Web アプリ/API を選択します。

**アクセス キーの作成**

サービス プリンシパルには認証用のキーが必要です。このセクションの手順に従って、キーを生成してください。

1.  Azure Active Directory の **[アプリの登録]** から、アプリケーションを選択します。

    ![アプリケーションを選択する](media\azure-stack-solution-machine-learning\image77.png)

1.  **[アプリケーション ID] の値をメモします。この値は、VSTS でサービス エンドポイントを構成するときに使用されます。**

    ![Alt text](media\azure-stack-solution-machine-learning\image78.png)

1.  認証キーを生成するには、**[設定]** を選択します。

    ![設定を選択](media\azure-stack-solution-machine-learning\image79.png)

1.  **[キー]** を選択します。

    ![[キー] を選択する](media\azure-stack-solution-machine-learning\image80.png)

1.  キーの説明を入力し、キーの期間を指定します。 操作が完了したら、**[保存]** をクリックします。

    ![キーを保存する](media\azure-stack-solution-machine-learning\image81.png)

キーを保存すると、キーの値が表示されます。 この値は後で必要なため、コピーしておきます。 **キー値**はアプリケーション ID と共に、アプリケーションとしてログインするために必要です。 このキー値を、アプリケーションからの取得が可能な場所に格納します。

![Alt text](media\azure-stack-solution-machine-learning\image82.png)

#### <a name="get-tenant-id"></a>テナント ID を取得する

サービス エンドポイント構成の一部として、VSTS には、Azure Stack スタンプがデプロイされた先の AAD ディレクトリに対応する**テナント ID** が必要です。 このセクションの手順に従って、テナント ID を取得してください。

1.  **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] を選択する](media\azure-stack-solution-machine-learning\image83.png)

1.  テナント ID を取得するには、Azure AD テナントの **[プロパティ]** を選択します。

    ![Azure AD のプロパティを選択する](media\azure-stack-solution-machine-learning\image84.png)

1.  **ディレクトリ ID** をコピーします。 この値がテナント ID です。

    ![テナント ID](media\azure-stack-solution-machine-learning\image85.png)

Azure Stack サブスクリプション内にリソースをデプロイするサービス プリンシパル権限を付与する

サブスクリプション内のリソースにアクセスするには、アプリケーションをロールに割り当てます。 アプリケーションにとって適切なアクセス許可を表すのはどのロールであるかを判断します。 利用可能なロールについては、「[RBAC: 組み込みのロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)」を参照してください。

スコープをサブスクリプション、リソース グループ、またはリソースのレベルに設定します。 アクセス許可は、スコープの下位レベルに継承されます。 たとえば、アプリケーションをリソース グループの閲覧者ロールに追加すると、そのアプリケーションはリソース グループとそれに含まれているすべてのリソースを読み取ることができます。

1.  アプリケーションを割り当てるスコープの目的のレベルに移動します。 たとえば、サブスクリプション スコープでロールを割り当てるには、**[サブスクリプション]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image86.jpeg)

1.  アプリケーションを割り当てる**サブスクリプション** (リソース グループまたはリソース) を選択します。

    ![割り当てのためのサブスクリプションの選択](media\azure-stack-solution-machine-learning\image87.png)

1.  **[アクセス制御 (IAM)]** を選択します。

    ![アクセスの選択](media\azure-stack-solution-machine-learning\image88.png)

1.  **[追加]** を選択します。

    ![select add](media\azure-stack-solution-machine-learning\image89.png)

1.  アプリケーションを割り当てるロールを選択します。 次の図は、**所有者**ロールを示しています。

    ![Alt text](media\azure-stack-solution-machine-learning\image90.png)

1.  既定では、Azure Active Directory アプリケーションは、使用可能なオプションに表示されません。 アプリケーションを見つけるには、検索フィールドで**名前を指定し**、それを選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image91.png)

1.  **[保存]** を選択して、ロールの割り当てを完了します。 アプリケーションは、そのスコープのロールに割り当てられたユーザーの一覧に表示されます。

### <a name="role-based-access-control"></a>ロールベースのアクセス制御

Azure のロールベースのアクセス制御 (RBAC) では、Azure と Azure Stack のアクセスをきめ細かく管理できます。 RBAC を使用すると、ユーザーが自分のジョブを実行するために必要な量のアクセスだけを付与できます。 ロールベースのアクセス制御の詳細については、[Azure サブスクリプション リソースへのアクセスの管理](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json)に関するページを参照してください。

**VSTS エージェント プール**

各エージェントを個別に管理する代わりに、エージェントが**エージェント プール**に構成されます。 エージェント プールでは、そのプール内のすべてのエージェントに対して共有境界が定義されています。 VSTS では、エージェント プールはスコープが VSTS アカウントに設定されるため、チーム プロジェクトにまたがって共有できます。 VSTS エージェント プールを作成する方法の詳細およびチュートリアルについては、[エージェント プールとキューの作成](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts)に関するページを参照してください。

**Azure Stack の個人用アクセス トークン (PAT) を追加する**

 -  VSTS アカウントにログオンし、**アカウント プロファイル**名を選択します。

 -  **[セキュリティの管理]** を選択して、トークン作成ページにアクセスします。

![Alt text](media\azure-stack-solution-machine-learning\image92.png)

![Alt text](media\azure-stack-solution-machine-learning\image93.jpeg)

![Alt text](media\azure-stack-solution-machine-learning\image94.jpeg)

> [!Note]  
> トークン情報を取得してください。 この情報は、この画面を閉じると再び表示されることはありません。

1.  **トークン**をコピーします。

    ![Alt text](media\azure-stack-solution-machine-learning\image95.png)

#### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Azure Stack でホストされているビルド サーバーに VSTS ビルド エージェントをインストールする

1.  Azure Stack ホストにデプロイされた**ビルド サーバー**に接続します。

    > [!Note]  
    > Azure Stack でホストされているビルド サーバーがパブリック インターネットからアクセス可能であることを確認してください。 そうでない場合は、Azure Stack オペレーターと協力してアクセスを取得します。

    ```Bash  
    ssh <user>@<buildserver.publicip>
    ```

2.  Ubuntu ビルド サーバーを最新バージョン (18.04) にアップグレードします。

    ```Bash  
    sudo su
    apt-get update
    apt-get upgrade
    apt-get dist-upgrade
    apt-get autoremove
    do-release-upgrade -d
    ```

    > [!Note]  
    > この操作には少し時間がかかります。

2.  ビルド サーバーの前提条件のアプリケーションをインストールします。 Ubuntu ベースのビルド サーバーの bash シェルから、次のコマンドを実行します。

    ```Bash  
    wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.asc.gpg
    sudo mv microsoft.asc.gpg /etc/apt/trusted.gpg.d/
    wget -q https://packages.microsoft.com/config/ubuntu/18.04/prod.list 
    sudo mv prod.list /etc/apt/sources.list.d/microsoft-prod.list
    sudo chown root:root /etc/apt/trusted.gpg.d/microsoft.asc.gpg
    sudo chown root:root /etc/apt/sources.list.d/microsoft-prod.list
    sudo apt-get install apt-transport-https
    sudo apt-get update
    sudo apt-get install liblttng-ust0 libcurl3 libssl1.0.0 curl lsb-release libkrb5-3 zlib1g libicu60 aspnetcore-runtime-2.1 dotnet-sdk-2.1
    wget https://github.com/PowerShell/PowerShell/releases/download/v6.1.0-preview.3/powershell-preview_6.1.0-preview.3-1.ubuntu.18.04_amd64.deb
    sudo dpkg -i powershell-preview_6.1.0-preview.3-1.ubuntu.18.04_amd64.deb
    sudo apt-get install -f
    AZ_REPO=$(lsb_release -cs)
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ_REPO main" | \
        sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add –
    sudo apt-get update && sudo apt-get install azure-cli
    ```

2.  **個人用アクセス トークン (PAT)** を使用してビルド エージェントをダウンロードし、サービスとしてデプロイした後、VM 管理者アカウントとして実行します。

    ![Alt text](media\azure-stack-solution-machine-learning\image96.png)

    ```Bash  
        cd \home\<user>
        sudo mkdir myagent && cd myagent
        wget https://vstsagentpackage.azureedge.net/agent/2.134.2/vsts-agent-linux-x64-2.134.2.tar.gz
        sudo tar zxvf vsts-agent-linux-x64-2.134.2.tar.gz
    ```

2.  抽出されたビルド エージェント フォルダーに移動します。 次のコードを実行します。

    ```Bash  
        cd ..
        sudo chmod -R 777 myagent
        cd myagent
        ./config.sh
    ```

    ![Alt text](media\azure-stack-solution-machine-learning\image97.png)

2.  **./config.sh** が完了したら、次のコードを実行してサーバー ブート時にサービスを有効にし、そのサービスを開始します。

    ```Bash  
    sudo ./svc.sh install
    sudo ./svc.sh start
    ```

エージェントが VSTS フォルダーに表示されるようになりました。

#### <a name="endpoint-creation-permissions"></a>エンドポイント作成のアクセス許可

VSTO ビルドが Azure Service アプリをスタックにデプロイできるように、ユーザーがエンドポイントを作成できます。 VSTS がビルド エージェントに接続された後、エージェントは Azure Stack に接続されます。

![Alt text](media\azure-stack-solution-machine-learning\image98.png)

1.  **[設定]** メニューで **[セキュリティ]** を選択します。

2.  左側の **[VSTS グループ]** の一覧で、**[エンドポイント作成者]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image99.png)

3.  **[メンバー]** タブで、**[+ 追加]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image100.png)

1.  **ユーザー名**を入力し、一覧からそのユーザー名を選択します。

2.  **[変更の保存]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image101.png)

3.  左側の **[VSTS グループ]** の一覧で、**[エンドポイント管理者]** を選択します。

4.  **[メンバー]** タブで、**[+ 追加]** を選択します。

5.  **ユーザー名**を入力し、一覧からそのユーザーを選択します。

6.  **[変更の保存]** を選択します。

    ![buchatech](media\azure-stack-solution-machine-learning\image102.jpeg)

    Azure Stack 内のビルド エージェントに VSTS から指示が届き、Azure Stack と通信するためのエンドポイント情報が伝達されます。

    これで、VSTS と Azure Stack を接続できるようになりました。

    ![Alt text](media\azure-stack-solution-machine-learning\image103.png)

### <a name="configure-build-and-release-definitions"></a>ビルドおよびリリース定義を構成する

これで、接続が確立されたので、作成された Azure エンドポイント、AKS、および Azure Container Registry をビルドおよびリリース定義に手動でマッピングします。

#### <a name="create-the-build-definition-for-the-yaml-code"></a>YAML コードのビルド定義を作成する

1.  ビルドおよびリリース ハブの下の [Builds] (ビルド) セクションを選択し、新しい定義を作成します。

    ![Alt text](media\azure-stack-solution-machine-learning\image104.png)

1.  [VSTS Git] を選択し、以前に作成されたリポジトリを選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image105.png)

1.  テンプレートとして空のパイプラインを選択する

    ![Alt text](media\azure-stack-solution-machine-learning\image106.png)

1.  ビルドに **Copy Artifact** という名前を付け、エージェント キューの Azure Stack ビルド サーバーを選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image107.png)

1.  プロセスのフェーズ 1 を選択し、その名前を **Copy Artifact** に変更してから、そのフェーズに**タスクを追加します**。

    ![Alt text](media\azure-stack-solution-machine-learning\image108.png)

1.  **[Utility] (ユーティリティ)** 一覧から **[Publish Build Artifacts] (ビルド アーティファクトの発行)** を選択し、**[追加]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image109.png)

1.  **[path to publish] (発行するパス)** を選択し、**iris_deployment.yaml** ファイルを選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image110.png)

1.  アーティファクトに **iris_deployment** という名前を付け、発行場所を **Visual Studio Team Services/TFS** になるように選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image111.png)

1.  **[保存してキューに登録]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image112.png)

1.  ビルド ID を選択することによって、ビルドの状態を確認します。

    ![Alt text](media\azure-stack-solution-machine-learning\image113.png)

成功すると、次のように表示されます。

![Alt text](media\azure-stack-solution-machine-learning\image114.png)

#### <a name="create-the-release-definition-for-the-yaml-code"></a>YAML コードのリリース定義を作成する

1.  ビルドおよびリリース ハブの下の [Releases] (リリース) セクションを選択し、新しい定義を作成します。

    ![Alt text](media\azure-stack-solution-machine-learning\image115.png)

1.  テンプレートとして空のパイプラインを選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image106.png)

1.  環境に Azure Stack という名前を付けます。

    ![Alt text](media\azure-stack-solution-machine-learning\image116.png)

1.  **[Artifacts] (アーティファクト)** および **[+ 追加]** を選択することによって、新しいアーティファクトを追加します。

2.  ソースの種類として [Build] (ビルド) を、プロジェクトとして **[HybridMLIris]** を選択します。

3.  次に、ソース用に以前に作成されたビルド定義を選択します。

4.  その後、**[追加]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image117.png)

1.  環境から Azure Stack を選択し、Azure Stack に新しいタスクを追加する

    ![Alt text](media\azure-stack-solution-machine-learning\image118.png)


1.  エージェント フェーズで、エージェント キューを Azure Stack でホストされているビルド サーバーに設定します。

    ![Alt text](media\azure-stack-solution-machine-learning\image119.png)

1.  このフェーズに新しいタスクを追加し、[デプロイ] の下の [Kubernetes へのデプロイ] タスクを選択して [追加] を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image120.png)


1.  これに **Kubectl Apply** (既定の名前) という名前を付け、適用コマンドを選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image121.png)

    次に、新しい Kubernetes サービス接続を作成します。

#### <a name="create-kubernetes-service-endpoint"></a>Kubernetes サービス エンドポイントを作成する

1.  Kubernetes サービス接続の下の **[+ 新規]** ボタンを選択し、一覧から **[Kubernetes]** を選択します。 このエンドポイントを使用して、**VSTS** と **Azure Container Service (AKS)** を接続できます。

2.  **[接続名]**: 接続名を指定します。

3.  **[サーバー URL]**: http://{API サーバー アドレス} という形式でコンテナー サービスのアドレスを指定します。

4.  **[Kubeconfig]**: Kubeconfig 値を取得するには、管理者特権で起動されたコマンド プロンプトで次の Azure コマンドを実行します。

    > [!Important]  
    > 次の手順を実行するには、この CLI ウィンドウを使用します。

6.  CLI ウィンドウで Azure にログインします。 az login の詳細については、[こちら](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)を参照してください。

7.  コマンド プロンプトに、次のコマンドを入力します。

    ```CLI
        az login
    ```

10. このコマンドは、ブラウザーの <https://aka.ms/devicelogin> で使用するコードを返します。

11. ブラウザー内の <https://aka.ms/devicelogin> に移動します。 メッセージが表示されたら、CLI で受信されたコードをブラウザーに入力します。

    ![Kubernetes サービス エンドポイント](media\azure-stack-solution-machine-learning\image122.png)

1.  Kubernetes クラスターのアクセス資格情報を取得するには、コマンド プロンプトで次のコマンドを入力します。

### <a name="azure-ml-workbench-cli"></a>Azure ML Workbench CLI

az aks get-credentials resource-group <yourResourceGroup> name <yourazurecontainerservice>

![Kubernetes サービス エンドポイント](media\azure-stack-solution-machine-learning\image123.png)

1.  ホーム ディレクトリの下の **.kube** フォルダー (たとえば、C:\\Users\\<user>\\Documents\\Kube) に移動します。

2.  **config** ファイルの内容をコピーし、それを [Kubernetes Connection] (Kubernetes 接続) ウィンドウに貼り付けます。 **[OK]** ボタンを選択します。

    ![Kubernetes サービス エンドポイント](media\azure-stack-solution-machine-learning\image124.png)
    

3.  Kubernetes エンドポイントが作成されて選択されたら、[構成ファイルを使用する] チェックボックスをオンにして構成ファイルを追加します。 次に、リンクされたアーティファクト内の iris_deployment.yaml ファイルを参照します。

    ![Alt text](media\azure-stack-solution-machine-learning\image125.png)

    ![Alt text](media\azure-stack-solution-machine-learning\image126.png)

4.  リリース定義を保存します。

#### <a name="check-the-status-of-the-release-definition"></a>リリース定義の状態を確認します。 

保存されたら、そのリリース定義で VSTS が自動的にリリースを開始します。

WSL コマンド プロンプトで簡単なコマンドを実行し、Kubernetes UI をチェックすることによって、デプロイの状態を確認します。

```Bash  
kubectl get deployments
```

デプロイ プロセス中、出力は次のようになります。

![Alt text](media\azure-stack-solution-machine-learning\image127.png)

```Bash  
kubectl proxy
```

kubernetes UI が実行されたら、[**https://localhost:8001/**](https://localhost:8001/) のデプロイを参照してから、**[ワークロード] -> [Replica Sets] (レプリカ セット)** に移動します。

![Alt text](media\azure-stack-solution-machine-learning\image128.png)

### <a name="deploy-the-yaml-service"></a>YAML サービスをデプロイする

#### <a name="upload-the-irisserviceyaml-to-the-repository-and-sync-changes"></a>iris_service.yaml をリポジトリにアップロードし、変更を同期する

1.  新しく複製されたリポジトリに移動します。

    ```Bash  
    cd /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/
    ```

    ![Alt text](media\azure-stack-solution-machine-learning\image75.png)

1.  **iris_service.yaml** ファイルをリポジトリにコピーします。

    ```Bash  
    cp /mnt/c/users/<User>/documents/Kube/iris_service.yaml  /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/iris_service.yaml
    ```

1.  GIT の変更をコミットする

    ```Bash  
    git add .
    git commit -m “Added Service YAML” 
    git push
    ```

![Alt text](media\azure-stack-solution-machine-learning\image129.png)

#### <a name="update-the-build-definition-for-the-yaml-code"></a>YAML コードのビルド定義を更新する

1.  ビルドおよびリリース ハブの下の [Builds] (ビルド) セクションを選択し、以前に作成された定義を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image130.png)

2.  [編集] ボタンを選択して定義を編集します。

    ![Alt text](media\azure-stack-solution-machine-learning\image131.png)

3.  このフェーズに**タスクを追加します**。 **[Utility] (ユーティリティ)** 一覧から **[Publish Build Artifacts] (ビルド アーティファクトの発行)** を選択し、**[追加]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image108.png)

4.  これに **Kubectl Apply** (既定の名前) という名前を付け、適用コマンドを選択します。



#### <a name="update-the-release-definition-for-the-yaml-code"></a>YAML コードのリリース定義を更新する

1.  ビルドおよびリリース ハブの下の [Releases] (リリース) セクションを選択し、以前に作成されたリリース定義を選択します。 次に、[編集] リンクを選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image132.png)

1.  環境 **Azure Stack** を選択し、Azure Stack に新しいタスクを追加します。

    ![Alt text](media\azure-stack-solution-machine-learning\image133.png)

1.  このフェーズに**新しいタスク**を追加し、**[デプロイ]** の下の **[Kubernetes へのデプロイ]** タスクを選択して **[追加]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image134.png)

1.  これに **Kubectl Apply** (既定の名前) という名前を付け、適用コマンドを選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image109.png)

1.  Kubernates サービス接続を以前に作成された Azure Stack 接続に設定してから、**[構成ファイルを使用する]** チェックボックスをオンにして構成ファイルを追加します。 リンクされたアーティファクト内の iris_service.yaml ファイルを参照します。

    ![Alt text](media\azure-stack-solution-machine-learning\image135.png)


    ![Alt text](media\azure-stack-solution-machine-learning\image136.png)

1.  リリース定義を保存します。

#### <a name="check-the-status-of-the-release-definition"></a>リリース定義の状態を確認する

保存されたら、そのリリース定義で VSTS が自動的にリリースを開始します。

WSL コマンド プロンプトで簡単なコマンドを実行し、Kubernetes UI をチェックすることによって、デプロイの状態を確認します。

```Bash  
kubectl get deployments
```

デプロイ プロセス中、出力は次のようになります。

![Alt text](media\azure-stack-solution-machine-learning\image127.png)


```Bash  
kubectl proxy
```

kubernetes UI が実行されたら、[**https://localhost:8001/**](https://localhost:8001/) のデプロイを参照してから、**[ワークロード] -> [Replica Sets] (レプリカ セット)** に移動します。

![Alt text](media\azure-stack-solution-machine-learning\image137.png)


### <a name="kubernetes-scoring-and-validation"></a>Kubernetes のスコア付けと検証

Kubernetes UI を起動する

```Bash  
kubectl proxy
```

Kubernetes UI を参照してから、**[デプロイメント]** -> **[Iris-Deployment] (Iris デプロイメント)** -> **[New Replica Set] (新しいレプリカ セット)** -> **[Iris-Deployment-xxxxxxxxx] (Iris デプロイメント-xxxxxxxxx)** (ここで、x はデプロイ ID) に移動します。

![Alt text](media\azure-stack-solution-machine-learning\image138.png)

次に、**[サービス]** に移動し、サービスの**外部エンドポイント**を選択して、それが機能していることを検証します。

![Alt text](media\azure-stack-solution-machine-learning\image139.png)

次のような検証メッセージが表示されます。

![Alt text](media\azure-stack-solution-machine-learning\image140.png)

#### <a name="create-azure-stack-scoring-function-app-in-the-azure-stack-portal"></a>Azure Stack ポータルで Azure Stack スコア付け関数アプリを作成する

各関数の実行をホストするには、関数アプリが必要です。 関数アプリを使用すると、ロジック ユニットとしての関数のグループ化が可能になり、リソースの管理、デプロイ、および共有が容易になります。

1.  Azure Stack ユーザー ポータルから、左上隅にある **[+ 新規]** ボタンを選択し、**[Web + モバイル]** >**[Function App] (関数アプリ)** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image141.png)

1.  関数に **data-functions** という名前を付け、それを残りの Machine Learning コンテンツと共に同じリソース グループに配置します。 ツールで消費用の新しいアプリ サービス プランが自動作成され、アプリ ストレージ用の以前に作成されたストレージ アカウントが使用されるようにします。

    ![新しい関数アプリの設定を定義する](media\azure-stack-solution-machine-learning\image142.png)

1.  **[作成]** を選択して、関数アプリをプロビジョニングおよびデプロイします。

2.  ポータルの右上隅の通知アイコンを選択し、"**デプロイメントに成功しました**" というメッセージが表示されるまで待ちます。

    ![新しい関数アプリの設定を定義する](media\azure-stack-solution-machine-learning\image143.png)

1.  新しい関数アプリを表示するには、**[リソースに移動]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image144.png)

1.  **[関数]**、**[+New Function] (+ 新しい関数)** ボタンの順に選択することによって、新しい関数を作成します。

    ![Alt text](media\azure-stack-solution-machine-learning\image145.png)

1.  HTTP トリガーを選択する

    ![Alt text](media\azure-stack-solution-machine-learning\image146.png)

1.  言語として **[C]\#** を選択し、関数に **clean-score-data** という名前を付けて、承認レベルを **[匿名]** に設定します。

    ![Alt text](media\azure-stack-solution-machine-learning\image147.png)

1.  clean-score-data のコード例の内容をコピーし、それをこの関数に貼り付けます。

    ![Alt text](media\azure-stack-solution-machine-learning\image148.png)

#### <a name="use-postman-to-validate-functions"></a>Postman を使用して関数を検証する

Kbernetes と関数が正しく設定されていることを確認するには、無料のアプリ Postman を使用してスキーマと関数をテストおよび検証できます。 このプロセスを開始するには、まず Kubernetes インスタンスからいくつかの情報を収集する必要があります。

1.  Kubernetes UI を参照してから、**[デプロイメント]** -> **[Iris-Deployment] (Iris デプロイメント)** -> **[New Replica Set] (新しいレプリカ セット)** -> **[Iris-Deployment-xxxxxxxxx] (Iris デプロイメント-xxxxxxxxx)** (ここで、x はデプロイ ID) に移動します。

    ![Alt text](media\azure-stack-solution-machine-learning\image138.png)

1.  次に、**[サービス]** に移動し、**外部エンドポイント**をコピーします。

    ![Alt text](media\azure-stack-solution-machine-learning\image149.png)

1.  必要に応じて、[ここで](https://www.getpostman.com/apps) Postman アプリをダウンロードしてインストールします。

2.  Postman アプリにサインインし、[新しいファイル] ダイアログを閉じます。

    ![Alt text](media\azure-stack-solution-machine-learning\image150.png)

1.  Postman アプリ内から、[POST] を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image151.png)

1.  下に示すように、**外部エンドポイント** URL を Postman アプリの **[Request URL]** の下に貼り付け、**\\score** を URL の最後に追加します。

    ![Alt text](media\azure-stack-solution-machine-learning\image152.png)

1.  **[Body]** タブ、データ型として **[raw]**、**[JSON]** の順に選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image153.png)

1.  Web ブラウザーから、**外部エンドポイント**に移動します。 URL **/swagger.json** に次を追加します。これにより、セットアップのテストのために Services Swagger ファイルが使用されます。

    ![Alt text](media\azure-stack-solution-machine-learning\image154.png)

1.  **Swagger.JSON** ファイルにリストされている例をコピーします。

2.  Postman アプリで、この例を [Post] の本文に貼り付け、**[Send]** を選択します。 これにより、下に示すような値が返されます。

    ![Alt text](media\azure-stack-solution-machine-learning\image155.png)

## <a name="step-7-create-an-azure-stack-storage-account-and-storage-queue"></a>手順 7: Azure Stack ストレージ アカウントとストレージ キューを作成する

データのための Azure Stack ストレージ アカウントとストレージ キューを作成します。

1.  Azure Stack ユーザー ポータルにサインインします。 (各 Azure Stack には一意のポータル URL があります)

2.  Azure Stack ユーザー ポータルで、左側にあるメニューを展開してサービスのメニューを開き、**[すべてのサービス]** を選択します。 **[ストレージ]** まで下へスクロールし、**[ストレージ アカウント]** を選択します。 **[ストレージ アカウント]** ウィンドウで、**[追加]** を選択します。

3.  ストレージ アカウントの名前を入力します。

4.  ストレージ アカウントのレプリケーション オプション (**[LRS]**) を選択します。

5.  新しいリソース グループを指定するか、既定のリソース グループを選択します。

6.  ストレージ アカウントの場所として **[ローカル]** を選択します。

7.  **[作成]** を選択してストレージ アカウントを作成します。

    ![Alt text](media\azure-stack-solution-machine-learning\image156.png)

1.  最近作成されたストレージ アカウントを選択します。

2.  **[キュー]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image157.png)

1.  **[+ キュー]** を選択してキューに名前を付け、**[OK]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image158.png)

1.  ストレージ キューの **[接続文字列]** を取得し、それをコピーします。

    ![Alt text](media\azure-stack-solution-machine-learning\image159.png)

1.  Azure Function App に移動し、**[アプリケーションの設定]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image160.png)

1.  関数アプリの [アプリケーションの設定] 内から、[アプリケーションの設定] まで下へスクロールし、**[+ Add new setting] (+ 新しい設定の追加)** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image161.png)

1.  ストレージ アカウントの名前を **[名前]** フィールドに入力し、最後に "_STORAGE" を追加します。

これにより、アプリケーションは、これがストレージ アカウント エンドポイントであることを理解できます。

1.  次に、接続文字列を **[値]** フィールドに貼り付けます。

    ![Alt text](media\azure-stack-solution-machine-learning\image162.png)

1.  [アプリケーションの設定] の一番上までスクロールし、**[保存]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image163.png)

### <a name="update-the-scoring-function-to-use-storage-queue"></a>ストレージ キューを使用するようにスコア付け関数を更新する

1.  この関数で **[統合]** を選択し、**[GET]** オプションをオフにします。

2.  **[保存]** を選択します。

3.  次に、[出力] から **[+New Output] (+ 新しい出力)** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image164.png)

1.  次に、**[Azure Queue Storage]** を選択し、**[選択]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image165.png)

1.  **[キュー名]** を以前に作成されたストレージ キューに更新してから、**[Storage Account Connection] (ストレージ アカウント接続)** を以前に作成されたストレージ アカウント接続に設定し、**[保存]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image166.png)

## <a name="step-8-create-a-function-to-handle-clean-data"></a>手順 8: クリーン データを処理する関数を作成する

クリーン データを Azure Stack から Azure に移動する新しい Azure Stack 関数を作成します。

1.  **[関数]**、**[+New Function] (+ 新しい関数)** ボタンの順に選択することによって、新しい関数を作成します。

    ![Alt text](media\azure-stack-solution-machine-learning\image167.png)

1.  **[タイマー トリガー]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image168.png)

1.  言語として **[C]\#** を選択し、関数に **upload-to-azure** という名前を付けて、スケジュールを **0 0 \*/1 \* \* \*** (CRON 表記で 1 時間に 1 回) に設定します。

    ![Alt text](media\azure-stack-solution-machine-learning\image169.png)

### <a name="get-the-connection-string-to-the-azure-hosted-storage-account"></a>Azure でホストされているストレージ アカウントへの接続文字列を取得する

1.  <https://portal.azure.com> を参照してから、以前に作成された **[Azure Storage アカウント]** を選択します。

2.  **[Access Keys] (アクセス キー)** を選択し、ストレージ アカウントの **[接続文字列]** をコピーします。

    ![Alt text](media\azure-stack-solution-machine-learning\image170.png)

### <a name="update-the-upload-to-azure-function-to-use-the-azure-hosted-storage"></a>Azure でホストされているストレージを使用するように upload-to-azure 関数を更新する

1.  Azure Function App に移動し、**[アプリケーションの設定]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image171.png)

1.  関数アプリの [アプリケーションの設定] 内から、[アプリケーションの設定] まで下へスクロールし、**[+ Add new setting] (+ 新しい設定の追加)** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image172.png)

1.  ストレージ アカウントの名前を **[名前]** フィールドに入力し、最後に "_STORAGE" を追加します。

これにより、アプリケーションは、これがストレージ アカウント エンドポイントであることを理解できます。

1.  次に、Azure でホストされているストレージ アカウント接続文字列を **[値]** フィールドに貼り付けます。

    ![Alt text](media\azure-stack-solution-machine-learning\image173.png)

1.  [アプリケーションの設定] の一番上までスクロールし、**[保存]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image174.png)

1.  元の **upload-to-azure** 関数に移動します。

2.  この関数で **[統合]** を選択します。

3.  次に、[出力] から **[+New Output] (+ 新しい出力)** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image175.png)

1.  次に、**[Azure Blob Storage]** を選択し、**[選択]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image176.png)

1.  **[パス]** を **uploadeddata/{rand-guid}.txt** という形式の以前に作成されたストレージ コンテナーに更新してから、**[Storage Account Connection] (ストレージ アカウント接続)** を以前に作成された Azure へのストレージ アカウント接続に設定し、**[保存]** を選択します。

    ![Alt text](media\azure-stack-solution-machine-learning\image177.png)

1.  **upload-to-azure** のコード例の内容をコピーし、それをこの関数に貼り付けます。

2.  必要に応じて、ストレージ アカウント接続文字列を指すように変更します。

3.  保存し、コードを実行します。

    ![Alt text](media\azure-stack-solution-machine-learning\image178.png)

1.  Azure でホストされているストレージ アカウントをチェックして、データが Azure からクラウドまで解析されていることを確認します。成功すると、次のように表示されます。

    ![Alt text](media\azure-stack-solution-machine-learning\image179.png)

データは、Azure Stack でホストされている Kubernetes Machine Learning によって機密データがサニタイズされ、Azure Stack でホストされている関数アプリ経由でオンプレミスの Azure Stack から Azure パブリック クラウドにアップロードされます。そのため、エッジのシナリオや接続が切断されたシナリオでのアップロード用にこのデータをステージングできます。

## <a name="next-steps"></a>次の手順

 - Azure のクラウド パターンの詳細については、「[Cloud Design Pattern (クラウド設計パターン)](https://docs.microsoft.com/azure/architecture/patterns)」を参照してください。
