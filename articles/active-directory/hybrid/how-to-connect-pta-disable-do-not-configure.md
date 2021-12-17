---
title: Azure AD Connect の [構成しない] を使用して PTA を無効にする | Microsoft Docs
description: この記事では、Azure AD Connect の [構成しない] 機能を使用して PTA を無効にする方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e58ca9cce30c03467e7171ef29cf1e360fbe02e
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371716"
---
# <a name="disable-pta"></a>PTA を無効にする 

PTA を無効にするには、この記事の「[Azure AD Connect を使用して PTA を無効にする](#disable-pta-when-using-azure-ad-connect)」と「[PowerShell での PTA を無効にする](#disable-pta-in-powershell)」で説明されている手順を実行します。

## <a name="disable-pta-when-using-azure-ad-connect"></a>Azure AD Connect を使用して PTA を無効にする

Azure AD Connect でパススルー認証を使用していて、それを **[構成しない]** に設定している場合は、それを無効にできます。 

>[!NOTE]
>PHS が既に有効になっている場合、PTA を無効にすると、テナントのフォールバックが PHS になります。

PTA の無効化は、次のコマンドレットを使用して行うことができます。 

## <a name="prerequisites"></a>前提条件
以下の前提条件が必要です。
- PTA エージェントがインストールされている任意の Windows マシン。 
- エージェントがバージョン 1.5.1742.0 以降であること。 
- PowerShell コマンドレットを実行して PTA を無効にするための Azure グローバル管理者アカウント。

>[!NOTE]
> エージェントが古い場合は、この操作を完了するために必要なコマンドレットがない可能性があります。 Azure portal から新しいエージェントを取得し、任意の Windows マシンにインストールし、管理者の資格情報を指定できます。 (エージェントをインストールしても、クラウドの PTA ステータスには影響しません)。

> [!IMPORTANT]
> Azure Government クラウドを使用している場合は、次の値を使用して、ENVIRONMENTNAME パラメーターを渡す必要があります。 
>
>| 環境名 | クラウド |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="disable-pta-in-powershell"></a>PowerShell での PTA を無効にする

PowerShell セッション内から、次を使用して PTA を無効にします。

1. PS C:\Program Files\Microsoft Azure AD Connect Authentication Agent> `Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus`
3. `Disable-PassthroughAuthentication`

## <a name="if-you-dont-have-access-to-an-agent"></a>エージェントへのアクセス権がない場合

エージェント マシンがない場合は、次のコマンドを使用してエージェントをインストールできます。

1. portal.azure.com から最新の認証エージェントをダウンロードします。
2. 機能 `.\AADConnectAuthAgentSetup.exe` または `.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>` をインストールします。


## <a name="next-steps"></a>次のステップ

- [Azure Active Directory パススルー認証によるユーザー サインイン](how-to-connect-pta.md)
