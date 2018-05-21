---
title: Azure および Azure Stack へのアプリのデプロイ | Microsoft Docs
description: ハイブリッド CI/CD パイプラインを使用して、アプリを Azure と Azure Stack にデプロイする方法を説明します。
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
ms.date: 05/15/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 483122f5d2e39c8595d0f28a6b937772c4ea2e50
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2018
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>チュートリアル: Azure と Azure Stack にアプリをデプロイする

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

ハイブリッド継続的インテグレーション/継続的デリバリー (CI/CD) パイプラインによって、アプリのビルド、テスト、および複数のクラウドへのデプロイを行うことができます。  このチュートリアルでは、以下を実現するためのサンプル環境を構築します。
 
> [!div class="checklist"]
> * コード コミットに基づいて、Visual Studio Team Services (VSTS) リポジトリに対して新しいビルドを開始します。
> * ユーザー受け入れテストのため、新しくビルドされたコードをグローバル Azure に自動的にデプロイします。
> * コードがテストに合格したら、自動的に Azure Stack にデプロイします。

### <a name="about-the-hybrid-delivery-build-pipe"></a>ハイブリッド デリバリー ビルド パイプについて

アプリケーション デプロイの継続性、セキュリティ、および信頼性は、お客様の組織にとって非常に重要です。また、お客様の開発チームにも欠かせません。 ハイブリッド CI/CD パイプラインを使用することにより、オンプレミスの環境とパブリック クラウドの枠を越えてパイプラインを統合することができます。 お使いのアプリケーションを切り替えずに、場所を変更できます。

また、このアプローチにより、一貫性のある開発ツール セットを維持することもできます。 Azure パブリック クラウドとお使いのオンプレミス Azure Stack 環境の枠を越えてツールに一貫性を持たせさることで、CI/CD 開発プラクティスの実装が格段に容易になります。 Azure および Azure Stack にデプロイされたアプリとサービスは両方の環境で同じようにご利用いただけます。また、両方で同じコードを実行できるため、オンプレミスとパブリック クラウドの機能を活用できます。

