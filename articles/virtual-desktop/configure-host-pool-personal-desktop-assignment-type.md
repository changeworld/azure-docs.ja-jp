---
title: Windows Virtual Desktop の個人用デスクトップの割り当ての種類 - Azure
description: Windows Virtual Desktop の個人用デスクトップ ホスト プールの割り当ての種類を構成する方法。
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
ms.openlocfilehash: 8065be841b18fa0f8706a3bea3739ee2cb013323
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367591"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>個人用デスクトップ ホスト プールの割り当ての種類を構成する

ニーズに合わせて、個人用デスクトップ ホスト プールの割り当ての種類を構成して Windows Virtual Desktop 環境を調整することができます。 このトピックでは、ユーザーの自動割り当てまたは直接割り当てを構成する方法について説明します。

>[!NOTE]
> この記事の手順は、プールされたホスト プールではなく個人用デスクトップ ホスト プールにのみ適用されます。これは、プールされたホスト プール内のユーザーは特定のセッション ホストに割り当てられないためです。

## <a name="configure-automatic-assignment"></a>自動割り当てを構成する

自動割り当ては、Windows Virtual Desktop 環境で作成された新しい個人用デスクトップ ホスト プールの既定の割り当ての種類です。 ユーザーを自動的に割り当てる場合、特定のセッション ホストは必要ありません。

ユーザーを自動的に割り当てるには、まずユーザーを個人用デスクトップ ホスト プールに割り当てて、ユーザーが自分のフィードでデスクトップを表示できるようにします。 割り当てられたユーザーが自分のフィードでデスクトップを起動すると、ホスト プールにまだ接続していない場合は、使用可能なセッション ホストが要求されます。これにより、割り当てプロセスが完了します。

作業を開始する前に、[Windows Virtual Desktop PowerShell モジュールをダウンロードしてインポート](/powershell/windows-virtual-desktop/overview/)します (まだの場合のみ)。 

> [!NOTE]
> これらの手順を実行する前に、Windows Virtual Desktop PowerShell モジュール バージョン 1.0.1534.2001 以降がインストールされていることを確認してください。

その後、次のコマンドレットを実行して、ご自分のアカウントにサインインします。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

ユーザーを VM に自動的に割り当てるようにホスト プールを構成するには、次の PowerShell コマンドレットを実行します。

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

ユーザーを個人用デスクトップ ホスト プールに割り当てるには、次の PowerShell コマンドレットを実行します。

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>直接割り当てを構成する

自動割り当てとは異なり、直接割り当てを使用する場合、ユーザーが個人用デスクトップに接続するには、ユーザーを個人用デスクトップ ホストプールと特定のセッション ホストの両方に割り当てる必要があります。 ユーザーがセッション ホストの割り当てなしでホスト プールにのみ割り当てられている場合、ユーザーはリソースにアクセスできません。

ユーザーをセッション ホストに直接割り当てる必要があるようにホスト プールを構成するには、次の PowerShell コマンドレットを実行します。

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

ユーザーを個人用デスクトップ ホスト プールに割り当てるには、次の PowerShell コマンドレットを実行します。

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

特定のセッション ホストにユーザーを割り当てるには、次の PowerShell コマンドレットを実行します。

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>次のステップ

個人用デスクトップ割り当ての種類を構成したので、次は Windows Virtual Desktop クライアントにサインインしてユーザー セッションの一部としてテストすることができます。 次の 2 つの手順では、任意のクライアントを使用してセッションに接続する方法を説明します。

- [Windows デスクトップ クライアントを使用して接続する](connect-windows-7-and-10.md)
- [Web クライアントに接続する](connect-web.md)
