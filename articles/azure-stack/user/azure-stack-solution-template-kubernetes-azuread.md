---
title: Azure Active Directory (Azure AD) を使用して Azure Stack に Kubernetes をデプロイする | Microsoft Docs
description: Azure Active Directory (Azure AD) を使用して Azure Stack に Kubernetes をデプロイする方法について説明します。
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
ms.date: 01/16/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: c207fb866cdd17e4db1796e415c159eb887eef08
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243557"
---
# <a name="deploy-kubernetes-to-azure-stack-using-azure-active-directory"></a>Azure Active Directory を使用して Azure Stack に Kubernetes をデプロイする

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

> [!Note]  
> Azure Stack 上の Kubernetes はプレビュー段階にあります。

この記事の手順に従えば、Azure Active Directory (Azure AD) を ID 管理サービスとして 1 回の連携した操作で使用するときに、Kubernetes のリソースをデプロイおよび設定することができます。

## <a name="prerequisites"></a>前提条件

使用を開始するには、次の手順に従って、適切なアクセス許可を保持し、Azure Stack の準備が整っていることを確認します。

1. Azure Active Directory (Azure AD) テナントにアプリケーションを作成できることを確認します。 Kubernetes のデプロイには所定のアクセス許可が必要です。

    アクセス許可をチェックする手順については、「[Azure Active Directory のアクセス許可を確認する](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions)」を参照してください。

1. SSH 公開および秘密キー ペアを生成して Azure Stack 上の Linux VM にサインインします。 クラスターを作成するときに、公開キーが必要になります。

    キーを生成する手順については、[SSH キーの生成](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation)に関するページを参照してください。

1. Azure Stack テナント ポータルに有効なサブスクリプションがあること、また、新しいアプリケーションの追加に使用できる十分なパブリック IP アドレスがあることを確認します。

    Azure Stack **Administrator** サブスクリプションにクラスターを展開することはできません。 **User** サブスクリプションを使用する必要があります。 

1. マーケットプレースに Kubernetes クラスターがない場合は、Azure Stack 管理者に連絡してください。

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

Azure でサービス プリンシパルを設定します。 サービス プリンシパルは、アプリケーションに Azure Stack リソースへのアクセス権を付与します。