各項目の詳細情報
 - [継続的インテグレーションとは](https://www.visualstudio.com/learn/what-is-continuous-integration/)
 - [継続的デリバリーとは](https://www.visualstudio.com/learn/what-is-continuous-delivery/)


## <a name="prerequisites"></a>前提条件

ハイブリッド CI/CD パイプラインを構築するには、いくつかのコンポーネントが必要になります。 この準備には、多少時間がかかることがあります。
 
 - 運用 Azure Stack は、Azure OEM/ハードウェア パートナーがデプロイできます。Azure Stack Development Kit (ASDK) は、すべてのユーザーがデプロイできます。 
 - また、Azure Stack オペレーターが、App Service のデプロイ、プランとオファーの作成、テナント サブスクリプションの作成、および Windows Server 2016 イメージの追加を行う必要もあります。

これらのコンポーネントの一部を既に持っている場合は、開始する前にそれらが要件を満たしていることを確認してください。

このトピックは、Azure と Azure Stack について一定の知識があることを前提にしています。 続行する前に知識を深めておく場合は、以下の各トピックから開始するようにしてください。


このチュートリアルは、Azure と Azure Stack について一定の知識があることを前提にしています。 

続行する前に知識を深めておく必要がある場合は、以下の各トピックから開始できます。
 - [Azure 入門](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure Stack の主要概念](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

 - Azure で [Web アプリ](https://docs.microsoft.com/azure/app-service/app-service-web-overview)を作成します。 後で使用するため、新しい Web アプリの URL を書き留めておきます。

Azure Stack
 - Azure Stack 統合システムを使用するか、以下のリンクされている Azure Stack Development Kit (ASDK) をデプロイします。
    - ASDK のデプロイに関する詳細な手順については、「[チュートリアル: インストーラーを使用して ASDK をデプロイする](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy)」を参照してください
    - PowerShell スクリプト [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ) を使用して、ASDK デプロイ後の手順の多くを自動化できます。

    > [!Note]  
    > ASDK のインストールが完了するまで 7 時間かかるため、それに応じて計画を立ててください。

 - [App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS サービスを Azure Stack にデプロイします。 
 - Azure Stack 環境内で[プラン/オファー](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview)を作成します。 
 - Azure Stack 環境内で[テナント サブスクリプション](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)を作成します。 
 - テナント サブスクリプション内で Web アプリを作成します。 後で使用できるように新しい Web アプリの URL を書き留めておきます。
 - 引き続きテナント サブスクリプション内で、VSTS 仮想マシンをデプロイします。
 - .NET 3.5 がインストールされた Windows Server 2016 VM が必要です。 この VM は、プライベート ビルド エージェントとして Azure Stack に構築されます。 

### <a name="developer-tools"></a>開発者用ツール

 - [VSTS ワークスペース](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)を作成します。 サインアップ プロセスによって **MyFirstProject** という名前のプロジェクトが作成されます。
 - [Visual Studio 2017 をインストール](https://docs.microsoft.com/visualstudio/install/install-visual-studio)して [VSTS にサインイン](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)します。
 - プロジェクトに接続し、[ローカルに複製](https://www.visualstudio.com/docs/git/gitquickstart)します。
 
 > [!Note]  
 > (Windows Server と SQL の) 実行および App Service のデプロイには、適切なイメージがシンジケート化された Azure Stack が必要です。
 
## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>Visual Studio Team Services 統合用にプライベート ビルドとリリース エージェントを準備する

### <a name="prerequisites"></a>前提条件

Visual Studio Team Services (VSTS) では、サービス プリンシパルを使用して、Azure Resource Manager に対する認証が行われます。 Azure Stack サブスクリプションで VSTS がリソースをプロビジョニングできるようにするには、共同作成者の状態が必要です。

このような認証を有効にするための構成に必要な手順の概要を次に示します。

1. サービス プリンシパルを作成します。または、既存のものを使用することもできます。
2. サービス プリンシパル用に認証キーを作成する必要があります。
3. SPN を共同作成者ロールに含めることができるように、ロールベースのアクセス制御を使用して、Azure Stack サブスクリプションを検証する必要があります。
4. Azure Stack エンドポイントと SPN 情報を使用して、VSTS での新しいサービス定義を作成する必要があります。

### <a name="service-principal-creation"></a>サービス プリンシパルの作成

[サービス プリンシパルの作成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)の手順を参照してサービス プリンシパルを作成し、アプリケーションの種類として Web アプリ/API を選択します。

### <a name="access-key-creation"></a>アクセス キーの作成

サービス プリンシパルには認証用のキーが必要です。このセクションの手順に従って、キーを生成してください。


1. Azure Active Directory の **[アプリの登録]** で、アプリケーションを選択します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2.  **[アプリケーション ID]** の値をメモします。 この値は、VSTS でサービス エンドポイントを構成するときに使用します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. 認証キーを生成するには、**[設定]** を選択します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. 認証キーを生成するには、**[キー]** を選択します。
 
    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. キーの説明を入力し、キーの期間を指定します。 操作が完了したら、**[保存]** をクリックします。
 
    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    キーを保存すると、キーの値が表示されます。 キーは後で取得できないため、この値をコピーしておきます。 **キー値**は、アプリケーションとしてログインする際にアプリケーション ID と共に入力します。 アプリケーションが取得できる場所にキー値を保存します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-tenant-id"></a>テナント ID を取得する

VSTS には、サービス エンドポイント構成の一部として、Azure Stack スタンプのデプロイ先の AAD ディレクトリに対応する**テナント ID** が必要です。 このセクションの手順に従って、テナント ID を取得してください。

1. **[Azure Active Directory]** を選択します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. テナント ID を取得するには、Azure AD テナントの **[プロパティ]** を選択します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_08.png)
 
3. **ディレクトリ ID** をコピーします。 この値がテナント ID です。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Azure Stack サブスクリプション内にリソースをデプロイするサービス プリンシパル権限を付与する 

サブスクリプション内のリソースにアクセスするには、アプリケーションをロールに割り当てる必要があります。 アプリケーションにとって適切なアクセス許可を表すのはどのロールであるかを判断します。 利用可能なロールについては、「[RBAC: 組み込みのロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)」を参照してください。

スコープは、サブスクリプション、リソース グループ、またはリソースのレベルで設定できます。 アクセス許可は、スコープの下位レベルに継承されます。 たとえば、アプリケーションをリソース グループの閲覧者ロールに追加すると、アプリケーションではリソース グループとそれに含まれているすべてのリソースを読み取ることができます。

1. アプリケーションを割り当てるスコープのレベルに移動します。 たとえば、サブスクリプション スコープでロールを割り当てるには、**[サブスクリプション]** を選択します。 リソース グループまたはリソースを選択することもできます。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. アプリケーションを割り当てる**サブスクリプション** (リソース グループまたはリソース) を選択します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. **[アクセス制御 (IAM)]** を選択します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. **[追加]** を選択します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. アプリケーションに割り当てるロールを選択します。 次の図は、**所有者**ロールを示しています。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. 既定では、Azure Active Directory アプリケーションは、使用可能なオプションに表示されません。 目的のアプリケーションを見つけるには、検索フィールドで**その名前を指定**する必要があります。 それを選択します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. **[保存]** を選択して、ロールの割り当てを完了します。 該当のスコープのロールに割り当てられたユーザーの一覧にアプリケーションが表示されます。

### <a name="role-based-access-control"></a>ロールベースのアクセス制御

Azure のロールベースのアクセス制御 (RBAC) では、Azure のアクセス権の詳細な管理を実現します。 RBAC を使用すると、職務に必要な範囲のアクセス権だけをユーザーに付与することができます。 ロールベースのアクセス制御の詳細については、[Azure サブスクリプション リソースへのアクセスの管理](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json)に関するページをご覧ください。

### <a name="vsts-agent-pools"></a>VSTS エージェント プール

各エージェントを個別に管理するのではなく、エージェントをエージェント プール内に整理します。 エージェント プールでは、そのプール内のすべてのエージェントに対して共有境界が定義されています。 VSTS では、エージェント プールのスコープは VSTS アカウントに制限されるため、チーム プロジェクトの枠を越えてエージェント プールを共有できます。 VSTS エージェント プールの作成方法に関する詳細とチュートリアルについては、[エージェント プールとキューの作成](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts)に関するページをご覧ください。

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Azure Stack の個人用アクセス トークン (PAT) を追加する

1. VSTS アカウントにサインインし、ご自分のアカウント プロファイル名を選択します。
2. **[セキュリティの管理]** を選択して、トークン作成ページにアクセスします。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. トークンをコピーします。
    
    > [!Note]  
    > トークン情報を取得してください。 この情報は、この画面を閉じると再び表示されることはありません。 
    
    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_19.png)
    

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Azure Stack でホストされているビルド サーバーに VSTS ビルド エージェントをインストールする

1.  Azure Stack ホストにデプロイしたビルド サーバーに接続します。

2.  ご自身の個人用アクセス トークン (PAT) を使用して、ビルド エージェントをダウンロードし、サービスとしてデプロイします。その後、VM 管理者アカウントとして実行します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. 抽出されたビルド エージェント フォルダーに移動します。 管理者特権でのコマンド プロンプトから **run.cmd** ファイルを実行します。 

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4.  run.cmd が完了すると、抽出された内容を含むフォルダーは次のようになります。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    VSTS フォルダーにエージェントが表示されています。

## <a name="endpoint-creation-permissions"></a>エンドポイント作成のアクセス許可

VSTO ビルドが Azure Service アプリをスタックにデプロイできるように、ユーザーがエンドポイントを作成できます。 VSTS がビルド エージェントに接続された後、エージェントは Azure Stack に接続されます。 

![代替テキスト](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. **[設定]** メニューで **[セキュリティ]** を選択します。
2. 左側の **[VSTS グループ]** の一覧で、**[エンドポイント作成者]** を選択します。 

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

3. **[メンバー]** タブで **[+ 追加]** を選択します。 

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

4. ユーザー名を入力し、一覧からそのユーザーを選択します。
5. **[変更を保存]** をクリックします。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

6. 左側の **[VSTS グループ]** の一覧で、**[エンドポイント管理者]** を選択します。
7. **[メンバー]** タブで **[+ 追加]** を選択します。
8. ユーザー名を入力し、一覧からそのユーザーを選択します。
9. **[変更を保存]** をクリックします。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

    Azure Stack 内のビルド エージェントに VSTS から指示が届き、Azure Stack と通信するためのエンドポイント情報が伝達されます。 
    
    これで、VSTS と Azure Stack を接続できるようになりました。

## <a name="develop-your-application"></a>自分のアプリケーションを開発する

ハイブリッド CI/CD を設定して、Web アプリを Azure および Azure Stack にデプロイし、両方のクラウドに変更を自動プッシュします。

> [!Note]  
> (Windows Server と SQL の) 実行および App Service のデプロイには、適切なイメージがシンジケート化された Azure Stack が必要です。 Azure Stack オペレーターの要件については、App Service のドキュメントの「前提条件」セクションをご覧ください。

### <a name="add-code-to-vsts-project"></a>コードを VSTS プロジェクトに追加する

1. Azure Stack でのプロジェクト作成権限が付与されているアカウントを使用して、Visual Studio にサインインします。

    ハイブリッド CI/CD は、アプリケーション コードとインフラストラクチャ コードの両方に適用できます。 [Azure Resource Manager テンプレート](https://azure.microsoft.com/resources/templates/)を Web アプリ コードのように VSTS から両方のクラウドに対して使用します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. 既定の Web アプリを作成して開くことで、**リポジトリを複製**します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>両方のクラウドで App Services の自己完結型 Web アプリ デプロイを作成する

1. **WebApplication.csproj** ファイルを編集します。**Runtimeidentifier** を選択し、`win10-x64.` を追加します。詳細については、[自己完結型のデプロイ](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)に関するドキュメントをドキュメントをご覧ください。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. チーム エクスプローラーを使用して、コードを VSTS にチェックインします。

3. アプリケーション コードが Visual Studio Team Services にチェックインされたことを確認します。 

### <a name="create-the-build-definition"></a>ビルド定義を作成する

1. VSTS にサインインして、ビルド定義を作成する機能を確認します。

2. **-r win10-x64** コードを追加します。 これは、.Net Core を使用して自己完結型のデプロイをトリガーするために必要です。 

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

3. ビルドを実行します。 [自己完結型のデプロイ ビルド](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)のプロセスにより、Azure および Azure Stack 上で実行できる成果物が発行されます。

### <a name="using-an-azure-hosted-agent"></a>Azure ホステッド エージェントを使用する

Web アプリをビルドおよびデプロイする場合、VSTS でホステッド エージェントを使用すると便利です。 Microsoft Azure によってメンテナンスやアップグレードが自動的に実施され、開発、テスト、デプロイについても、継続的に、かつ中断されることなく実行できます。

### <a name="manage-and-configure-the-continuous-deployment-cd-process"></a>継続的配置 (CD) プロセスを管理および構成する

Visual Studio Team Services (VSTS) および Team Foundation Server (TFS) が提供するパイプラインは自由に構成でき、管理性にも優れ、開発、ステージング、QA、運用など、さまざまな環境へのリリースに使用できます。また、特定のステージで承認を要求することもできます。

### <a name="create-release-definition"></a>リリース定義の作成

![代替テキスト](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

1. VSO の [ビルドとリリース] ページの **[リリース] タブ**で **\[+]** を選択して、新しいリリースを追加します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\102.png)

2. **Azure App Service の配置**テンプレートを適用します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\103.png)

3. [成果物の追加] プルダウン メニューで、Azure Cloud ビルド アプリに対して**成果物を追加**します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\104.png)

4. [パイプライン] タブで、環境の**フェーズ**、**タスク** リンクを選択し、Azure のクラウド環境の値を設定します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\105.png)

5. **環境名**を設定し、Azure クラウド エンドポイントに対して Azure **サブスクリプション**を選択します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\106.png)

6. [環境名] で、必要な **Azure アプリ サービス名**を設定します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\107.png)

7. Azure クラウドでホストされる環境のエージェント キューで、「**Hosted VS2017**」と入力します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\108.png)

8. [Azure App Service 配置] メニューで、環境に対して有効な**パッケージまたはフォルダー**を選択します。 **[OK]** を選択して、**フォルダーの場所**を選択します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\109.png)

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\110.png)

