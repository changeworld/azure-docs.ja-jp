---
title: Azure Virtual Desktop ユーザー用のフィードをカスタマイズする - Azure
description: PowerShell コマンドレットで Azure Virtual Desktop ユーザー用のフィードをカスタマイズする方法。
author: Heidilohr
ms.topic: how-to
ms.date: 09/14/2019
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: d2a1e8df23bca2e57f2d26bd83fc06ad93847903
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2021
ms.locfileid: "113032941"
---
# <a name="customize-the-feed-for-azure-virtual-desktop-users"></a>Azure Virtual Desktop ユーザー用のフィードをカスタマイズする

>[!IMPORTANT]
>この内容は、Azure Resource Manager Azure Virtual Desktop オブジェクトを含む Azure Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトを含まない Azure Virtual Desktop (クラシック) を使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/customize-feed-virtual-desktop-users-2019.md)を参照してください。

フィードは、RemoteApp とリモート デスクトップのリソースがユーザーの認識可能な方法で表示されるようにカスタマイズすることができます。

## <a name="prerequisites"></a>前提条件

この記事では、Azure Virtual Desktop PowerShell モジュールを既にダウンロードしてインストールしていることを前提としています。 まだ行っていない場合は、[PowerShell モジュールのセットアップ](powershell-module.md)に関する記事の手順に従ってください。

## <a name="customize-the-display-name-for-a-remoteapp"></a>RemoteApp の表示名をカスタマイズする

公開された RemoteApp の表示名は、フレンドリ名を設定することで変更できます。 既定では、フレンドリ名は RemoteApp プログラムの名前と同じです。

アプリ グループの公開された Remoteapp の一覧を取得するには、次の PowerShell コマンドレットを実行します。

```powershell
Get-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname>
```

RemoteApp にフレンドリ名を割り当てるには、必要なパラメーターを指定して次のコマンドレットを実行します。

```powershell
Update-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

たとえば、次のコマンドレット例を使用して現在のアプリケーションを取得したとします。

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list
```

出力は次のようになります。

```powershell
CommandLineArgument :
CommandLineSetting  : DoNotAllow
Description         :
FilePath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
FriendlyName        : Microsoft Word
IconContent         : {0, 0, 1, 0…}
IconHash            : --iom0PS6XLu-EMMlHWVW3F7LLsNt63Zz2K10RE0_64
IconIndex           : 0
IconPath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
Id                  : /subscriptions/<subid>/resourcegroups/0301RG/providers/Microsoft.DesktopVirtualization/applicationgroups/0301RAG/applications/Microsoft Word
Name                : 0301RAG/Microsoft Word
ShowInPortal        : False
Type                : Microsoft.DesktopVirtualization/applicationgroups/applications
```
フレンドリ名を更新するには、次のコマンドレットを実行します。

```powershell
Update-AzWvdApplication -GroupName 0301RAG -Name "Microsoft Word" -FriendlyName "WordUpdate" -ResourceGroupName 0301RG -IconIndex 0 -IconPath "C:\Program Files\Windows NT\Accessories\wordpad.exe" -ShowInPortal:$true -CommandLineSetting DoNotallow -FilePath "C:\Program Files\Windows NT\Accessories\wordpad.exe"
```

フレンドリ名が正常に更新されたことを確認するには、次のコマンドレットを実行します。

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list FriendlyName
```

コマンドレットにより、次の出力が得られます。

```powershell
FriendlyName        : WordUpdate
```

## <a name="customize-the-display-name-for-a-remote-desktop"></a>リモート デスクトップの表示名をカスタマイズする

公開されたリモート デスクトップの表示名は、フレンドリ名を設定することで変更できます。 ホスト プールとデスクトップ アプリ グループを、PowerShell を使用して手動で作成した場合、既定のフレンドリ名は「セッション デスクトップ」です。 ホスト プールとデスクトップ アプリ グループを、GitHub の Azure Resource Manager テンプレートまたは Azure Marketplace のサービスを使用して作成した場合、既定のフレンドリ名はホスト プール名と同じです。

リモート デスクトップのリソースを取得するには、次の PowerShell コマンドレットを実行します。

```powershell
Get-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname>
```

リモート デスクトップのリソースにフレンドリ名を割り当てるには、次の PowerShell コマンドレットを実行します。

```powershell
Update-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

## <a name="customize-a-display-name-in-azure-portal"></a>Azure portal で表示名をカスタマイズする

公開されたリモート デスクトップの表示名は、Azure portal を使用してフレンドリ名を設定することで変更できます。

1. Azure Portal ( <https://portal.azure.com> ) にサインインします。

2. **Azure Virtual Desktop** を検索します。

3. [サービス] の下にある **[Azure Virtual Desktop]** を選択します。

4. Azure Virtual Desktop のページで、画面の左側にある **[アプリケーション グループ]** を選択し、編集するアプリ グループの名前を選択します。 (たとえば、デスクトップ アプリ グループの表示名を編集する場合、**デスクトップ** という名前のアプリ グループを選択します)。

5. 画面の左側にあるメニューで **[アプリケーション]** を選択します。

6. 更新するアプリケーションを選択した後、新しい **[表示名]** を入力します。

7. **[保存]** を選択します。 これで、編集したアプリケーションに更新された名前が表示されるようになります。

## <a name="next-steps"></a>次のステップ

ユーザーに合わせてフィードをカスタマイズしたので、Azure Virtual Desktop クライアントにサインインしてそれを試すことができます。これを行うには、Azure Virtual Desktop への接続方法に関する記事に進みます。

 * [Windows 10 または Windows 7 を使用して接続する](./user-documentation/connect-windows-7-10.md)
 * [Web クライアントに接続する](./user-documentation/connect-web.md)
 * [Android クライアントに接続する](./user-documentation/connect-android.md)
 * [iOS クライアントに接続する](./user-documentation/connect-ios.md)
 * [macOS クライアントに接続する](./user-documentation/connect-macos.md)
