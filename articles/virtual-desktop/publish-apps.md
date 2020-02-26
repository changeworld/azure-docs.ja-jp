---
title: Windows Virtual Desktop で組み込みアプリを発行する - Azure
description: Windows Virtual Desktop で組み込みアプリを発行する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: helohr
ms.openlocfilehash: e8e8512fa9f6a64fb0db688b7badcb699f6ccaa5
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484623"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Windows Virtual Desktop で組み込みアプリを発行する

この記事では、Windows Virtual Desktop 環境でアプリを発行する方法について説明します。

## <a name="publish-built-in-apps"></a>組み込みアプリの公開

組み込みアプリを発行するには:

1. ホスト プール内のいずれかの仮想マシンに接続します。
2. [この記事](/powershell/module/appx/get-appxpackage?view=win10-ps/)の手順に従って、発行するアプリの **PackageFamilyName** を取得します。
3. 最後に、`<PackageFamilyName>` を前の手順で確認した **PackageFamilyName** に置き換えて、次のコマンドレットを実行します。
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Windows Virtual Desktop は、インストール場所が `C:\Program Files\Windows Apps` で始まるアプリの発行のみをサポートします。

## <a name="update-app-icons"></a>アプリ アイコンの更新

アプリを発行すると、通常のアイコン画像ではなく、既定の Windows アプリ アイコンが表示されます。 アイコンを通常のアイコンに変更するには、目的のアイコンの画像をネットワーク共有に配置します。 サポートされているイメージ形式は、PNG、BMP、GIF、JPG、JPEG、および ICO です。

## <a name="publish-microsoft-edge"></a>Microsoft Edge の発行

Microsoft Edge の発行に使用するプロセスは、他のアプリの発行プロセスとは少し異なります。 既定のホーム ページを使用して Microsoft Edge を発行するには、次のコマンドレットを実行します。

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## <a name="next-steps"></a>次のステップ

- フィードを構成して、ユーザーのためにアプリを表示する方法を整理する方法について、「[Windows Virtual Desktop ユーザー用のフィードをカスタマイズする](customize-feed-for-virtual-desktop-users.md)」を参照してください。
- MSIX アプリのアタッチ機能について、「[MSIX アプリのアタッチを設定する](app-attach.md)」を参照してください。

