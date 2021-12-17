---
title: ドレイン モードを使用してセッション ホストを分離する方法 - Azure Virtual Desktop
description: Azure Virtual Desktop でドレイン モードを使用してセッション ホストを分離し、メンテナンスを実行する方法について説明します。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/14/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 4bacff4ecef6762a4e08760c463531392a71245f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352014"
---
# <a name="use-drain-mode-to-isolate-session-hosts-and-apply-patches"></a>ドレイン モードを使用してセッション ホストを分離し、パッチを適用する

ユーザー セッションを中断せずに修正プログラムを適用したりメンテナンスを行ったりしたい場合に、ドレイン モードを使用するとセッション ホストが分離されます。 分離されている場合、セッション ホストでは新しいユーザー セッションを受け入れません。 新しい接続は、次に使用可能なセッショ ン ホストにリダイレクトされます。 セッション ホスト内の既存の接続は、ユーザーがサインアウトするか、管理者がセッションを終了するまで動作し続けます。 セッション ホストがドレイン モードであるときは、管理者も Azure Virtual Desktop サービスを使わずにリモートでサーバーに接続できます。 この設定は、プールされたデスクトップと個人用のデスクトップの両方に適用できます。

## <a name="set-drain-mode-using-the-azure-portal"></a>Azure portal を使用してドレイン モードを設定する

Azure portal でドレイン モードをオンにするには、次のようにします。

1. Azure portal を開き、分離するホスト プールにアクセスします。

2. ナビゲーション メニューで、 **[セッション ホスト]** を選択します。

3. 次に、ドレイン モードをオンにするホストを選択し、 **[Turn drain mode on]\(ドレイン モードをオンにする\)** を選択します。

4. ドレイン モードをオフにするには、ドレイン モードがオンになっているホスト プールを選択し、 **[Turn drain mode off]\(ドレイン モードをオフにする\)** を選択します。

## <a name="set-drain-mode-using-powershell"></a>PowerShell を使用してドレイン モードを設定する

PowerShell では、[Update-AzWvdSessionhost](/powershell/module/az.desktopvirtualization/update-azwvdsessionhost?view=azps-5.8.0&preserve-view=true) コマンドの一部である *AllowNewSessions* パラメーターを使用してドレイン モードを設定できます。

ドレイン モードを有効にするには、このコマンドレットを実行します。

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$False
```

ドレイン モードを無効にするには、このコマンドレットを実行します。

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$True
```

>[!IMPORTANT]
>このコマンドは、設定を適用するすべてのセッション ホストに対して実行する必要があります。

## <a name="next-steps"></a>次のステップ

Azure Virtual Desktop で Azure portal を利用する方法を詳しく知りたい方は、[チュートリアル](create-host-pools-azure-marketplace.md)をご覧ください。 基本について既によく理解している場合は、[MSIX アプリのアタッチ](app-attach-azure-portal.md)や [Azure Advisor](azure-advisor.md) など、Azure portal で使用できるその他の機能を確認してください。

PowerShell を利用していて、このモジュールで他に何ができるかを知りたいという方は「[Azure Virtual Desktop 用 PowerShell モジュールを設定する](powershell-module.md)」と [PowerShell の参考ドキュメント](/powershell/module/az.desktopvirtualization/)をご覧ください。
