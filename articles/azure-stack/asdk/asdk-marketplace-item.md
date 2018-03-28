---
title: Azure から Azure Stack マーケットプレース項目を追加する | Microsoft Docs
description: Azure ベースの Windows Server 仮想マシン イメージを Azure Stack Marketplace に追加する方法について説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 25f179f825526e20377c0e94ccb38a788cadd898
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-azure"></a>チュートリアル: Azure から Azure Stack マーケットプレース項目を追加する

Azure Stack オペレーターとして、ユーザーが仮想マシン (VM) をデプロイできるようにするために最初に行うことは、VM イメージを Azure Stack マーケットプレースに追加することです。 既定では、Azure Stack マーケットプレースには何も発行されませんが、Azure Stack 上で動くことがテスト済みの [Azure Marketplace の項目の精選された一覧](.\.\azure-stack-marketplace-azure-items.md)から項目をダウンロードすることができます。 接続されたシナリオで操作しており、Azure Stack インスタンスを Azure に登録している場合は、このオプションを使用します。

このチュートリアルでは、Windows Server 2016 VM イメージを Azure マーケットプレースから Azure Stack マーケットプレースに追加します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Windows Server VM Azure Stack マーケットプレース項目を追加する
> * VM イメージが使用可能であることを確認する 
> * VM イメージをテストする

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- [Azure Stack と互換性のある Azure PowerShell モジュール](asdk-post-deploy.md#install-azure-stack-powershell)をインストールする
- [Azure Stack ツール](asdk-post-deploy.md#download-the-azure-stack-tools)をダウンロードする
- Azure サブスクリプションに [ASDK を登録する](asdk-register.md)

## <a name="add-a-windows-server-vm-image"></a>Windows Server VM イメージを追加する
Azure マーケットプレースからイメージをダウンロードすることにより、Azure Stack マーケットプレースに Windows Server 2016 イメージを追加できます。 接続されたシナリオで操作しており、既に Azure に [Azure Stack インスタンスを登録](asdk-register.md)している場合は、このオプションを使用します。

1. [ASDK 管理ポータル](https://adminportal.local.azurestack.external)にサインインします。

2. **[More services]\(その他のサービス\)** > **[Marketplace Management]\(Marketplace の管理\)** > **[Add from Azure]\(Azure から追加\)** を選択します。 

   ![Azure から追加する](media/asdk-marketplace-item/azs-marketplace.png)

3. **Windows Server 2016 Datacenter** イメージを見つけるか検索します。

4. **Windows Server 2016 Datacenter** イメージを選択し、**[ダウンロード]** を選択します。

   ![Azure からのイメージのダウンロード](media/asdk-marketplace-item/azure-marketplace-ws2016.png)


> [!NOTE]
> VM イメージをダウンロードして Azure Stack マーケットプレースに発行するには、約 1 時間かかります。 

ダウンロードが完了すると、そのイメージが発行され、**[Marketplace Management]\(Marketplace の管理\)** で利用可能になります。 イメージは **[Compute]\(コンピューティング\)** でも利用可能であり、ここで新しい仮想マシンを作成することができます。

## <a name="verify-the-marketplace-item-is-available"></a>マーケットプレース項目が利用可能であることを確認する
以下の手順を使用して、新しい VM イメージが Azure Stack マーケットプレースで利用可能であることを確認します。

1. [ASDK 管理ポータル](https://adminportal.local.azurestack.external)にサインインします。

2. **[その他のサービス]** > **[Marketplace Management]\(Marketplace の管理\)** の順に選択します。 

3. Windows Server 2016 Datacenter VM イメージが正常に追加されていることを確認します。

   ![Azure からダウンロードされたイメージ](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>VM イメージをテストする
Azure Stack オペレーターとして、[管理者ポータル](https://adminportal.local.azurestack.external)を使用してテスト VM を作成し、イメージが正常に利用可能になったことを検証できます。 

1. [ASDK 管理ポータル](https://adminportal.local.azurestack.external)にサインインします。

2. **[新規]** > **[コンピューティング]** > **[Windows Server 2016 Datacenter]** > **[作成]** の順にクリックします。  
 ![マーケットプレース イメージから VM を作成する](media/asdk-marketplace-item/new-compute.png)

3. **[基本]** ブレードで次の情報を入力し、**[OK]** をクリックします。
  - **名前**: test-vm-1
  - **ユーザー名**: AdminTestUser
  - **パスワード**: AzS-TestVM01
  - **サブスクリプション**: 既定のプロバイダー サブスクリプションを使用します
  - **リソース グループ**: test-vm-rg
  - **場所**: ローカル

4. **[サイズの選択]** ブレードで、**[A1 標準]** をクリックしてから **[選択]** をクリックします。  

5. **[設定]** ブレードで、既定値のまま **[OK]** をクリックします。

6. **[概要]** ブレードで、**[OK]** をクリックして仮想マシンを作成します。  
> [!NOTE]
> 仮想マシンのデプロイが完了するまでに数分かかる場合があります。

7. 新しい VM を表示するには、**[仮想マシン]** をクリックし、**[test-vm-1]** を検索して、その名前をクリックします。
    ![表示される最初のテスト VM イメージ](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ
VM に正常にサインインし、テスト イメージが正常に動作していることを確認した後で、テスト リソース グループを削除する必要があります。 そうすることで、単一ノード ASDK のインストールに利用できる、限られたリソースが解放されます。

不要になったら、次の手順に従って、リソース グループ、VM、および関連するすべてのリソースを削除できます。

1. [ASDK 管理ポータル](https://adminportal.local.azurestack.external)にサインインします。
2. **[リソース グループ]** > **[test-vm-rg]** > **[リソース グループの削除]** の順にクリックします。
3. リソース グループ名として「**test-vm-rg**」と入力し、**[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Windows Server VM Azure Stack マーケットプレース項目を追加する
> * VM イメージが使用可能であることを確認する 
> * VM イメージをテストする

次のチュートリアルに進み、Azure Stack のオファーとプランを作成する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Stack の IaaS サービスをオファーする](asdk-offer-services.md)