9. すべての変更を保存し、**リリース パイプライン**に戻ります。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\111.png)

10. Azure Stack アプリのビルドを選択して、**新しい成果物**を追加します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\112.png)

11. **Azure App Service の配置**を適用して、環境をもう 1 つ追加します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\113.png)

12. 新しい環境に **Azure Stack** という名前を付けます。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\114.png)

13. **[タスク]** タブで Azure Stack 環境を見つけます。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\115.png)

14. Azure Stack エンドポイントの**サブスクリプション**を選択します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\116.png)

15. **[App Service の名前]** として、Azure Stack Web アプリの名前を設定します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\117.png)

16. **Azure Stack エージェント**を選択します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\118.png)

17. [Azure App Service 配置] セクションで、環境に対して有効な**パッケージまたはフォルダー**を選択します。 [OK] を選択して、**フォルダーの場所**を選択します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\119.png)

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\120.png)

18. [変数] タブで、**VSTS_ARM_REST_IGNORE_SSL_ERRORS** という名前の変数を追加し、その値を **true** に設定し、スコープを **Azure Stack** に設定します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\121.png)

19. 両方の成果物で**継続的**配置トリガー アイコンを選択し、継続的配置トリガーを有効にします。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\122.png)

20. Azure Stack 環境で**配置前**条件アイコンを選択し、トリガーを**リリース後**に設定します。

