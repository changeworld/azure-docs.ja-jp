---
title: Azure Stack ユーザーが仮想マシンを使用できるようにする | Microsoft Docs
description: Azure Stack で仮想マシンを使用できるようにする方法について説明します
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 6bd722801202f5cdff2882c29895ae06fecbbcb8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425365"
---
# <a name="tutorial-make-virtual-machines-available-to-your-azure-stack-users"></a>チュートリアル: Azure Stack ユーザーが仮想マシンを使用できるようにする

Azure Stack のクラウド管理者は、ユーザー (テナントとも呼ばれます) がサブスクライブできるオファーを作成できます。 オファーにサブスクライブすることで、ユーザーはオファーに用意されている Azure Stack のサービスを使用できるようになります。

このチュートリアルでは、仮想マシン用のオファーを作成し、ユーザーとしてサインインしてそのオファーをテストする方法について説明します。

学習内容

> [!div class="checklist"]
> * オファーの作成
> * イメージの追加
> * オファーのテスト

Azure Stack では、サブスクリプション、オファー、プランを使用してサービスがユーザーに配信されます。 ユーザーは複数のオファーにサブスクライブできます。 オファーには 1 つ以上のプラン、プランには 1 つ以上のサービスが含まれることがあります。

![サブスクリプション、オファー、およびプラン](media/azure-stack-key-features/image4.png)

詳細については、「[Azure Stack の主要な機能と概念](azure-stack-key-features.md)」を参照してください。

## <a name="create-an-offer"></a>オファーの作成

オファーは、プロバイダーがユーザーに提示する 1 つまたは複数のプランのグループで、ユーザーはそれを購入またはサブスクライブします。 オファーを作成するには、いくつかの手順を実行します。 最初にオファーを、次にプランを、最後にクォータを作成するように指示されます。

1. ポータルにクラウドの管理者として[サインイン](azure-stack-connect-azure-stack.md)してから、**[新規]** > **[プラン]** > **[オファー]** の順に選択します。

   ![新しいプラン](media/azure-stack-tutorial-tenant-vm/image01.png)

1. **[新しいオファー]** で、**[表示名]** と **[リソース名]** に入力し、新規または既存の **[リソース グループ]** を選択します。 表示名は、オファーのフレンドリ名です。 リソース名は、クラウド オペレーターのみが表示できます。 この名前を使用して、管理者はオファーを Azure リソース マネージャーのリソースとして操作します。

   ![表示名](media/azure-stack-tutorial-tenant-vm/image02.png)

1. **[基本プラン]** を選択し、**[プラン]** セクションで **[追加]** を選択して新しいプランをオファーに追加します。

   ![プランの追加](media/azure-stack-tutorial-tenant-vm/image03.png)

1. **[新しいプラン]** セクションで **[表示名]** と **[リソース名]** を入力します。 表示名は、ユーザーに表示されるプランのフレンドリ名です。 リソース名は、クラウド オペレーターのみが表示できます。 クラウド オペレーターはこの名前を使用して、プランを Azure Resource Manager のリソースとして操作します。

   ![プランの表示名](media/azure-stack-tutorial-tenant-vm/image04.png)

1. **[サービス]** を選択します。 サービスの一覧から、**[Microsoft.Compute]**、**[Microsoft.Network]**、および **[Microsoft.Storage]** を選びます。 **[選択]** を選択してそれらサービスをプランに追加します。

   ![プランのサービス](media/azure-stack-tutorial-tenant-vm/image05.png)

1. **[クォータ]** を選択してから、クォータを作成する最初のサービスを選択します。 IaaS クォータには、次の例をコンピューティング サービス、ネットワーク サービス、ストレージ サービスのそれぞれにクォータを構成するガイドとして使用します。

   - まず、コンピューティング サービスのクォータを作成します。 名前空間の一覧で、**[Microsoft.Compute]** を選択し、**[新しいクォータの作成]** を選択します。

     ![新しいクォータの作成](media/azure-stack-tutorial-tenant-vm/image06.png)

   - **[クォータの作成]** で、クォータの名前を入力します。 作成中のクォータに表示されているクォータのすべての値は、変更することもそのまま使用することもできます。 この例では、既定の設定をそのまま使用し、**[OK]** を選択します。

     ![クォータ名](media/azure-stack-tutorial-tenant-vm/image07.png)

   - 名前空間の一覧から **[Microsoft.Compute]** を選び、作成したクォータを選択します。 これにより、クォータがコンピューティング サービスにリンクされます。

     ![クォータの選択](media/azure-stack-tutorial-tenant-vm/image08.png)

      ネットワーク サービスとストレージ サービスでこれらの手順を繰り返します。 完了したら、**[クォータ]** で **[OK]** を選択してすべてのクォータを保存します。

