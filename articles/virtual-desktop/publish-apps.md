---
title: Windows Virtual Desktop で組み込みアプリを発行する - Azure
description: Windows Virtual Desktop で組み込みアプリを発行する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3a238a5ae282d5059eee23b4bcaa86a3e84b6656
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82863299"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Windows Virtual Desktop で組み込みアプリを発行する

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトと Spring 2020 更新プログラムの組み合わせに適用されます。 Azure Resource Manager オブジェクトなしで Windows Virtual Desktop Fall 2019 リリースを使用している場合は、[この記事](./virtual-desktop-fall-2019/publish-apps-2019.md)を参照してください。
>
> Windows Virtual Desktop Spring 2020 更新プログラムは現在、パブリック プレビュー段階です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、Windows Virtual Desktop 環境でアプリを発行する方法について説明します。

## <a name="publish-built-in-apps"></a>組み込みアプリの公開

組み込みアプリを発行するには:

1. ホスト プール内のいずれかの仮想マシンに接続します。
2. [この記事](/powershell/module/appx/get-appxpackage?view=win10-ps/)の手順に従って、発行するアプリの **PackageFamilyName** を取得します。
3. 最後に、`<PackageFamilyName>` を前の手順で確認した **PackageFamilyName** に置き換えて、次のコマンドレットを実行します。
   
   ```powershell
   New-AzWvdApplication -Name <applicationname> -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -FilePath "shell:appsFolder\<PackageFamilyName>!App" -CommandLineSetting <Allow|Require|DoNotAllow> -IconIndex 0 -IconPath <iconpath> -ShowInPortal:$true
   ```

>[!NOTE]
> Windows Virtual Desktop は、インストール場所が `C:\Program Files\WindowsApps` で始まるアプリの発行のみをサポートします。

## <a name="update-app-icons"></a>アプリ アイコンの更新

アプリを発行すると、通常のアイコン画像ではなく、既定の Windows アプリ アイコンが表示されます。 アイコンを通常のアイコンに変更するには、目的のアイコンの画像をネットワーク共有に配置します。 サポートされているイメージ形式は、PNG、BMP、GIF、JPG、JPEG、および ICO です。

## <a name="publish-microsoft-edge"></a>Microsoft Edge の発行

Microsoft Edge の発行に使用するプロセスは、他のアプリの発行プロセスとは少し異なります。 既定のホーム ページを使用して Microsoft Edge を発行するには、次のコマンドレットを実行します。

```powershell
New-AzWvdApplication -Name -ResourceGroupName -ApplicationGroupName -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" -CommandLineSetting <Allow|Require|DoNotAllow> -iconPath "C:\Windows\SystemApps\Microsoft.MicrosoftEdge_8wekyb3d8bbwe\microsoftedge.exe" -iconIndex 0 -ShowInPortal:$true 
```

## <a name="next-steps"></a>次のステップ

- フィードを構成して、ユーザーのためにアプリを表示する方法を整理する方法について、「[Windows Virtual Desktop ユーザー用のフィードをカスタマイズする](customize-feed-for-virtual-desktop-users.md)」を参照してください。
- MSIX アプリのアタッチ機能について、「[MSIX アプリのアタッチを設定する](app-attach.md)」を参照してください。