21. すべての変更を保存します。

> [!Note]  
> タスクの一部の設定は、テンプレートからリリース定義を作成したときに、[環境変数](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables)として自動的に定義されている可能性があります。 こうした設定は、タスクの設定では変更できません。これらの設定を編集するには、親環境項目を選択する必要があります。

## <a name="create-a-release"></a>リリースを作成する

リリース定義への変更が完了したら、デプロイを開始できます。 これを行うには、リリース定義からリリースを作成します。 リリースは自動的に作成される場合があります。たとえば、継続的配置トリガーはリリース定義で設定されています。 つまり、ソース コードを変更すると、新しいビルドが起動され、これにより新しいリリースが起動されます。 ただし、このセクションでは、新しいリリースを手動で作成します。

1. **[リリース]** ドロップダウン リストを開いて、**[リリースの作成]** を選択します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\200.png)
 
2. リリースの説明を入力し、正しい成果物が選択されていることを確認して、**[作成]** を選択します。 しばらくすると、新しいリリースが作成されたことを示すバナーが表示されます。 リンク (リリースの名前) を選択します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\201.png)
 
3. リリースの概要ページが開き、リリースの詳細が表示されます。 **[環境]** セクションで、"QA" 環境のデプロイ状態が "進行中" から "成功" に変るのを確認できます。この時点で、リリースが承認待ちであることを示すバナーが表示されます。 環境へのデプロイが保留中か、失敗した場合は、青い (i) 情報アイコンが表示されます。 これをポイントすると、理由が記載されたポップアップが表示されます。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\202.png)