1. **[新しいプラン]** で、**[OK]** を選択します。

1. **[プラン]** で、新しいプランを選択し、**[選択]** を選択します。

1. **[新しいオファー]** で、**[作成]** を選択します。 オファーが作成されると通知が表示されます。

1. ダッシュボード メニューで、**[オファー]** を選択し、作成したオファーをクリックします。

1. **[状態の変更]** を選択し、**[パブリック]** を選択します。

    ![[パブリック] の状態](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>イメージの追加

仮想マシンをプロビジョニングする前に、Azure Stack Marketplace にイメージを追加する必要があります。 Azure Marketplace の Linux イメージを含め、任意のイメージを追加できます。

接続されるシナリオで運用していて、Azure に Azure Stack インスタンスを登録済みの場合は、「[Azure から Azure Stack に Marketplace の項目をダウンロードする](azure-stack-download-azure-marketplace-item.md)」というトピックに記載されている手順を使用して、Azure Marketplace から Windows Server 2016 の VM イメージをダウンロードできます。

Marketplace にさまざまな項目を追加する方法については、「[Azure Stack Marketplace](azure-stack-marketplace.md)」を参照してください。

## <a name="test-the-offer"></a>オファーのテスト

オファーを作成したので、そのテストを行うことができます。 ユーザーとしてサインインしてオファーにサブスクライブし、仮想マシンを追加します。

1. 
  **オファーへのサブスクライブ**

   a. ユーザー アカウントでユーザー ポータルにサインインし、**[サブスクリプションの取得]** タイルを選択します。
   - 統合システムの場合、URL はオペレーターのリージョンと外部ドメイン名によって異なり、https://portal.&lt;*リージョン*&gt;.&lt;*FQDN*&gt; の形式になります。
   - Azure Stack Development Kit を使用している場合、ポータルのアドレスは https://portal.local.azurestack.external になります。

   ![サブスクリプションの取得](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. **[サブスクリプションの取得]** で、**[表示名]** フィールドに、お客様のサブスクリプションの名前を入力します。 **[オファー]** を選択し、**[オファーの選択]** の一覧からオファーを 1 つ選びます。 **作成**を選択します。

   ![オファーの作成](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. サブスクリプションを表示するには、**[その他のサービス]** を選択し、**[サブスクリプション]** を選択します。 新しいサブスクリプションを選択し、どのサービスがそのサブスクリプションの一部であるかを確認します。

   >[!NOTE]
   >オファーをサブスクライブした後、どのサービスが新しいサブスクリプションの一部であるかを確認するには、ポータルを更新する必要がある場合があります。

1. **仮想マシンのプロビジョニング**

   ユーザー ポータルから、新しいサブスクリプションを使用して仮想マシンをプロビジョニングできます。

   a. ユーザー アカウントを使用してユーザー ポータルにサインインします。
      - 統合システムの場合、URL はオペレーターのリージョンと外部ドメイン名によって異なり、https://portal.&lt;*リージョン*&gt;.&lt;*FQDN*&gt; の形式になります。
   - Azure Stack Development Kit を使用している場合、ポータルのアドレスは https://portal.local.azurestack.external になります。

   b.  ダッシュボードで、**[新規]** > **[コンピューティング]** > **[Windows Server 2016 Datacenter Eval]** の順に選択し、**[作成]** を選択します。

   c. **[基本]** で、次の情報を指定します。
      - **名前**を入力する
      - **ユーザー名**を入力する
      - **パスワード**を入力する
      - **[サブスクリプション]**
      - **リソース グループ**を作成する (または既存のものを選択する) 
      - **[OK]** を選択してこのパターンを保存する

   d. **[サイズの選択]** で、**[A1 標準]** を選択し、**[選択]** を選択します。  

   e. **[設定]** で、**[仮想ネットワーク]** を選択します。

   f. **[仮想ネットワークの選択]** で、**[新規作成]** を選択します。

   g. **[仮想ネットワークの作成]** で、すべての既定値をそのまま使用し、**[OK]** を選択します。

   h. **[設定]** で **[OK]** を選択し、ネットワーク構成を保存します。

   ![Create virtual network](media/azure-stack-provision-vm/image04.png)

   i. **[概要]** で、**[OK]** を選択して仮想マシンを作成します。  

   j. 新しい仮想マシンを表示するには、**[すべてのリソース]** を選択します。 仮想マシンを検索し、検索結果からその名前を選択します。

   ![すべてのリソース](media/azure-stack-provision-vm/image06.png)

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * オファーの作成
> * イメージの追加
> * オファーのテスト

次のチュートリアルに進み、次の操作方法を確認してください。
> [!div class="nextstepaction"]
> [SQL データベースを Azure Stack ユーザーから使用できるようにする](azure-stack-tutorial-sql-server.md)