---
title: PowerShell を使用した RDP プロパティのカスタマイズ - Azure
description: PowerShell コマンドレットを使用して Windows Virtual Desktop 用の RDP プロパティをカスタマイズする方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 66b76fcdd9729b2a92ea2d561c740dbe148e0bbe
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611553"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>ホスト プールのリモート デスクトップ プロトコル プロパティをカスタマイズする

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトと Spring 2020 更新プログラムの組み合わせに適用されます。 Azure Resource Manager オブジェクトなしで Windows Virtual Desktop Fall 2019 リリースを使用している場合は、[この記事](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md)を参照してください。
>
> Windows Virtual Desktop Spring 2020 更新プログラムは現在、パブリック プレビュー段階です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

マルチ モニター エクスペリエンスやオーディオ リダイレクトなど、ホスト プールのリモート デスクトップ プロトコル (RDP) のプロパティをカスタマイズすると、ニーズに基づいてユーザーに最適なエクスペリエンスを提供できます。 Azure portal を使用するか、**Update-AzWvdHostPool** コマンドレットで *-CustomRdpProperty* パラメーターを使用して、Windows Virtual Desktop の RDP プロパティをカスタマイズできます。

サポートされているプロパティとその既定値の全リストについては、「[サポートされるリモート デスクトップ RDP ファイルの設定](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context)」を参照してください。

## <a name="prerequisites"></a>前提条件

作業を開始する前に、[Windows Virtual Desktop PowerShell モジュールの設定](powershell-module.md)に関するページの手順に従って、PowerShell モジュールを設定し、Azure にサインインしてください。

## <a name="default-rdp-properties"></a>既定の RDP のプロパティ

既定では、パブリッシュされた RDP ファイルには次のプロパティが含まれています。

|RDP のプロパティ | デスクトップ | RemoteApps |
|---|---| --- |
| マルチモニター モード | Enabled | 該当なし |
| ドライブ リダイレクト有効 | ドライブ、クリップボード、プリンター、COM ポート、USB デバイス、スマートカード| ドライブ、クリップボード、プリンター |
| リモート オーディオ モード | ローカルで再生 | ローカルで再生 |

ホスト プールに対して定義したカスタム プロパティは、これらの既定値よりも優先されます。

## <a name="configure-rdp-properties-in-the-azure-portal"></a>Azure portal で RDP プロパティを構成する

Azure portal で RDP プロパティを構成するには

1. <https://portal.azure.com> で Azure にサインインします。
2. 検索バーに「**windows virtual desktop**」と入力します。
3. サービスの下で **[Windows Virtual Desktop]** を選択します。
4. [Windows Virtual Desktop] ページで、画面の左側にあるメニューの **[ホスト プール]** を選択します。
5. 更新する**ホスト プールの名前**を選択します。
6. 画面の左側にあるメニューで **[プロパティ]** を選択します。
7. RDP のプロパティの編集を開始するには、 **[RDP 設定]** を選択します。
8. 完了したら、 **[保存]** を選択して変更を保存します。

RDP 設定メニューに表示されない編集する設定がある場合は、PowerShell でコマンドレットを実行して手動で編集する必要があります。 次のセクションでは、PowerShell でカスタム RDP プロパティを手動で編集する方法について説明します。

## <a name="add-or-edit-a-single-custom-rdp-property"></a>単一のカスタム RDP プロパティを追加または編集する

単一のカスタム RDP プロパティを追加または編集するには、次の PowerShell コマンドレットを実行します。

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty <property>
```

実行したコマンドレットでプロパティを更新したかどうかを確認するには、次のコマンドレットを実行します。

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

たとえば、0301HP という名前のホスト プールで "audiocapturemode" プロパティを確認している場合は、次のコマンドレットを入力します。

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;
```

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>複数のカスタム RDP プロパティを追加または編集する

複数のカスタム RDP プロパティを追加または編集するには、カスタム RDP プロパティをセミコロンで区切った文字列として指定して、次の PowerShell コマンドレットを実行します。

```powershell
$properties="<property1>;<property2>;<property3>" 
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty $properties 
```

次のコマンドレットを実行して、RDP プロパティが追加されたことを確認できます。

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty 

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

前のコマンドレットの例に基づいて、0301HP ホスト プールに複数の RDP プロパティを設定した場合、コマンドレットは次のようになります。

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty 

Name              : 0301HP 
CustomRdpProperty : audiocapturemode:i:1;audiomode:i:0;
```

## <a name="reset-all-custom-rdp-properties"></a>すべてのカスタム RDP プロパティをリセットする

「[単一のカスタム RDP プロパティを追加または編集する](#add-or-edit-a-single-custom-rdp-property)」の手順に従って個々のカスタム RDP プロパティを既定値にリセットすることも、次の PowerShell コマンドレットを実行してホスト プールのすべてのカスタム RDP プロパティをリセットすることもできます。

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty ""
```

設定が正常に削除されたことを確認するには、次のコマンドレットを入力します。

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty 

Name              : <hostpoolname> 
CustomRdpProperty : <CustomRDPpropertystring>
```

## <a name="next-steps"></a>次のステップ

特定のホスト プールの RDP プロパティをカスタマイズしたので、Windows Virtual Desktop クライアントにサインインして、それらをユーザー セッションの一部としてテストできます。 次の攻略ガイドでは、選択したクライアントを使用してセッションに接続する方法を説明します。

- [Windows デスクトップ クライアントを使用して接続する](connect-windows-7-and-10.md)
- [Web クライアントに接続する](connect-web.md)
- [Android クライアントに接続する](connect-android.md)
- [macOS クライアントに接続する](connect-macos.md)
- [iOS クライアントに接続する](connect-ios.md)