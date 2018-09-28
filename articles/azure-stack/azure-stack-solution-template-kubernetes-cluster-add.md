---
title: Kubernetes を Azure Stack Marketplace に追加する | Microsoft Docs
description: Kubernetes を Azure Stack Marketplace に追加する方法について説明します。
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
ms.date: 09/25/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 69bf788ef30a18bbe70e251fdd6a814d0f528f55
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994566"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Kubernetes を Azure Stack Marketplace に追加する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

> [!note]  
> Azure Stack 上の Kubernetes はプレビュー段階にあります。 この記事の手順を実行するために必要な Kubernetes Cluster Marketplace 項目へのアクセスを要求するには、[アクセス取得の要求を送信](https://aka.ms/azsk8)してください。

Kubernetes は、Marketplace 項目としてユーザーに提供できます。 ユーザーは、1 回の連携した操作で Kubernetes をデプロイできます。

以降の記事では、Azure Resource Manager テンプレートを使用して、スタンドアロンの Kubernetes Cluster のためにリソースのデプロイとプロビジョニングを行う方法を示しています。 開始する前に、Azure Stack とグローバルな Azure テナントの設定を確認します。 必要な、Azure Stack に関する情報を収集します。 テナントと Azure Stack Marketplace に必要なリソースを追加します。 このクラスターは、Ubuntu サーバー、カスタム スクリプト、およびマーケットプレース内に置かれる Kubernetes 項目に依存します。

## <a name="create-a-plan-an-offer-and-a-subscription"></a>プラン、オファー、サブスクリプションを作成する

Kubernetes の Marketplace 項目のプラン、オファー、サブスクリプションを作成します。 既存のプランとオファーを使うこともできます。

1. [管理ポータル](https://adminportal.local.azurestack.external)にサインインします。

1. プランは基本プランとして作成します。 手順については、「[Azure Stack でのプランの作成](azure-stack-create-plan.md)」をご覧ください。

1. オファーを作成します。 手順については、「[Azure Stack でのオファーの作成](azure-stack-create-offer.md)」をご覧ください。

1. **[オファー]** を選択し、作成したプランを見つけます。

1. [オファー] ブレードで **[概要]** を選択します。

1. **[状態の変更]** を選択します。 **[パブリック]** を選択します。

1. **[+ リソースの作成]** > **[Offers and Plans] (オファーとプラン)** > **[サブスクリプション]** と選択し、新しいサブスクリプションを作成します。

    a. **表示名**を入力します。

    b. **ユーザー**を入力します。 テナントに関連付けられている Azure AD アカウントを使用します。

    c. **[プロバイダーの説明]**

    d. **[Directory tenant]** を Azure Stack の Azure AD テナントに設定します。 

    e. **[オファー]** を選択します。 作成したオファーの名前を選択します。 サブスクリプション ID をメモします。

## <a name="add-an-ubuntu-server-image"></a>Ubuntu サーバーのイメージを追加する

次の Ubuntu Server イメージを Marketplace に追加します。

1. [管理ポータル](https://adminportal.local.azurestack.external)にサインインします。

1. **[すべてのサービス]** を選択し、**[管理]** カテゴリで **[Marketplace management] (Marketplace 管理)** を選択します。

1. **+ Add from Azure**(+ Azure から追加) を選択します。

1. 「 `UbuntuServer` 」を入力します。

1. 次のプロファイルを持つ最新バージョンのサーバーを選択します。
    - **[発行元]**: Canonical
    - **[オファー]**: UbuntuServer
    - **[SKU]**: 16.04-LTS

1. **[ダウンロード]** を選択します。

## <a name="add-a-custom-script-for-linux"></a>Linux 用のカスタム スクリプトを追加する

Marketplace から Kubernetes を追加します。

1. [管理ポータル](https://adminportal.local.azurestack.external)を開きます。

1. **[すべてのサービス]** を選択し、**[管理]** カテゴリで **[Marketplace management] (Marketplace 管理)** を選択します。

1. **+ Add from Azure**(+ Azure から追加) を選択します。

1. 「 `Custom Script for Linux` 」を入力します。

1. 次のプロファイルを持つスクリプトを選択します。
    - **[オファー]**: Custom Script for Linux 2.0 (Linux 2.0 用のカスタム スクリプト)
    - **バージョン**: 2.0.6
    - **[発行元]**: Microsoft Corp

    > [!Note]  
    > 複数バージョンの Linux 用カスタム スクリプトが表示されることがあります。 一致するバージョンを追加する必要があります。 Kubernetes には、正確なバージョンの項目が必要です。

1. **[ダウンロード]** を選択します。


## <a name="add-kubernetes-to-the-marketplace"></a>Kubernetes をマーケットプレースに追加する

1. [管理ポータル](https://adminportal.local.azurestack.external)を開きます。

1. **[すべてのサービス]** を選択し、**[管理]** カテゴリで **[Marketplace management] (Marketplace 管理)** を選択します。

1. **+ Add from Azure**(+ Azure から追加) を選択します。

1. 「 `Kubernetes` 」を入力します。

1. [`Kubernetes Cluster`] を選択します。

1. **[ダウンロード]** を選択します。

    > [!note]  
    > マーケットプレース項目が Marketplace に表示されるまで 5 分かかる場合があります。

    ![Kubernetes](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Kubernetes を更新または削除する 

Kubernetes 項目を更新するときには、Marketplace 内にある項目を削除する必要があります。 その後、この記事の指示に従って、Kubernetes をマーケットプレースに追加できます。

Kubernetes を削除するには:

1. 現在の項目の名前に注意してください。例: `Microsoft.AzureStackKubernetesCluster.0.2.0`

1. PowerShell を使用して Azure Stack に接続します。

1. 次の PowerShell コマンドレットを使用して項目を削除します。

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.2.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>次の手順

[Kubernetes を Azure Stack にデプロイする](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[Azure Stack でのサービスの提供の概要](azure-stack-offer-services-overview.md)