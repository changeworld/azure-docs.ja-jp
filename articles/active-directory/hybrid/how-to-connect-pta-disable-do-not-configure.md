---
title: Azure AD Connect の [構成しない] を使用して PTA を無効にする | Microsoft Docs
description: この記事では、Azure AD Connect の [構成しない] 機能を使用して PTA を無効にする方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa1046dc64fed3edb6c9d04f76a96f488769ff42
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81725764"
---
# <a name="disable-pta-when-using-azure-ad-connect-do-not-configure"></a>Azure AD Connect の [構成しない] を使用して PTA を無効にする

Azure AD Connect でパススルー認証 (PTA) を使用していて、それを [構成しない] に設定している場合は、無効にすることができます。 PTA の無効化は、次のコマンドレットを使用して行うことができます。 

## <a name="prerequisites"></a>前提条件
以下の前提条件が必要です。
- PTA エージェントがインストールされている任意の Windows マシン。 
- エージェントがバージョン 1.5.1742.0 以降であること。 
- PowerShell コマンドレットを実行して PTA を無効にするための Azure グローバル管理者アカウント。

>[!NOTE]
> エージェントが古い場合は、この操作を完了するために必要なコマンドレットがない可能性があります。 Azure portal から新しいエージェントを取得して、任意の Windows マシンにインストールし、管理者の資格情報を指定することができます (エージェントをインストールしても、クラウドの PTA ステータスには影響しません)。

> [!IMPORTANT]
> Azure Government クラウドを使用している場合は、次の値を使用して、ENVIRONMENTNAME パラメーターを渡す必要があります。 
>
>| 環境名 | クラウド |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>PTA を無効にするには
PowerShell セッション内から、次を使用して PTA を無効にします。
1. PS C:\Program Files\Microsoft Azure AD Connect Authentication Agent> `Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` または `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` または `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>エージェントへのアクセス権がない場合

エージェント マシンがない場合は、次のコマンドを使用してエージェントをインストールできます。

1. portal.azure.com から最新の認証エージェントをダウンロードします。
2. 機能 `.\AADConnectAuthAgentSetup.exe` または `.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>` をインストールします。


## <a name="next-steps"></a>次のステップ

- [Azure Active Directory パススルー認証によるユーザー サインイン](how-to-connect-pta.md)