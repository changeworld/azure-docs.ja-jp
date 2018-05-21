---
title: Azure および Azure Stack アプリケーションでハイブリッド クラウド ID を構成する | Microsoft Docs
description: Azure および Azure Stack アプリケーションでハイブリッド クラウド ID を構成する方法について説明します。
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
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 5224fe4682509f92e1f18d865e5cc5afb1218ad1
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-configure-hybrid-cloud-identity-with-azure-and-azure-stack-applications"></a>チュートリアル: Azure および Azure Stack アプリケーションでハイブリッド クラウド ID を構成する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

グローバル Azure および Azure スタックの両方でアプリケーションへのアクセスを許可するには、2 つのオプションがあります。 Azure Stack がインターネットに継続的に接続している場合、Azure Active Directory (Azure AD) を使用できます。 Azure Stack がインターネットから切断されているときは、Active Directory フェデレーション サービス (AD FS) を使用できます。 Azure Stack 内の Azure Resource Manager を使用したデプロイまたは構成のために、サービス プリンシパルを使用して、Azure Stack 内のアプリケーションへのアクセスを許可します。 

このチュートリアルでは、以下を実現するためのサンプル環境を構築します。

> [!div class="checklist"]
> * グローバル Azure および Azure Stack でハイブリッド ID を確立する
> * Azure Stack API にアクセスするためのトークンを取得する。

これらの手順を実行するには、Azure Stack オペレーター アクセス許可が必要です。

## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>ポータルで Azure AD のサービス プリンシパルを作成する

Azure AD を ID ストアとして使用して Azure Stack をデプロイした場合は、Azure での手順と同様の方法でサービス プリンシパルを作成できます。 ポータルを使用してこれらの手順を実行する方法については、[このドキュメント](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad)に説明されています。 始める前に、[Azure AD で必要なアクセス許可](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions)があることを確認してください。

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>PowerShell を使用して AD FS のサービス プリンシパルを作成する

AD FS を使用して Azure Stack をデプロイした場合は、PowerShell を使ってサービス プリンシパルを作成し、アクセスのロールを割り当てて、その ID を使用して PowerShell からサインインできます。 PowerShell を使用してこれらの手順を実行する方法については、[このドキュメント](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs)に説明されています。

## <a name="using-the-azure-stack-api"></a>Azure Stack API を使用する

Azure Stack API にアクセスするためのトークンを取得するプロセスについては、[このチュートリアル](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-rest-api-use)に説明されています。

## <a name="connect-to-azure-stack-using-powershell"></a>PowerShell を使用して Azure Stack に接続する

このドキュメントに示されている、Azure Stack に接続するための概念を使用したサンプル スクリプトを示します。

### <a name="prerequisites"></a>前提条件

アクセスできるサブスクリプションの Azure Active Directory に接続された Azure Stack インストール。 Azure Stack のインストールがない場合は、この手順に従って [Azure Stack Development Kit](https://docs.microsoft.com/en-us/azure/azure-stack/asdk/asdk-deploy) をセットアップできます。

[このチュートリアル](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-powershell-configure-quickstart)では、Azure PowerShell をインストールし、Azure Stack インストールに接続するために必要な手順について説明します。

#### <a name="connect-to-azure-stack-using-code"></a>コードを使用して Azure Stack に接続する

コードを使用して Azure Stack に接続するには、Azure Resource Manager エンドポイント API を使用して、Azure Stack インストールの認証およびグラフ エンドポイントを取得し、REST 要求を使用して認証します。 [こちら](https://github.com/shriramnat/HybridARMApplication)でサンプル アプリケーションを見つけることができます。

> [!Note]  
選択した言語の Azure SDK で Azure API プロファイルがサポートされていない限り、SDK は Azure Stack で動作しません。 Azure API プロファイルの詳細については、[このページ](https://docs.microsoft.com/da-dk/azure/azure-stack/user/azure-stack-version-profiles)を参照してください。

## <a name="next-steps"></a>次の手順

 - Azure Stack での ID の処理方法の詳細については、「[Azure Stack の ID アーキテクチャ](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture)」を参照してください。  
 - Azure のクラウド パターンの詳細については、「[クラウド設計パターン](https://docs.microsoft.com/azure/architecture/patterns)」を参照してください。
