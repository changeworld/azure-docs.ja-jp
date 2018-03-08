---
title: "Visual Studio をインストールして Azure Stack に接続する | Microsoft Docs"
description: "Visual Studio をインストールして Azure Stack に接続するために必要な手順を説明します。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 5eae2553af1e41ace878a2f8f2c1a1656c63a7c4
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2018
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Visual Studio をインストールして Azure Stack に接続する

*適用先: Azure Stack 統合システムと Azure Stack Development Kit*

Visual Studio を使用して、Azure Resource Manager [テンプレート](azure-stack-arm-templates.md)を作成し、それを Azure Stack にデプロイします。 この記事の手順に従って、[Azure Stack Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) を使用するか、[VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) 経由で接続している場合は Windows ベースの外部クライアントを使用して、Visual Studio をインストールできます。 これらの手順を実行すると、Visual Studio 2015 Community Edition が新規にインストールされます。 他の Visual Studio バージョンとの[共存](https://msdn.microsoft.com/library/ms246609.aspx)に関する記事も参照してください。

## <a name="install-visual-studio"></a>Visual Studio のインストール
1. [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) をダウンロードして実行します。             
2. **Visual Studio Community 2015 with Microsoft Azure SDK - 2.9.6** を探して **[追加]** をクリックし、**[インストール]** をクリックします。

    ![WebPI のインストール手順を示すスクリーンショット](./media/azure-stack-install-visual-studio/image1.png) 

3. Azure SDK の一部としてインストールされている **Microsoft Azure PowerShell** をアンインストールします。

    ![Azure PowerShell のプログラムの追加と削除のインターフェイスのスクリーンショット](./media/azure-stack-install-visual-studio/image2.png) 

4. [PowerShell for Azure Stack のインストール](azure-stack-powershell-install.md)

5. インストールが完了したら、オペレーティング システムを再起動します。

## <a name="connect-to-azure-stack"></a>Azure Stack への接続

1. Visual Studio を起動します。

2. **[表示]** メニューの **[Cloud Explorer]** を選択します。

3. 新しいウィンドウで **[アカウントの追加]** を選択し、Azure Active Directory の資格情報を使ってサインインします。  
    ![ログインして Azure Stack に接続した後の Cloud Explorer のスクリーンショット](./media/azure-stack-install-visual-studio/image6.png)

ログインしたら、[テンプレートをデプロイ](azure-stack-deploy-template-visual-studio.md)するか、使用可能なリソースの種類やリソース グループを参照して独自のテンプレートを作成することができます。  

## <a name="next-steps"></a>次の手順

 - [Azure Stack のテンプレートの開発](azure-stack-develop-templates.md)