リリースの一覧など、その他のビューにも、承認待ちであることを示すアイコンが表示されます。 そのアイコンをポイントすると、環境名と詳細情報が記載されたポップアップが表示されます。 これにより、管理者が、承認待ちのリリースとすべてのリリースの全体的な進行状況を容易に確認できます。

### <a name="monitor-and-track-deployments"></a>デプロイを監視および追跡する

このセクションでは、前のセクションで作成したリリースから、デプロイ (この例では 2 つの Azure App Services Web サイトへのデプロイ) を監視および追跡する方法について説明します。

1. リリースの概要ページで、**[ログ]** リンクを選択します。 デプロイ中、このページには、エージェントからのライブ ログが表示され、左側のウィンドウには、環境ごとにデプロイ プロセスの各操作の状態が示されます。

    デプロイ前またはデプロイ後の承認の **[アクション]** 列でアイコンを選択すると、デプロイを承認 (または拒否) したユーザーの詳細と、そのユーザーからのメッセージが表示されます。

2. デプロイ完了後、ログ ファイル全体が右側のウィンドウに表示されます。 左側のウィンドウで**プロセス ステップ**のいずれかを選択すると、そのステップのログ ファイルの内容のみが表示されます。 これにより、デプロイ全体の各部分を追跡およびデバッグするのが簡単になります。 また、個々のログ ファイル、またはすべてのログ ファイルを含む zip ファイルを、ページのアイコンやリンクからダウンロードすることもできます。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\203.png)
 
3. **[概要]** タブを開くと、リリース全体の詳細を確認できます。 ビルドおよびビルドがデプロイされている環境の詳細、デプロイ状態、およびリリースに関するその他の情報が表示されます。

4. 各**環境リンク**を選択すると、その特定の環境への既存および保留中のデプロイの詳細が表示されます。 これらのページを使用すると、同じビルドが両方の環境にデプロイされていることを確認できます。

5. **デプロイされた運用アプリ**をブラウザーで開きます。 たとえば、Azure App Services Web サイトについては、URL `http://[your-app-name].azurewebsites.net` で開きます。

## <a name="next-steps"></a>次の手順

- Azure のクラウド パターンの詳細については、「[Cloud Design Pattern (クラウド設計パターン)](https://docs.microsoft.com/azure/architecture/patterns)」を参照してください。