1. グローバルな [Azure Portal](http://portal.azure.com) にサインインします。

1. Azure Stack インスタンスに関連付けられた Azure AD テナントを使ってサインインしたことを確認します。 Azure ツールバーのフィルター アイコンをクリックすると、サインインを切り替えることができます。

    ![AD テナントを選択する](media/azure-stack-solution-template-kubernetes-deploy/tenantselector.png)

1. Azure AD アプリケーションを作成します。

    a. **[Azure Active Directory]** > **[+ アプリの登録]** > **[新しいアプリケーションの登録]** を選択します。

    b. アプリケーションの**名前** を入力します。

    c. **[Web アプリ / API]** を選択します。

    d. **[サインオン URL]** に `http://localhost` と入力します。

    c. **Create** をクリックしてください。

1. **アプリケーション ID** をメモします。 クラスターを作成するときに、ID が必要になります。 ID は、**サービス プリンシパル クライアント ID** として参照されます。

1. **[設定]** > **[キー]** の順に選択します。

    a. **[説明]** を入力します。

    b. **[有効期限]** で **[Never expires]\(有効期限なし\)** を選択します。

    c. **[保存]** を選択します。 キー文字列をメモします。 クラスターを作成するときに、キー文字列が必要になります。 キーは、**サービス プリンシパル クライアント シークレット**として参照されます。

## <a name="give-the-service-principal-access"></a>サービス プリンシパルへのアクセスの付与

サービス プリンシパルがリソースを作成できるように、そのプリンシパルにサブスクリプションへのアクセスを付与します。

1.  [Azure Stack ポータル](https://portal.local.azurestack.external/)にサインインします。

1. **[すべてのサービス]** > **[サブスクリプション]** を選択します。

1. オペレーターによって作成された、Kubernetes クラスターを使用するためのサブスクリプションを選択します。

1. **[アクセス制御 (IAM)]** を選択し、**[ロール割り当ての追加]** を選択します。

1. **[共同作成者]** ロールを選択します。

1. サービス プリンシパル用に作成したアプリケーションの名前を選択します。 必要に応じて、検索ボックスに名前を入力します。

1. **[Save]** をクリックします。

## <a name="deploy-kubernetes"></a>Kubernetes のデプロイ

1. [Azure Stack ポータル](https://portal.local.azurestack.external)を開きます。

1. **[+ リソースの作成]** > **[コンピューティング]** > **[Kubernetes クラスター]** を選択します。 **Create** をクリックしてください。

    ![ソリューション テンプレートのデプロイ](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1.基本

1. [Kubernetes クラスターを作成] で **[基本]** を選びます。

    ![ソリューション テンプレートのデプロイ](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. **[サブスクリプション]** を選択します。

1. 新しいリソース グループの名前を入力するか、既存のリソース グループを選択します。 リソース名は、英数字かつ小文字にする必要があります。

1. リソース グループの **[場所]** を選択します。 これは、Azure Stack のインストールに対して選択するリージョンです。

### <a name="2-kubernetes-cluster-settings"></a>2.Kubernetes クラスターの設定

1. [Kubernetes クラスターを作成] で **[Kubernetes Cluster Settings] (Kubernetes クラスターの設定)** を選択します。

    ![ソリューション テンプレートのデプロイ](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-aad.png)

1. **[Linux VM Admin Username]\(Linux VM 管理者ユーザー名\)** を入力します。 Kubernetes クラスターと DVM の一部である Linux Virtual Machines のユーザー名

1. Kubernetes クラスターと DVM の一部として作成されたすべての Linux マシンに対する承認に使用される **[SSH 公開キー]** を入力します。

1. リージョンで一意の **[Master Profile DNS Prefix]\(マスター プロファイル DNS プレフィックス\)** を入力します。 これは、`k8s-12345` など、リージョンで一意の名前になっている必要があります。 ベスト プラクティスとして、リソース グループ名と同じ名前を選択してみてください。

    > [!Note]  
    > 各クラスターに対して、新しい一意のマスター プロファイル DNS プレフィックスを使用してください。

1. **[Kubernetes Master Pool Profile Count] (Kubernetes マスター プール プロファイル数)** を選択します。 この数には、マスター プール内のノードの数が含まれます。 1 ～ 7 を使用できます。 この値は奇数である必要があります。

1. **[The VMSize of the Kubernetes master VMs] (Kubernetes マスター VM の VMSize)** を選択します。

1. **[Kubernetes Node Pool Profile Count] (Kubernetes ノード プール プロファイル数)** を選択します。 値には、クラスター内のエージェントの数が含まれます。 

1. **[Storage Profile] (ストレージ プロファイル)** を選択します。 **[Blob Disk] (Blob ディスク)** または **[Managed Disk] (マネージド ディスク)** を選択できます。 これにより、Kubernetes ノード VM の VM サイズを指定します。 

1. Azure Stack のインストールに対して、**Azure Stack の ID システム**用の **Azure AD** を選択します。 

1. **[Service Principal ClientId]\(サービス プリンシパル クライアント ID\)** を入力します。これは、Kubernetes Azure クラウド プロバイダーによって使用されます。 サービス プリンシパルを作成したときにアプリケーション ID として識別されたクライアント ID。

1. サービス プリンシパルを作成するときに作成した、**[Service Principal Client Secret]\(サービス プリンシパル クライアント シークレット\)** を入力します。

1. **Kubernetes Azure クラウド プロバイダーのバージョン**を入力します。 これは、Kubernetes Azure プロバイダーのバージョンです。 Azure Stack は、各 Azure Stack バージョンに対してカスタムの Kubernetes ビルドをリリースします。

### <a name="3-summary"></a>手順 3.まとめ

1. [Summary] (概要) を選択します。 ブレードには、Kubernetes クラスターの構成設定の検証メッセージが表示されます。

    ![ソリューション テンプレートのデプロイ](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. 設定を確認します。

3. **[OK]** を選択してクラスターをデプロイします。

> [!TIP]  
>  デプロイに関して質問がある場合は、[Azure Stack フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)で質問を投稿するか、他の人が既に回答を受け取っていないか確認することができます。


## <a name="next-steps"></a>次の手順

[クラスターへの接続](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)