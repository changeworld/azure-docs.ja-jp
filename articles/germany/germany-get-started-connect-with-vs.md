---
title: Visual Studio を使用した Azure Germany へのアクセス | Microsoft Docs
description: Visual Studio を使用して Azure Germany でサブスクリプションを管理する方法についての情報
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: f5749ed9d2080a92a33993d8131595359110b75e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029113"
---
# <a name="connect-to-azure-germany-by-using-visual-studio"></a>Visual Studio を使用して Azure Germany に接続する

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

開発者は、Visual Studio を使用して、ソリューションの構築時に Azure サブスクリプションを簡単に管理できます。 現時点では、Visual Studio のユーザー インターフェイスで、Azure Germany への接続を構成することはできません。

## <a name="visual-studio-2017-and-visual-studio-2019"></a>Visual Studio 2017 と Visual Studio 2019

Visual Studio で Azure Germany に接続するには、構成ファイルが必要です。 このファイルを配置すると、Visual Studio は、グローバル Azure ではなく Azure Germany に接続します。

### <a name="create-a-configuration-file-for-azure-germany"></a>Azure Germany の構成ファイルの作成

次の内容を含む *AadProvider.Configuration.json* という名前のファイルを作成します。

```json
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
```

### <a name="update-visual-studio-for-azure-germany"></a>Azure Germany の Visual Studio の更新

1. Visual Studio を閉じます。
1. *%localappdata%\\.IdentityService\AadConfigurations* に *AadProvider.Configuration.json* を配置します。 存在しない場合は、このフォルダーを作成します。
1. Visual Studio を起動し、Azure Germany アカウントの使用を開始します。

> [!NOTE]
> この構成ファイルでアクセスできるのは Azure Germany サブスクリプションのみです。 以前に構成したサブスクリプションも表示されますが、Visual Studio はグローバル Azure でなく Azure Germany に接続されているので、これらは動作しません。 グローバル Azure にアクセスするには、このファイルを削除してください。
>

### <a name="revert-a-visual-studio-connection-to-azure-germany"></a>Visual Studio の接続を Azure Germany に戻す

Visual Studio がグローバル Azure に接続できるようにするには、Azure Germany への接続を可能にする構成ファイルを削除する必要があります。

1. Visual Studio を閉じます。
1. *%localappdata%\.IdentityService\AadConfigurations* を削除するか名前を変更します。
1. Visual Studio を再起動し、自分のグローバル Azure アカウントの使用を開始します。

> [!NOTE]
> この構成を元に戻すと、Azure Germany サブスクリプションにはアクセスできなくなります。
>

## <a name="visual-studio-2015"></a>Visual Studio 2015

Visual Studio 2015 で Azure Germany に接続するには、レジストリの変更が必要です。 このレジストリ キーの設定後、Visual Studio はグローバル Azure でなく Azure Germany に接続します。

### <a name="update-visual-studio-2015-for-azure-germany"></a>Azure Germany のために Visual Studio 2015 を更新する

Visual Studio が Azure Germany に接続できるようにするには、レジストリをアップデートしてください。

1. Visual Studio を閉じます。
1. *VisualStudioForAzureGermany.reg* というテキスト ファイルを作成します。
1. 以下のテキストをコピーして *VisualStudioForAzureGermany.reg* に貼り付けます。

```
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
"AadInstance"="https://login.microsoftonline.de/"
"adaluri"="https://management.microsoftazure.de"
"AzureRMEndpoint"="https://management.microsoftazure.de"
"AzureRMAudienceEndpoint"="https://management.core.cloudapi.de"
"EnableAzureRMIdentity"="true"
"GraphUrl"="graph.cloudapi.de"
"AadApplicationTenant"="f577cd82-810c-43f9-a1f6-0cc532871050"
```

1. 保存してからファイルをダブルクリックして実行します。 ファイルをレジストリにマージするよう求めるメッセージが表示されます。
1. Visual Studio を起動して Azure Germany アカウントで [Cloud Explorer](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer) の使用を開始します。

> [!NOTE]
> このレジストリ キーが設定されると、アクセスできるのは Azure Germany サブスクリプションのみになります。 以前に構成したサブスクリプションも表示されますが、Visual Studio はグローバル Azure でなく Azure Germany に接続されているので、これらは動作しません。 グローバル Azure にアクセスするには、変更を元に戻します。
>

### <a name="revert-a-visual-studio-2015-connection-to-azure-germany"></a>Visual Studio 2015 の接続を Azure Germany に戻す

Visual Studio でグローバル Azure に接続できるようにするには、Azure Germany への接続を可能にするレジストリ設定を削除する必要があります。

1. Visual Studio を閉じます。
1. *VisualStudioForAzureGermany_Remove.reg* というテキスト ファイルを作成します。
1. 以下のテキストをコピーし *VisualStudioForAzureGermany_Remove.reg* に貼り付けます。

```
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
"AadInstance"=-
"adaluri"=-
"AzureRMEndpoint"=-
"AzureRMAudienceEndpoint"=-
"EnableAzureRMIdentity"=-
"GraphUrl"=-
```

1. 保存してからファイルをダブルクリックして実行します。 ファイルをレジストリにマージするよう求めるメッセージが表示されます。
1. Visual Studio を起動します。

> [!NOTE]
> このレジストリ キーを元に戻すと、Azure Germany サブスクリプションは表示されますが、アクセスすることはできません。 事前に安全に削除できます。
>

## <a name="next-steps"></a>次のステップ

Azure Germany への接続の詳細については、以下のリソースを参照してください。

* [PowerShell を使用して Azure Germany に接続する](./germany-get-started-connect-with-ps.md)
* [Azure CLI を使用して Azure Germany に接続する](./germany-get-started-connect-with-cli.md)
* [Azure portal を使用して Azure Germany に接続する](./germany-get-started-connect-with-portal.md)