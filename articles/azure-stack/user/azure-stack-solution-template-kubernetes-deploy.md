---
title: Kubernetes Cluster の Azure Stack へのデプロイ | Microsoft Docs
description: Kubernetes Cluster を Azure Stack にデプロイする方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 43c0b7c87f9ee1cd33da3d617747c11dc120e51a
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823624"
---
# <a name="deploy-a-kubernetes-cluster-to-azure-stack"></a>Kubernetes Cluster の Azure Stack へのデプロイ

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

> [!Note]  
> Azure Stack 上の Azure Container Services (ACS) Kubernetes はプライベート プレビューの段階にあります。 Azure Stack のオペレーターは、この記事の手順を実行するために必要な Kubernetes Marketplace 項目へのアクセスを要求する必要があります。

以降の記事では、Azure Resource Manager ソリューション テンプレートを使用して、1 つの連携した操作で Kubernetes のためにリソースのデプロイとプロビジョニングを行う方法を示しています。 Azure Stack インストールに関する必要な情報を収集し、テンプレートを生成して、クラウドにデプロイする必要があります。

## <a name="kubernetes-and-containers"></a>Kubernetes とコンテナー

Azure Stack 上の Azure Container Services (ACS) エンジンによって生成された Azure Resource Manager テンプレートを使用して、Kubernetes をインストールできます。 [Kubernetes](https://kubernetes.io) はコンテナー内でのアプリケーションのデプロイ、スケーリング、管理を自動化するオープンソース システムです。 [コンテナー](https://www.docker.com/what-container)は、VM と同じように、イメージ内に含まれています。 VM とは違って、コンテナー イメージには、コード、コードを実行するためのランタイム、特定のライブラリ、および設定など、アプリケーションを実行するために必要なリソースのみが含まれています。

Kubernetes は、次の目的で使用できます。

- 数秒でデプロイできる極めてスケーラブルかつアップグレード可能なアプリケーションを開発する。 
- アプリケーションの設計を簡素化し、異なる Helm アプリケーションによって信頼性を向上させる。 [Helm](https://github.com/kubernetes/helm) は、Kubernetes アプリケーションのライフサイクルをインストールおよび管理するのに役立つオープン ソースのパッケージ化ツールです。
- スケーリングとアップグレードの機能を利用して、アプリケーションの正常性を簡単に監視および診断する。

## <a name="prerequisites"></a>前提条件 

使用を開始するには、次の手順に従って、適切なアクセス許可を保持し、Azure Stack の準備が整っていることを確認します。

1. Azure Active Directory (Azure AD) テナントにアプリケーションを作成できることを確認します。 Kubernetes のデプロイには所定のアクセス許可が必要です。

    アクセス許可をチェックする手順については、「[Azure Active Directory のアクセス許可を確認する](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions)」を参照してください。

2. SSH 公開および秘密キー ペアを生成して Azure Stack 上の Linux VM にサインインします。 クラスターを作成するときに、公開キーが必要になります。

    キーを生成する手順については、[SSH キーの生成](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation)に関するページを参照してください。

3. Azure Stack テナント ポータルに有効なサブスクリプションがあること、また、新しいアプリケーションの追加に使用できる十分なパブリック IP アドレスがあることを確認します。

    Azure Stack **Administrator** サブスクリプションにクラスターを展開することはできません。 User** サブスクリプションを使用する必要があります。 

## <a name="create-a-service-principal-in-azure-ad"></a>Azure AD でのサービス プリンシパルの作成

1. グローバルな [Azure Portal](http://portal.azure.com) にサインインします。
2. Azure Stack インスタンスに関連付けられた Azure AD テナントを使ってサインインしたことを確認します。
3. Azure AD アプリケーションを作成します。

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[Azure Active Directory]** > **[+ アプリの登録]** > **[新しいアプリケーションの登録]** を選択します。

    b. アプリケーションの**名前** を入力します。

    c. **[Web アプリ / API]** を選択します。

    d. **[サインオン URL]** に `http://localhost` と入力します。

    c. **Create** をクリックしてください。

4. **アプリケーション ID** をメモします。 クラスターを作成するときに、ID が必要になります。 ID は、**サービス プリンシパル クライアント ID** として参照されます。

5. **[設定]** > **[キー]** の順に選択します。

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[説明]** を入力します。

    b. **[有効期限]** で **[Never expires]\(有効期限なし\)** を選択します。

    c. **[保存]** を選択します。 キー文字列をメモします。 クラスターを作成するときに、キー文字列が必要になります。 キーは、**サービス プリンシパル クライアント シークレット**として参照されます。



## <a name="give-the-service-principal-access"></a>サービス プリンシパルへのアクセスの付与

サービス プリンシパルがリソースを作成できるように、そのプリンシパルにサブスクリプションへのアクセスを付与します。

1.  [管理ポータル](https://adminportal.local.azurestack.external)にサインインします。

2. **[More services]\(その他のサービス\)** > **[ユーザー サブスクリプション]** > **[+ 追加]** の順に選択します。

3. 作成したサブスクリプションを選択します。

4. **[アクセス制御 (IAM)]** > **[+ 追加]** の順に選択します。

5. **[所有者]** ロールを選択します

6. サービス プリンシパル用に作成したアプリケーションの名前を選択します。 必要に応じて、検索ボックスに名前を入力します。

7. **[Save]** をクリックします。

## <a name="deploy-a-kubernetes-cluster"></a>Kubernetes クラスターのデプロイ

1. [Azure Stack ポータル](https://portal.local.azurestack.external)を開きます。

2. **[+新規]** > **[コンピューティング]** > **[Kubernetes Cluster]\(Kubernetes クラスター\)** の順に選択します。 **Create** をクリックしてください。

    ![ソリューション テンプレートのデプロイ](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

3. [Kubernetes クラスターを作成] で **[基本]** を選びます。

    ![ソリューション テンプレートのデプロイ](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

2. **[Linux VM Admin Username]\(Linux VM 管理者ユーザー名\)** を入力します。 Kubernetes クラスターと DVM の一部である Linux Virtual Machines のユーザー名

3. Kubernetes クラスターと DVM の一部として作成されたすべての Linux マシンに対する承認に使用される **[SSH 公開キー]** を入力します。

4. リージョンで一意の **[Master Profile DNS Prefix]\(マスター プロファイル DNS プレフィックス\)** を入力します。 これは、`k8s-12345` など、リージョンで一意の名前になっている必要があります。 ベスト プラクティスとして、リソース グループ名と同じ名前を選択してみてください。

    > [!Note]  
    > 各クラスターに対して、新しい一意のマスター プロファイル DNS プレフィックスを使用してください。

5. **[Agent Pool Profile Count]\(エージェント プール プロファイル数\)** を入力します。 値には、クラスター内のエージェントの数が含まれます。 1 - 4 の値を使用できます。

6. **[Service Principal ClientId]\(サービス プリンシパル クライアント ID\)** を入力します。これは、Kubernetes Azure クラウド プロバイダーによって使用されます。

7. サービス プリンシパル アプリケーションを作成するときに作成した、**[Service Principal Client Secret]\(サービス プリンシパル クライアント シークレット\)** を入力します。

8. **Kubernetes Azure クラウド プロバイダーのバージョン**を入力します。 これは、Kubernetes Azure プロバイダーのバージョンです。 Azure Stack は、各 Azure Stack バージョンに対してカスタムの Kubernetes ビルドをリリースします。

9. **[サブスクリプション]** を選択します。

10. 新しいリソース グループの名前を入力するか、既存のリソース グループを選択します。 リソース名は、英数字かつ小文字にする必要があります。

11. リソース グループの **[場所]** を選択します。 これは、Azure Stack のインストールに対して選択するリージョンです。

### <a name="specify-the-azure-stack-settings"></a>Azure Stack の設定を指定する

1. **[Azure Stack Stamp Settings]\(Azure Stack スタンプの設定\)** を選択します。

    ![ソリューション テンプレートのデプロイ](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings.png)

2. **[Tenant Arm Endpoint]\(テナント ARM エンドポイント\)** を入力します。 これは、Kubernetes クラスターのリソース グループを作成するために接続する Azure Resource Manager のエンドポイントです。 統合システムの Azure Stack のオペレーターからエンドポイントを取得する必要があります。 Azure Stack Development Kit (ASDK) の場合は、`https://management.local.azurestack.external` を使用できます。

3. テナントの **[テナント ID]** を入力します。 この値を見つけるために手助けが必要な場合は、「[テナント ID を取得する](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id)」を参照してください。 

## <a name="connect-to-your-cluster"></a>クラスターへの接続

これで、クラスターに接続する準備ができました。 マスターはクラスター リソース グループ内にあり、`k8s-master-<sequence-of-numbers>` という名前です。 マスターに接続するには SSH クライアントを使います。 マスターでは、Kubernetes のコマンドライン クライアントである **kubectl** を使ってクラスターを管理できます。 方法については、[Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview) をご覧ください。

クラスターへのアプリのインストールと展開には、**Helm** パッケージ マネージャーも役に立ちます。 クラスターでの Helm のインストールと使用の方法については、[helm.sh](https://helm.sh/) をご覧ください。

## <a name="next-steps"></a>次の手順

[Kubernetes Cluster を Marketplace に追加する (Azure Stack のオペレーター)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Azure における Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
