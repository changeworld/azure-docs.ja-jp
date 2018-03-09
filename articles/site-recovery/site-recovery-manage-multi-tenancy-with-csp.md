---
title: "クラウド ソリューション プロバイダー (CSP) プログラムによる Azure Site Recovery でのマルチテナントの管理 | Microsoft Docs"
description: "CSP を使用してテナント サブスクリプションを作成および管理し、Azure Site Recovery をマルチテナント設定でデプロイする方法について説明します。"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 6dc8b573e66eaae1b5cb923ae72333fb959d0969
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2018
---
# <a name="manage-multi-tenancy-with-the-cloud-solution-provider-csp-program"></a>クラウド ソリューション プロバイダー (CSP) プログラムによるマルチテナントの管理

[CSP プログラム](https://partner.microsoft.com/en-US/cloud-solution-provider)は、Office 365、Enterprise Mobility Suite、Microsoft Azure など、Microsoft クラウド サービス向けの優れた連携による事例を促進します。 CSP により、パートナーは顧客とのエンド ツー エンドの関係を築き、顧客にとって最も重要な担当者になることができます。 パートナーは顧客の Azure サブスクリプションをデプロイし、サブスクリプションをカスタマイズされた独自の付加価値サービスと組み合わせることができます。

Azure Site Recovery を使用すると、パートナーは直接 CSP を介して顧客の完全なディザスター リカバリー ソリューションを管理できます。 また、CSP を使用して Site Recovery 環境をセットアップし、顧客がセルフサービス方式で独自のディザスター リカバリーのニーズを管理できるようにすることも可能です。 どちらのシナリオでも、パートナーは Site Recovery と顧客の間の連絡役となります。 パートナーは顧客関係にサービスを提供し、Site Recovery の利用料金を顧客に請求します。

この記事では、マルチテナントの VMware 設定向けに、パートナーが CSP を使用してテナント サブスクリプションを作成および管理する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション、Azure 仮想ネットワーク、ストレージ アカウントなどの Azure リソースを[準備します](tutorial-prepare-azure.md)。
- VMware オンプレミス VMware サーバーと VM を[準備します](tutorial-prepare-on-premises-vmware.md)。
- テナントごとに、テナントの VM やパートナーの vCenter と通信できる個別の管理サーバーを作成します。 パートナーだけがこのサーバーへのアクセス権を持ちます。 各種マルチテナント環境の詳細については、[マルチテナント VMware](site-recovery-multi-tenant-support-vmware-using-csp.md) に関するガイダンスを参照してください。

## <a name="create-a-tenant-account"></a>テナント アカウントを作成する

1. [Microsoft パートナー センター](https://partnercenter.microsoft.com/)から CSP アカウントにサインインします。

2. **[ダッシュボード]** メニューで **[顧客]** を選択します。

    ![Microsoft パートナー センターの [顧客] のリンク](./media/site-recovery-manage-multi-tenancy-with-csp/csp-dashboard-display.png)

3. 表示されたページで、**[顧客の追加]** ボタンをクリックします。

    ![[顧客の追加] ボタン](./media/site-recovery-manage-multi-tenancy-with-csp/add-new-customer.png)

4. **[新しい顧客]** ページで、テナントのアカウント情報の詳細をすべて入力してから、**[次へ: サブスクリプション]** をクリックします。

    ![[アカウント情報] ページ](./media/site-recovery-manage-multi-tenancy-with-csp/customer-add-filled.png)

5. サブスクリプションの選択ページで、**[Microsoft Azure]** チェック ボックスを選択します。 他のサブスクリプションは、今すぐ追加することもできますが、後でいつでも追加できます。

    ![Microsoft Azure サブスクリプションのチェック ボックス](./media/site-recovery-manage-multi-tenancy-with-csp/azure-subscription-selection.png)

6. **[レビュー]** ページで、テナントの詳細を確認し、**[送信]** をクリックします。

    ![[レビュー] ページ](./media/site-recovery-manage-multi-tenancy-with-csp/customer-summary-page.png)  

    テナント アカウントを作成すると確認ページが開き、そのサブスクリプションの既定のアカウントとパスワードの詳細が表示されます。

7. 情報を保存し、必要に応じて、後で Azure Portal のサインイン ページでパスワードを変更します。  

    この情報はテナントと現状のまま共有できます。また、必要に応じて個別のアカウントを作成し、共有することもできます。

## <a name="access-the-tenant-account"></a>テナント アカウントにアクセスする

テナントのサブスクリプションには、"手順 1: テナント アカウントを作成する" で説明した、Microsoft パートナー センターのダッシュボードからアクセスできます。

1. **[顧客]** ページに移動し、テナント アカウントの名前をクリックします。

2. テナント アカウントの **[サブスクリプション]** ページで、既存のアカウント サブスクリプションを監視したり、必要に応じてサブスクリプションをさらに追加したりできます。 テナントのディザスター リカバリー操作を管理するには、**[すべてのリソース (Azure Portal)]** を選択します。

    ![[すべてのリソース] のリンク](./media/site-recovery-manage-multi-tenancy-with-csp/all-resources-select.png)  

    **[すべてのリソース]** をクリックすると、テナントの Azure サブスクリプションへのアクセスが許可されます。 Azure Portal の右上にある Azure Active Directory のリンクをクリックすると、アクセスを確認できます。

    ![Azure Active Directory のリンク](./media/site-recovery-manage-multi-tenancy-with-csp/aad-admin-display.png)

これで、Azure Portal からテナントに対してサイトの回復のすべての操作を実行し、ディザスター リカバリー操作を管理できるようになりました。 管理されたディザスター リカバリーを行うために CSP を使用してテナント サブスクリプションにアクセスするには、上記のプロセスに従います。

## <a name="deploy-resources-to-the-tenant-subscription"></a>テナント サブスクリプションにリソースをデプロイする
1. Azure Portal でリソース グループを作成してから、通常のプロセスごとに Recovery Services コンテナーをデプロイします。

2. コンテナー登録キーをダウンロードします。

3. コンテナー登録キーを使用して、テナントの CS を登録します。

4. vCenter アクセス アカウントと VM アクセス アカウントの 2 つのアカウントの資格情報を入力します。

    ![マネージャー構成サーバー アカウント](./media/site-recovery-manage-multi-tenancy-with-csp/config-server-account-display.png)

## <a name="register-site-recovery-infrastructure-to-the-recovery-services-vault"></a>Site Recovery インフラストラクチャを Recovery Services コンテナーに登録する
1. Azure Portal で、前に作成したコンテナーで、"手順 3: テナント サブスクリプションにリソースをデプロイする" で登録した CS に vCenter サーバーを登録します。 この操作には、vCenter アクセス アカウントを使用します。
2. 通常のプロセスごとに、Site Recovery の "インフラストラクチャの準備" プロセスを完了します。
3. これで VM をレプリケートする準備ができました。 **[仮想マシンの選択]** ブレードの **[レプリケート]** オプションに、目的のテナントの VM だけが表示されていることを確認します。

    ![[仮想マシンの選択] ブレードのテナントの VM のリスト](./media/site-recovery-manage-multi-tenancy-with-csp/tenant-vm-display.png)

## <a name="assign-tenant-access-to-the-subscription"></a>サブスクリプションにテナント アクセスを割り当てる

セルフサービスのディザスター リカバリーの場合、"手順 1: テナント アカウントを作成する" セクションの手順 6. で説明したアカウントの詳細をテナントに提供します。 パートナーがディザスター リカバリーのインフラストラクチャをセットアップした後にこのアクションを実行します。 ディザスター リカバリーの種類 (管理またはセルフサービス) に関係なく、パートナーは CSP ポータルからテナント サブスクリプションにアクセスする必要があります。 パートナーは自身が所有するコンテナーをセットアップし、インフラストラクチャをテナント サブスクリプションに登録します。

パートナーは、次の内容を実行して CSP ポータルで新しいユーザーをテナント サブスクリプションに追加することもできます。

1. テナントの CSP サブスクリプション ページに移動し、**[Users and licenses]\(ユーザーとライセンス\)** を選択します。

    ![テナントの CSP サブスクリプション ページ](./media/site-recovery-manage-multi-tenancy-with-csp/users-and-licences.png)

    関連する詳細を入力してアクセス許可を選択するか、CSV ファイルでユーザーのリストをアップロードすることで、新しいユーザーを作成できます。

2. 新しいユーザーを作成したら、Azure Portal に戻ってから、**[サブスクリプション]** ブレードで該当するサブスクリプションを選択します。

3. 表示されたブレードで、**[アクセス制御 (IAM)]** を選択し、**[追加]** をクリックして適切なアクセス レベルを持つユーザーを追加します。      
    CSP ポータルで作成されたユーザーは、アクセス レベルをクリックした後に表示されるブレードに自動的に表示されます。

    ![ユーザーの追加](./media/site-recovery-manage-multi-tenancy-with-csp/add-user-subscription.png)

    ほとんどの管理操作には、"*共同作成者*" ロールで十分です。 このアクセス レベルのユーザーは、サブスクリプションに対して、アクセス レベルの変更 ("*所有者*" レベルのアクセス権が必要) を除くすべての操作を実行できます。

  Azure Site Recovery には、必要に応じてアクセス レベルをさらに制限するために使用できる、3 つの[定義済みユーザー ロール](site-recovery-role-based-linked-access-control.md)が用意されています。

## <a name="next-steps"></a>次の手順
  Azure Site Recovery デプロイを管理するためのロールベースのアクセス制御に関する[詳細を確認します](site-recovery-role-based-linked-access-control.md)。

  [マルチテナント VMware 環境を管理します](site-recovery-multi-tenant-support-vmware-using-csp.md)
