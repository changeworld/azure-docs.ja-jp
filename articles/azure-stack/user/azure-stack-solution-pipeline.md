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
ms.date: 06/08/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 3fcede7f813e97885d8fc3d7e0bc04776f2d0d12
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579846"
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>チュートリアル: Azure と Azure Stack にアプリをデプロイする

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

ハイブリッドの継続的インテグレーション/継続的デリバリー (CI/CD) パイプラインを使用して Azure と Azure Stack にアプリケーションをデプロイする方法を説明します。

このチュートリアルでは、以下を実現するためのサンプル環境を作成します。

> [!div class="checklist"]
> * コード コミットに基づいて、Visual Studio Team Services (VSTS) リポジトリに対して新しいビルドを開始します。
> * ユーザー受け入れテストを目的として、アプリをグローバル Azure に自動的にデプロイします。
> * コードがテストに合格した時点で自動的にアプリを Azure Stack にデプロイします。

## <a name="benefits-of-the-hybrid-delivery-build-pipe"></a>ハイブリッド デリバリー ビルド パイプの利点

継続性、セキュリティ、信頼性は、アプリケーション デプロイの重要な要素です。 これらは組織にとってこの上なく重要な要素であり、開発チームにとっては生命線ともいうべきものです。 ハイブリッド CI/CD パイプラインを使用することにより、オンプレミス環境とパブリック クラウドの垣根を越えてビルド パイプを統合することができます。 ハイブリッド デリバリー モデルを使用すれば、アプリケーションに変更を加えることなくデプロイ先を変更することもできます。

他にも、ハイブリッド アプローチの使用には次のような利点があります。

* オンプレミスの Azure Stack 環境と Azure パブリック クラウドとの間で各種開発ツールの一貫性を確保できます。  一連のツールが共通化されることで、CI/CD のパターンや手法が導入しやすくなります。
* Azure または Azure Stack にデプロイされたアプリとサービスは互換性があり、どちらの場所でも同じコードを実行することができます。 オンプレミスとパブリック クラウドの機能を活かすことができます。

CI と CD については、次の記事をご覧ください。

