---
title: PowerShell を使用した RDP プロパティのカスタマイズ - Azure
description: PowerShell コマンドレットを使用して Windows Virtual Desktop 用の RDP プロパティをカスタマイズする方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4a0f193437353bac1f5998b50b9d7b4d43bedefa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128065"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>ホスト プールのリモート デスクトップ プロトコル プロパティをカスタマイズする

マルチ モニター エクスペリエンスやオーディオ リダイレクトなど、ホスト プールのリモート デスクトップ プロトコル (RDP) のプロパティをカスタマイズすると、ニーズに基づいてユーザーに最適なエクスペリエンスを提供できます。 **Set-RdsHostPool** コマンドレットの **-CustomRdpProperty** パラメーターを使用して、Windows Virtual Desktop の RDP プロパティをカスタマイズできます。

サポートされているプロパティとその既定値の全リストについては、「[サポートされるリモート デスクトップ RDP ファイルの設定](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context)」を参照してください。

まず、まだ行っていない場合は、PowerShell セッション内で使用する [Windows Virtual Desktop PowerShell モジュールをダウンロードしてインポート](/powershell/windows-virtual-desktop/overview/)します。 その後、次のコマンドレットを実行して、ご自分のアカウントにサインインします。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>既定の RDP のプロパティ

既定では、パブリッシュされた RDP ファイルには次のプロパティが含まれています。

|RDP のプロパティ | デスクトップ | RemoteApps |
|---|---| --- |
| マルチモニター モード | 有効 | 該当なし |
| ドライブ リダイレクト有効 | ドライブ、クリップボード、プリンター、COM ポート、USB デバイス、スマートカード| ドライブ、クリップボード、プリンター |
| リモート オーディオ モード | ローカルで再生 | ローカルで再生 |

ホスト プールに対して定義したカスタム プロパティは、これらの既定値よりも優先されます。

## <a name="add-or-edit-a-single-custom-rdp-property"></a>単一のカスタム RDP プロパティを追加または編集する

単一のカスタム RDP プロパティを追加または編集するには、次の PowerShell コマンドレットを実行します。

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

![Name と FriendlyName が強調表示された PowerShell コマンドレット Get-RDSRemoteApp のスクリーンショット。](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>複数のカスタム RDP プロパティを追加または編集する

複数のカスタム RDP プロパティを追加または編集するには、カスタム RDP プロパティをセミコロンで区切った文字列として指定して、次の PowerShell コマンドレットを実行します。

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

![Name と FriendlyName が強調表示された PowerShell コマンドレット Get-RDSRemoteApp のスクリーンショット。](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>すべてのカスタム RDP プロパティをリセットする

「[単一のカスタム RDP プロパティを追加または編集する](#add-or-edit-a-single-custom-rdp-property)」の手順に従って個々のカスタム RDP プロパティを既定値にリセットすることも、次の PowerShell コマンドレットを実行してホスト プールのすべてのカスタム RDP プロパティをリセットすることもできます。

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

![Name と FriendlyName が強調表示された PowerShell コマンドレット Get-RDSRemoteApp のスクリーンショット。](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>次のステップ

特定のホスト プールの RDP プロパティをカスタマイズしたので、Windows Virtual Desktop クライアントにサインインして、それらをユーザー セッションの一部としてテストできます。 次の 2 つの手順では、任意のクライアントを使用してセッションに接続する方法を説明します。

- [Windows デスクトップ クライアントを使用して接続する](connect-windows-7-and-10.md)
- [Web クライアントに接続する](connect-web.md)
