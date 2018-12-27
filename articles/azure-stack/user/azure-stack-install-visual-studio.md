---
title: Visual Studio をインストールして Azure Stack に接続する | Microsoft Docs
description: Visual Studio をインストールして Azure Stack に接続するために必要な手順を説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 2afbea68c017805e9bd7db43b03face0705608b7
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2018
ms.locfileid: "42358749"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Visual Studio をインストールして Azure Stack に接続する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Visual Studio を使用して Azure Resource Manager [テンプレート](azure-stack-arm-templates.md)を作成し、Azure Stack にデプロイできます。 この記事では、Visual Studio を [Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) にインストールする手順、または [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) を介して Azure Stack を使用する予定の場合に外部コンピューターにインストールする手順について説明します。

## <a name="install-visual-studio"></a>Visual Studio のインストール

1. [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) をダウンロードして実行します。  

2. **Microsoft Web Platform Installer** を開きます。

3. **Visual Studio Community 2015 with Microsoft Azure SDK - 2.9.6** を検索します。 **[追加]**、**[インストール]** の順にクリックします。

4. Azure SDK の一部としてインストールされている **Microsoft Azure PowerShell** をアンインストールします。

    ![WebPI のインストール手順を示すスクリーンショット](./media/azure-stack-install-visual-studio/image1.png) 

5. [PowerShell for Azure Stack のインストール](azure-stack-powershell-install.md)

6. インストールが完了したら、オペレーティング システムを再起動します。

## <a name="connect-to-azure-stack-with-azure-ad"></a>Azure AD を使用して Azure Stack に接続する

1. Visual Studio を起動します。

2. **[表示]** メニューの **[Cloud Explorer]** を選択します。

3. 新しいウィンドウで **[アカウントの追加]** を選択し、Azure Active Directory (Azure AD) の資格情報を使ってサインインします。  

    ![ログインして Azure Stack に接続した後の Cloud Explorer のスクリーンショット](./media/azure-stack-install-visual-studio/image2.png)

ログインしたら、[テンプレートをデプロイ](azure-stack-deploy-template-visual-studio.md)するか、使用可能なリソースの種類やリソース グループを参照して独自のテンプレートを作成することができます。  

## <a name="connect-to-azure-stack-with-ad-fs"></a>AD FS を使用して Azure Stack に接続する

1. Visual Studio を起動します。

2. **[ツール]** から **[オプション]** を選択します。

3. **ナビゲーション ウィンドウ**の **[環境]** を展開し、**[アカウント]** を選択します。

4. **[追加]** を選択し、User Azure Resource Manger エンドポイントを入力します。  
  Azure Stack Development Kit の URL は `https://management.local.azurestack/external` です。  
  Azure Stack 統合システムの URL は `https://management.[Region}.[External FQDN]` です。

    ![○](./media/azure-stack-install-visual-studio/image5.png)

5. **[追加]** を選択します。  

    Visual Studio は Azure Resource Manger を呼び出し、Azure Directory Federated Services (AD FS) の認証エンドポイントを含むエンドポイントを検出します。

    ![ログインして Azure Stack に接続した後の Cloud Explorer のスクリーンショット](./media/azure-stack-install-visual-studio/image6.png)

6. **[表示]** メニューの **[Cloud Explorer]** を選択します。
7. **[アカウントの追加]** を選択し、AD FS の資格情報でサインインします。  

    ![○](./media/azure-stack-install-visual-studio/image7.png)

    Cloud Explorer は使用できるサブスクリプションのクエリを実行します。 使用できるサブスクリプションを 1 つ選択して管理できます。

    ![○](./media/azure-stack-install-visual-studio/image8.png)

8. 既存のリソース、リソース グループ、またはデプロイ テンプレートを参照します。

## <a name="next-steps"></a>次の手順

 - 他の Visual Studio バージョンとの[共存](https://msdn.microsoft.com/library/ms246609.aspx)に関する記事も参照してください。
 - [Azure Stack のテンプレートの開発](azure-stack-develop-templates.md)