---
title: Azure Virtual Desktop (クラシック) で組み込みアプリを発行する - Azure
description: Azure Virtual Desktop (クラシック) で組み込みアプリを発行する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 0a375f61098a1000b101be74d137fabd023cf4d5
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111751777"
---
# <a name="publish-built-in-apps-in-azure-virtual-desktop-classic"></a>Azure Virtual Desktop (クラシック) で組み込みアプリを発行する

>[!IMPORTANT]
>この内容は、Azure Resource Manager Azure Virtual Desktop オブジェクトをサポートしていない Azure Virtual Desktop (クラシック) に適用されます。 Azure Resource Manager Azure Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../publish-apps.md)を参照してください。

この記事では、Azure Virtual Desktop 環境でアプリを発行する方法について説明します。

## <a name="publish-built-in-apps"></a>組み込みアプリの公開

組み込みアプリを発行するには:

1. ホスト プール内のいずれかの仮想マシンに接続します。
2. [この記事](/powershell/module/appx/get-appxpackage)の手順に従って、発行するアプリの **PackageFamilyName** を取得します。
3. 最後に、`<PackageFamilyName>` を前の手順で確認した **PackageFamilyName** に置き換えて、次のコマンドレットを実行します。

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Azure Virtual Desktop は、インストール場所が `C:\Program Files\Windows Apps` で始まるアプリの発行のみをサポートします。

## <a name="update-app-icons"></a>アプリ アイコンの更新

アプリを発行すると、通常のアイコン画像ではなく、既定の Windows アプリ アイコンが表示されます。 アイコンを通常のアイコンに変更するには、目的のアイコンの画像をネットワーク共有に配置します。 サポートされているイメージ形式は、PNG、BMP、GIF、JPG、JPEG、および ICO です。

## <a name="publish-microsoft-edge"></a>Microsoft Edge の発行

Microsoft Edge の発行に使用するプロセスは、他のアプリの発行プロセスとは少し異なります。 既定のホーム ページを使用して Microsoft Edge を発行するには、次のコマンドレットを実行します。

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge"
```

## <a name="next-steps"></a>次のステップ

- フィードを構成して、ユーザーのためにアプリを表示する方法を整理する方法について、「[Azure Virtual Desktop ユーザー用のフィードをカスタマイズする](customize-feed-virtual-desktop-users-2019.md)」を参照してください。
- MSIX アプリのアタッチ機能について、「[MSIX アプリのアタッチを設定する](../app-attach.md)」を参照してください。