* [継続的インテグレーションとは](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [継続的デリバリーとは](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

## <a name="prerequisites"></a>前提条件

ハイブリッド CI/CD パイプラインを構築するには、いくつかのコンポーネントが必要になります。 次のコンポーネントは準備に時間がかかります。

* 運用 Azure Stack は、Azure OEM/ハードウェア パートナーがデプロイできます。 Azure Stack Development Kit (ASDK) は、すべてのユーザーがデプロイできます。
* また、Azure Stack オペレーターが、App Service のデプロイ、プランとオファーの作成、テナント サブスクリプションの作成、および Windows Server 2016 イメージの追加を行う必要もあります。

>[!NOTE]
>これらのコンポーネントの一部がデプロイ済みである場合は、このチュートリアルを開始する前にそれらがすべての要件を満たしていることをご確認ください。

このチュートリアルは、Azure と Azure Stack について一定の基本知識があることを前提にしています。 チュートリアルを開始する前に詳細を確認するには、次の記事をお読みください。

* [Azure 入門](https://azure.microsoft.com/overview/what-is-azure/)
* [Azure Stack の主要概念](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure-requirements"></a>Azure の要件

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
* Azure で [Web アプリ](https://docs.microsoft.com/azure/app-service/app-service-web-overview)を作成します。 Web アプリの URL は書き留めておいてください。このチュートリアルの中で必要になります。

### <a name="azure-stack-requirements"></a>Azure Stack の要件

* Azure Stack 統合システムを使用するか、Azure Stack Development Kit (ASDK) をデプロイします。 ASDK をデプロイするには、次の手順を実行します。
    * [インストーラーを使った ASDK のデプロイに関するチュートリアル](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy)で詳しいデプロイ手順を確認します。
    * [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ) PowerShell スクリプトを使って ASDK デプロイ後の手順を自動化します。

    > [!Note]
    > ASDK のインストールは完了までに 7 時間ほどかかるため、それに応じて計画を立ててください。

 * [App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS サービスを Azure Stack にデプロイします。
 * Azure Stack に[プラン/オファー](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview)を作成します。
 * Azure Stack に[テナント サブスクリプション](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)を作成します。
 * テナント サブスクリプションに Web アプリを作成します。 後で使用できるように新しい Web アプリの URL を書き留めておきます。
 * テナント サブスクリプションに VSTS 仮想マシンをデプロイします。
* 仮想マシン (VM) 用に .NET 3.5 を含んだ Windows Server 2016 イメージを用意します。 この VM は、プライベート ビルド エージェントとして Azure Stack に構築されます。

### <a name="developer-tool-requirements"></a>開発者ツールの要件

* [VSTS ワークスペース](https://docs.microsoft.com/vsts/repos/tfvc/create-work-workspaces)を作成します。 サインアップ プロセスによって **MyFirstProject** という名前のプロジェクトが作成されます。
* [Visual Studio 2017 をインストール](https://docs.microsoft.com/visualstudio/install/install-visual-studio)して [VSTS にサインイン](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)します。
* プロジェクトに接続し、[ローカルに複製](https://www.visualstudio.com/docs/git/gitquickstart)します。

 > [!Note]
 > Azure Stack 環境には、Windows Server と SQL Server を実行する適切なイメージがシンジケート化されている必要があります。 また、App Service がデプロイされている必要があります。

## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>Visual Studio Team Services 統合用にプライベート ビルドとリリース エージェントを準備する

### <a name="prerequisites"></a>前提条件

Visual Studio Team Services (VSTS) では、サービス プリンシパルを使用して、Azure Resource Manager に対する認証が行われます。 Azure Stack サブスクリプションにリソースをプロビジョニングするためには、VSTS に**共同作成者**ロールが必要です。

認証を構成するための要件を次の手順で説明します。

1. サービス プリンシパルを作成するか、既存のサービス プリンシパルを使用します。
2. サービス プリンシパルの認証キーを作成します。
3. サービス プリンシパル名 (SPN) を共同作成者ロールに含めることができるように、ロールベースのアクセス制御を使用して、Azure Stack サブスクリプションを検証します。
4. Azure Stack エンドポイントと SPN 情報を使用して、VSTS での新しいサービス定義を作成します。

### <a name="create-a-service-principal"></a>サービス プリンシパルを作成する

[サービス プリンシパルの作成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)の手順を参照してサービス プリンシパルを作成し、アプリケーションの種類として **[Web アプリ/API]** を選択します。

### <a name="create-an-access-key"></a>アクセス キーを作成する

サービス プリンシパルには、認証用のキーが必要です。 次の手順に従ってキーを生成してください。

1. Azure Active Directory の **[アプリの登録]** で、アプリケーションを選択します。

    ![アプリケーションを選択](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2. **[アプリケーション ID]** の値をメモします。 この値は、VSTS でサービス エンドポイントを構成するときに使用します。

    ![アプリケーション ID](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. 認証キーを生成するには、**[設定]** を選択します。

    ![アプリの設定を編集](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. 認証キーを生成するには、**[キー]** を選択します。

    ![キーの設定を構成](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. キーの説明を入力し、キーの期間を設定します。 操作が完了したら、**[保存]** をクリックします。

    ![キーの説明と期間](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    キーを保存した後、キーの**値**が表示されます。 この値をコピーしてください。この値を後から取得することはできません。 **キー値**は、アプリケーションとしてサインインする際にアプリケーション ID と共に入力します。 アプリケーションが取得できる場所にキー値を保存します。

    ![キー値](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-the-tenant-id"></a>テナント ID を取得する

VSTS には、サービス エンドポイント構成の一部として、Azure Stack スタンプのデプロイ先の AAD ディレクトリに対応する**テナント ID** が必要です。 テナント ID を取得するには次の手順に従います。

1. **[Azure Active Directory]** を選択します。

    ![テナントの Azure Active Directory](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. テナント ID を取得するには、Azure AD テナントの **[プロパティ]** を選択します。

    ![テナントのプロパティを表示](media\azure-stack-solution-hybrid-pipeline\000_08.png)

3. **ディレクトリ ID** をコピーします。 この値がテナント ID です。

    ![ディレクトリ ID](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Azure Stack サブスクリプション内にリソースをデプロイするサービス プリンシパル権限を付与する

サブスクリプション内のリソースにアクセスするには、アプリケーションをロールに割り当てる必要があります。 アプリケーションにとって最適なアクセス許可を表すのはどのロールであるかを判断します。 利用可能なロールについては、「[RBAC: 組み込みのロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)」を参照してください。

スコープは、サブスクリプション、リソース グループ、またはリソースのレベルで設定できます。 アクセス許可は、スコープの下位レベルに継承されます。 たとえば、アプリケーションをリソース グループの閲覧者ロールに追加すると、アプリケーションではリソース グループとそのすべてのリソースを読み取ることができます。

1. アプリケーションを割り当てるスコープのレベルに移動します。 たとえば、サブスクリプション スコープでロールを割り当てるには、**[サブスクリプション]** を選択します。

    ![[サブスクリプション] を選択します。](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. **[サブスクリプション]** で [Visual Studio Enterprise] を選択します。

    ![Visual Studio Enterprise](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. Visual Studio Enterprise で **[アクセス制御 (IAM)]** を選択します。

    ![アクセス制御 (IAM)](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. **[追加]** を選択します。

    ![Add](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. **[アクセス許可の追加]** で、アプリケーションに割り当てるロールを選択します。 この例では **[所有者]** ロールです。

    ![[所有者] ロール](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. 既定では、Azure Active Directory アプリケーションは、使用可能なオプションに表示されません。 目的のアプリケーションを見つけるには、その名前を **[選択]** フィールドに指定して検索する必要があります。 アプリを選びます。

    ![アプリの検索結果](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. **[保存]** を選択して、ロールの割り当てを完了します。 該当のスコープのロールに割り当てられたユーザーの一覧にアプリケーションが表示されます。

### <a name="role-based-access-control"></a>ロールベースのアクセス制御

‎Azure のアクセス権は、そのロールベースのアクセス制御 (RBAC) によって詳細に管理することができます。 それぞれの職務を遂行するユーザーに必要なアクセスのレベルを RBAC を使用して制御することができます。 ロールベースのアクセス制御の詳細については、[Azure サブスクリプション リソースへのアクセスの管理](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json)に関するページをご覧ください。

### <a name="vsts-agent-pools"></a>VSTS エージェント プール

各エージェントを個別に管理するのではなく、エージェント プールにまとめて整理することができます。 エージェント プールでは、そのプール内のすべてのエージェントに対して共有境界が定義されています。 VSTS では、エージェント プールのスコープは VSTS アカウントに制限されます。つまり、チーム プロジェクトの枠を越えてエージェント プールを共有できます。 エージェント プールの詳細については、[エージェント プールとキューの作成](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts)に関するページをご覧ください。

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Azure Stack の個人用アクセス トークン (PAT) を追加する

VSTS にアクセスするための個人用アクセス トークンを作成します。

1. VSTS アカウントにサインインし、ご自分のアカウント プロファイル名を選択します。
2. **[セキュリティの管理]** を選択して、トークン作成ページにアクセスします。

    ![ユーザー サインイン](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![チーム プロジェクトの選択](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![個人用アクセス トークンの追加](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![トークンの作成](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. トークンをコピーします。

    > [!Note]
    > トークン情報は保存しておいてください。 この情報は格納されず、この Web ページから離れると二度と表示されません。

    ![個人用アクセス トークン](media\azure-stack-solution-hybrid-pipeline\000_19.png)

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Azure Stack でホストされているビルド サーバーに VSTS ビルド エージェントをインストールする

1. Azure Stack ホストにデプロイしたビルド サーバーに接続します。
2. ご自身の個人用アクセス トークン (PAT) を使用して、ビルド エージェントをダウンロードし、サービスとしてデプロイします。その後、VM 管理者アカウントとして実行します。

    ![ビルド エージェントのダウンロード](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. 抽出したビルド エージェントのフォルダーに移動します。 管理者特権でのコマンド プロンプトから **config.cmd** ファイルを実行します。

    ![抽出したビルド エージェント](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![ビルド エージェントの登録](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4. config.cmd が完了すると、ビルド エージェントのフォルダーが追加ファイルで更新されます。 抽出された内容を含むフォルダーは次のようになります。

    ![ビルド エージェントのフォルダーの更新](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    VSTS フォルダーにエージェントが確認できます。

## <a name="endpoint-creation-permissions"></a>エンドポイント作成のアクセス許可

Visual Studio Online (VSTO) のビルドでは、エンドポイントを作成することにより、Azure サービス アプリを Azure Stack にデプロイすることができます。 VSTS がビルド エージェントに接続し、エージェントが Azure Stack に接続します。

![VSTO における NorthwindCloud サンプル アプリ](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. VSTO にサインインし、アプリの設定ページに移動します。
2. **[設定]** の **[セキュリティ]** を選択します。
3. **[VSTS グループ]** の **[エンドポイント作成者]** を選択します。

    ![NorthwindCloud のエンドポイント作成者](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

4. **[メンバー]** タブで **[追加]** を選択します。

    ![メンバーを追加する](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

5. **[ユーザーとグループの追加]** にユーザー名を入力し、そのユーザーをユーザー一覧から選択します。
6. **[変更の保存]** を選択します。
7. **[VSTS グループ]** の一覧で、**[エンドポイント管理者]** を選択します。

    ![NorthwindCloud のエンドポイント管理者](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

8. **[メンバー]** タブで **[追加]** を選択します。
9. **[ユーザーとグループの追加]** にユーザー名を入力し、そのユーザーをユーザー一覧から選択します。
10. **[変更の保存]** を選択します。

これでエンドポイント情報が存在するので、VSTS から Azure Stack への接続を使用する準備は完了です。 Azure Stack のビルド エージェントは、VSTS から命令を受け取った後、Azure Stack との通信に使用されるエンドポイント情報を伝達します。

![ビルド エージェント](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

## <a name="develop-your-application-build"></a>アプリケーション ビルドの開発

チュートリアルのこの部分では、次の内容について説明します。

* コードを VSTS プロジェクトに追加する。
* 自己完結型 Web アプリ デプロイを作成する。
* 継続的配置プロセスを構成する。

> [!Note]
 > Azure Stack 環境には、Windows Server と SQL Server を実行する適切なイメージがシンジケート化されている必要があります。 また、App Service がデプロイされている必要があります。 Azure Stack オペレーターの要件については、App Service のドキュメントの「前提条件」セクションをご覧ください。

ハイブリッド CI/CD は、アプリケーション コードとインフラストラクチャ コードの両方に適用できます。 VSTS から [Azure Resource Manager テンプレート](https://azure.microsoft.com/resources/templates/)を Web アプリ コードのように使用して両方のクラウドに対してデプロイします。

### <a name="add-code-to-a-vsts-project"></a>コードを VSTS プロジェクトに追加する

1. Azure Stack でのプロジェクト作成権限が付与されているアカウントを使用して、VSTS にサインインします。 次のキャプチャ画面は、HybridCICD プロジェクトへの接続方法を示しています。

    ![プロジェクトに接続](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. 既定の Web アプリを作成して開くことで、**リポジトリを複製**します。

    ![リポジトリの複製](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>両方のクラウドで App Services の自己完結型 Web アプリ デプロイを作成する

1. **WebApplication.csproj** ファイルを編集します。**Runtimeidentifier** を選択し、`win10-x64.` を追加します。詳細については、[自己完結型のデプロイ](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)に関するドキュメントをご覧ください。

    ![Runtimeidentifier の構成](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. チーム エクスプローラーを使用して、コードを VSTS にチェックインします。

3. アプリケーション コードが Visual Studio Team Services にチェックインされたことを確認します。

### <a name="create-the-build-definition"></a>ビルド定義を作成する

1. ビルド定義を作成できるアカウントで VSTS にサインインします。
2. プロジェクトの **[Build Web Applicaiton]\(Web アプリケーションのビルド\)** ページに移動します。

3. **[引数]** に **-r win10-x64** コードを追加します。 これは、.Net Core を使用して自己完結型のデプロイをトリガーするために必要です。

    ![ビルド定義の引数を追加](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

4. ビルドを実行します。 [自己完結型のデプロイ ビルド](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)のプロセスにより、Azure および Azure Stack 上で実行できる成果物が発行されます。

### <a name="use-an-azure-hosted-build-agent"></a>Azure ホスト ビルド エージェントを使用する

Web アプリをビルドしてデプロイする場合、VSTS でホスト ビルド エージェントを使用すると便利です。 エージェントのメンテナンスやアップグレードは Microsoft Azure によって自動的に実施されるので、中断のない継続的な開発サイクルが実現します。

### <a name="configure-the-continuous-deployment-cd-process"></a>継続的配置 (CD) プロセスを構成する

Visual Studio Team Services (VSTS) および Team Foundation Server (TFS) が提供するパイプラインは自由に構成でき、管理性にも優れ、開発、ステージング、品質保証 (QA)、運用など、さまざまな環境へのリリースに使用できます。 このプロセスの一環として、アプリケーション ライフ サイクルの特定のステージで承認を要求することもできます。

### <a name="create-release-definition"></a>リリース定義の作成

アプリケーション ビルド プロセスの最後の手順は、リリース定義の作成です。 このリリース定義を使用してリリースを作成し、ビルドをデプロイします。

1. VSTS にサインインして、プロジェクトの **[ビルドとリリース]** に移動します。
2. **[リリース]** タブで **\[ + ]** を選択し、**[リリース定義の作成]** を選択します。

   ![リリース定義の作成](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

3. **[テンプレートの選択]** で **[Azure App Service の配置]** を選択し、**[適用]** を選択します。

    ![テンプレートを適用](media\azure-stack-solution-hybrid-pipeline\102.png)

4. **[成果物の追加]** の **[ソース (ビルド定義)]** プルダウン メニューから Azure Cloud ビルド アプリを選択します。

    ![成果物を追加する](media\azure-stack-solution-hybrid-pipeline\103.png)

5. **[パイプライン]** タブで、**1 フェーズ**、**1 タスク**のリンクを選択し、**環境のタスクを表示**します。

    ![パイプライン ビューのタスク](media\azure-stack-solution-hybrid-pipeline\104.png)

6. **[タスク]** タブで **[環境名]** に「Azure」と入力し、**[Azure サブスクリプション]** ボックスの一覧から [AzureCloud Traders-Web EP] を選択します。

    ![環境変数の設定](media\azure-stack-solution-hybrid-pipeline\105.png)

7. **Azure App Service の名前**として「northwindtraders」と入力します。次のキャプチャ画面を参照してください。

    ![App Service の名前](media\azure-stack-solution-hybrid-pipeline\106.png)

8. [エージェント フェーズ] で、**[エージェント キュー]** ボックスの一覧から **[Hosted VS2017]** を選択します。

    ![ホストされたエージェント](media\azure-stack-solution-hybrid-pipeline\107.png)

9. **[Azure App Service 配置]** で、環境に対して有効な**パッケージまたはフォルダー**を選択します。

    ![パッケージまたはフォルダーを選択](media\azure-stack-solution-hybrid-pipeline\108.png)

10. **[ファイルまたはフォルダーの選択]** で、**保存先**に対して **[OK]** を選択します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\109.png)

11. すべての変更を保存し、**[パイプライン]** に戻ります。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\110.png)

12. **[パイプライン]** タブで **[成果物の追加]** を選択し、**[ソース (ビルド定義)]** ボックスの一覧から **[NorthwindCloud Traders-Vessel]** を選択します。

    ![新しい成果物の追加](media\azure-stack-solution-hybrid-pipeline\111.png)

13. **[テンプレートの選択]** で、もう 1 つ環境を追加します。 **[Azure App Service の配置]** を選択し、**[適用]** を選択します。

    ![テンプレートの選択](media\azure-stack-solution-hybrid-pipeline\112.png)

14. **[環境名]** として「Azure Stack」と入力します。

    ![環境名](media\azure-stack-solution-hybrid-pipeline\113.png)

15. **[タスク]** タブで、[Azure Stack] を見つけて選択します。

    ![Azure Stack 環境](media\azure-stack-solution-hybrid-pipeline\114.png)

16. **[Azure サブスクリプション]** ボックスの一覧から、Azure Stack のエンドポイントに使用する [AzureStack Traders-Vessel EP] を選択します。

    ![代替テキスト](media\azure-stack-solution-hybrid-pipeline\115.png)

17. **[App Service の名前]** として、Azure Stack Web アプリの名前を入力します。

    ![App Service の名前](media\azure-stack-solution-hybrid-pipeline\116.png)

18. **[エージェントの選択]** で、**[エージェント キュー]** ボックスの一覧から [AzureStack -bDouglas Fir] を選択します。

    ![エージェントの選択](media\azure-stack-solution-hybrid-pipeline\117.png)

19. **[Azure App Service 配置]** で、環境に対して有効な**パッケージまたはフォルダー**を選択します。 **[ファイルまたはフォルダーの選択]** で、フォルダーの**保存先**に対して **[OK]** を選択します。

    ![パッケージまたはフォルダーの選択](media\azure-stack-solution-hybrid-pipeline\118.png)

    ![保存先の承認](media\azure-stack-solution-hybrid-pipeline\119.png)

20. **[変数]** タブで、**VSTS_ARM_REST_IGNORE_SSL_ERRORS** という名前の変数を見つけます。 変数の値を **[true]** に設定し、そのスコープを **[Azure Stack]** に設定します。

    ![変数の構成](media\azure-stack-solution-hybrid-pipeline\120.png)

21. **[パイプライン]** タブで、NorthwindCloud Traders-Web の成果物に使用する **[継続的配置トリガー]** アイコンを選択し、**[継続的配置トリガー]** を **[有効]** に設定します。  "NorthwindCloud Traders-Vessel" の成果物についても同じ操作を行います。

    ![継続的配置トリガーの設定](media\azure-stack-solution-hybrid-pipeline\121.png)

22. Azure Stack 環境で**配置前**条件アイコンを選択し、トリガーを**リリース後**に設定します。

    ![配置前条件のトリガーを設定](media\azure-stack-solution-hybrid-pipeline\122.png)

23. すべての変更を保存します。

> [!Note]
> リリース タスクの一部の設定は、テンプレートからリリース定義を作成したときに、[環境変数](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables)として自動的に定義されている可能性があります。 これらの設定は、タスクの設定では変更できません。 ただし親環境の項目では、これらの設定を編集することができます。

## <a name="create-a-release"></a>リリースを作成する

リリース定義への変更が完了したら、デプロイを開始できます。 これを行うには、リリース定義からリリースを作成します。 リリースは自動的に作成される場合があります。たとえば、継続的配置トリガーはリリース定義で設定されています。 つまり、ソース コードを変更すると、新しいビルドが起動され、これにより新しいリリースが起動されます。 ただし、このセクションでは、新しいリリースを手動で作成します。

1. **[パイプライン]** タブの **[リリース]** ボックスの一覧を開いて、**[リリースの作成]** を選択します。

    ![リリースを作成する](media\azure-stack-solution-hybrid-pipeline\200.png)

2. リリースの説明を入力し、正しい成果物が選択されていることを確認して、**[作成]** を選択します。 しばらくすると、新しいリリースが作成されたことを示すバナーが表示され、そのリリース名がリンクとして表示されます。 リンクを選択すると、リリース概要ページが表示されます。

    ![リリース作成バナー](media\azure-stack-solution-hybrid-pipeline\201.png)

3. リリースの概要ページに、リリースに関する詳細が表示されます。 次の "Release-2" のキャプチャ画面で、**[環境]** セクションを見ると、Azure については**デプロイ状態**が "進行中" として表示され、Azure Stack については "成功" と表示されていることがわかります。 Azure 環境のデプロイ状態が "成功" に変わると、リリースの承認準備が完了したことを示すバナーが表示されます。 デプロイが保留中か、失敗した場合は、青い **(i)** 情報アイコンが表示されます。 このアイコンにマウス ポインターを合わせると、遅延または失敗の理由を示すポップアップが表示されます。

    ![リリース概要ページ](media\azure-stack-solution-hybrid-pipeline\202.png)

リリースの一覧など、その他のビューにも、承認待ちであることを示すアイコンが表示されます。 このアイコンのポップアップには、環境の名前のほか、デプロイに関連するさらに詳しい情報が表示されます。 管理者は、リリースの全体的な進行状況を見て、どのリリースが承認待ちになっているかを簡単に確認できます。

### <a name="monitor-and-track-deployments"></a>デプロイを監視および追跡する

このセクションでは、すべてのデプロイを監視し、追跡する方法について説明します。 ここでは、2 つの Azure App Services Web サイトをデプロイするためのリリースを例に取り上げています。

1. "Release-2" の概要ページで、**[ログ]** を選択します。 デプロイ時には、エージェントからリアルタイムのログがこのページに表示されます。 左側のウィンドウには、デプロイに伴う各操作の状態が環境ごとに表示されます。

    デプロイ前またはデプロイ後の承認の **[アクション]** 列で人アイコンを選択すると、デプロイを承認 (または拒否) したユーザーと、そのユーザーからのメッセージが表示されます。

2. デプロイ完了後、ログ ファイル全体が右側のウィンドウに表示されます。 左側のウィンドウでいずれかの**ステップ**を選択すると、"ジョブの初期化" など、単一ステップのログ ファイルが表示されます。 ログを個別に表示できることから、デプロイ全体を構成する各要素の追跡とデバッグがしやすくなっています。 特定のステップのログ ファイルを**保存**したり、**すべてのログを zip としてダウンロード**したりすることができます。

    ![リリース ログ](media\azure-stack-solution-hybrid-pipeline\203.png)

3. **[概要]** タブを開くと、そのリリースの概要情報が表示されます。 ビルドとそのデプロイ先となった環境の詳細、デプロイ状態など、リリースに関する各種の情報がこのビューに表示されます。

4. 環境リンク (**Azure** または **Azure Stack**) を選択すると、特定の環境に対する既存のデプロイと保留中のデプロイについての情報が表示されます。 これらのビューを使って簡単に、同じビルドが両方の環境にデプロイされていることを確認できます。

5. **デプロイされた運用アプリ**をブラウザーで開きます。 たとえば、Azure App Services Web サイトの URL `http://[your-app-name].azurewebsites.net` を開きます。

## <a name="next-steps"></a>次の手順

* Azure のクラウド パターンの詳細については、「[Cloud Design Pattern (クラウド設計パターン)](https://docs.microsoft.com/azure/architecture/patterns)」を参照してください。
