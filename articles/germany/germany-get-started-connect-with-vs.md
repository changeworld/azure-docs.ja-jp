---
title: Visual Studio を使用した Azure Germany へのアクセス | Microsoft Docs
description: Visual Studio を使用して Azure Germany でサブスクリプションを管理する方法についての情報
services: germany
cloud: na
documentationcenter: na
author: gitralf
manager: rainerst
ms.assetid: na
ms.service: germany
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2017
ms.author: ralfwi
ms.openlocfilehash: 9d7131d7ce6a8fd86ba2f154b8f7ca9db8afc3ee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61369956"
---
# <a name="connect-to-azure-germany-by-using-visual-studio"></a>Visual Studio を使用して Azure Germany に接続する
開発者は、Visual Studio を使用して、ソリューションの構築時に Azure サブスクリプションを簡単に管理できます。 現時点では、Visual Studio のユーザー インターフェイスから Azure Germany への接続を構成することはできません。  

## <a name="visual-studio-2017"></a>Visual Studio 2017
Visual Studio 2017 で Azure Germany に接続するには Visual Studio の構成ファイルが必要です。 このファイルを配置すると、Visual Studio は、グローバル Azure ではなく Azure Germany に接続します。

### <a name="create-a-configuration-file-for-azure-germany"></a>Azure Germany の構成ファイルの作成
次の内容を含む **AadProvider.Configuration.json** という名前のファイルを作成します。

        {
          "AuthenticationQueryParameters":null,
          "AsmEndPoint":"https://management.microsoftazure.de/",
          "Authority":"https://login.microsoftonline.de/",
          "AzureResourceManagementEndpoint":"https://management.microsoftazure.de/",
          "AzureResourceManagementAudienceEndpoints":["https://management.core.cloudapi.de/"],
          "ClientIdentifier":"872cd9fa-d31f-45e0-9eab-6e460a02d1f1",
          "EnvironmentName":"BlackForest",
          "GraphEndpoint":"https://graph.cloudapi.de",
          "MsaHomeTenantId":"f577cd82-810c-43f9-a1f6-0cc532871050",
          "NativeClientRedirect":"urn:ietf:wg:oauth:2.0:oob",
          "PortalEndpoint":"https://portal.core.cloudapi.de/",
          "ResourceEndpoint":"https://management.microsoftazure.de/",
          "ValidateAuthority":true,
          "VisualStudioOnlineEndpoint":"https://app.vssps.visualstudio.com/",
          "VisualStudioOnlineAudience":"499b84ac-1321-427f-aa17-267ca6975798"
        }

### <a name="update-visual-studio-for-azure-germany"></a>Azure Germany の Visual Studio の更新

1.  Visual Studio を閉じます。
2.  **%localappdata%\\.IdentityService\AadConfigurations** に **AadProvider.Configuration.json** を配置します。 存在しない場合は、このフォルダーを作成します。
3.  Visual Studio を起動し、Azure Germany アカウントの使用を開始します。

> [!NOTE]
> この構成ファイルでアクセスできるのは Azure Germany サブスクリプションのみです。 以前にこ得制したサブスクリプションも表示されますが、Visual Studio はグローバル Azure でなく Azure Germany に接続されているので、これらは動作しません。 グローバル Azure にアクセスするには、このファイルを削除してください。
> 
> 

### <a name="revert-a-visual-studio-connection-to-azure-germany"></a>Visual Studio の接続を Azure Germany に戻す
Visual Studio がグローバル Azure に接続できるようにするには、Azure Germany への接続を可能にする構成ファイルを削除する必要があります。

1.  Visual Studio を閉じます。
2.  **%localappdata%\.IdentityService\AadConfigurations** を削除するか名前を変更します。
3.  Visual Studio を再起動し、自分のグローバル Azure アカウントの使用を開始します。

> [!NOTE]
> この構成が元に戻ったら、Azure Germany サブスクリプションにはアクセスできなくなります。
> 
>

## <a name="visual-studio-2015"></a>Visual Studio 2015
Visual Studio 2015 では、Azure Germany に接続するにはレジストリを変更する必要があります。 このレジストリ キーが設定されると、Visual Studio はグローバル Azure でなく Azure Germany に接続します。

### <a name="update-visual-studio-for-azure-germany"></a>Azure Germany の Visual Studio の更新
Visual Studio が Azure Germany に接続できるようにするには、レジストリをアップデートしてください。

1. Visual Studio を閉じます。
2. **VisualStudioForAzureGermany.reg** というテキスト ファイルを作成します。
3. 以下のテキストをコピーして **VisualStudioForAzureGermany.reg** に貼り付けます。
   
        Windows Registry Editor Version 5.00
 
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"="https://login.microsoftonline.de/"
        "adaluri"="https://management.microsoftazure.de"
        "AzureRMEndpoint"="https://management.microsoftazure.de"
        "AzureRMAudienceEndpoint"="https://management.core.cloudapi.de"
        "EnableAzureRMIdentity"="true"
        "GraphUrl"="graph.cloudapi.de"
        "AadApplicationTenant"="f577cd82-810c-43f9-a1f6-0cc532871050"

4. 保存してからファイルをダブルクリックして実行します。 ファイルをレジストリにマージするよう求めるメッセージが表示されます。
5. Visual Studio を起動して Azure Germany アカウントで [Cloud Explorer](../vs-azure-tools-resources-managing-with-cloud-explorer.md) の使用を開始します。

> [!NOTE]
> このレジストリ キーが設定されると、アクセスできるのは Azure Germany サブスクリプションのみになります。 以前に構成したサブスクリプションも表示されますが、Visual Studio はグローバル Azure でなく Azure Germany に接続されているので、これらは動作しません。 グローバル Azure にアクセスするには、変更を元に戻します。
> 
> 

### <a name="revert-a-visual-studio-connection-to-azure-germany"></a>Visual Studio の接続を Azure Germany に戻す
Visual Studio でグローバル Azure に接続できるようにするには、Azure Germany への接続を可能にするレジストリ設定を削除する必要があります。

1. Visual Studio を閉じます。
2. **VisualStudioForAzureGermany_Remove.reg** というテキスト ファイルを作成します。
3. 以下のテキストをコピーし **VisualStudioForAzureGermany_Remove.reg** に貼り付けます。
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"=-
        "adaluri"=-
        "AzureRMEndpoint"=-
        "AzureRMAudienceEndpoint"=-
        "EnableAzureRMIdentity"=-
        "GraphUrl"=-
        
4. 保存してからファイルをダブルクリックして実行します。 ファイルをレジストリにマージするよう求めるメッセージが表示されます。
5. Visual Studio を起動します。

> [!NOTE]
> このレジストリ キーを元に戻すと、Azure Germany サブスクリプションは表示されますがアクセスできません。 事前に安全に削除できます。
> 
> 

## <a name="next-steps"></a>次の手順
Azure Germany への接続の詳細については、以下のリソースを参照してください。

* [PowerShell を使用して Azure Germany に接続する](./germany-get-started-connect-with-ps.md)
* [Azure CLI を使用して Azure Germany に接続する](./germany-get-started-connect-with-cli.md)
* [Azure portal を使用して Azure Germany に接続する](./germany-get-started-connect-with-portal.md)





