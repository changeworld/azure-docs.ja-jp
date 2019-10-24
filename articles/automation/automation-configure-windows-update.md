---
title: Azure Update Management と連携するように Windows Update の設定を構成する
description: この記事では、Update Management と連携するように構成する Windows Update の設定について説明します
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f50ca9515f12e8c9b5943904c4d0226f2ca3353c
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377392"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Update Management 用に Windows Update の設定を構成する

Update Management は、Windows の更新プログラムのダウンロードとインストールに Windows Update を使用しています。 そのため、Windows Update で使用される多くの設定を尊重しています。 Windows 以外の更新プログラムを有効にする設定を使用している場合、Update Management では、それらの更新プログラムも管理されます。 更新プログラムの展開が行われる前の更新プログラムのダウンロードを有効にすると、更新プログラムの展開が速くなり、メンテナンス期間を超過する可能性が低くなります。

## <a name="pre-download-updates"></a>更新プログラムの事前ダウンロード

グループ ポリシーで更新プログラムを自動的にダウンロードするように構成するには、[[自動更新を構成する]](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) 設定を **[3]** に設定します。 これで、必要な更新プログラムはバックグラウンドでダウンロードされますが、インストールされません。 そのため、Update Management のスケジュールを管理しながら、更新プログラムの管理のメンテナンス期間以外に更新プログラムをダウンロードできます。 この方法で Update Management の "**メンテナンス期間を超過しました**" エラーを防ぐことができます。

この設定は PowerShell で行うこともできます。更新プログラムを自動ダウンロードするシステム上で、次の PowerShell を実行してください。

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>自動インストールを無効化する

Azure VM では、更新プログラムの自動インストールが既定で有効になっています。 そのため、更新プログラムは、Update Management によってインストールされるようにユーザーがスケジュールする前に、インストールされる可能性があります。 この動作は、`NoAutoUpdate` レジストリ キーを `1` に設定すると無効化できます。 次の PowerShell スニペットは、この操作を行う 1 つの方法を示しています。

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>再起動の設定を構成する

「[レジストリを編集して自動更新を構成する](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-rej7uijui7jgistry)」と「[再起動の管理に使われるレジストリ キー](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)」に示されているレジストリ キーを使用すると、[更新プログラムのデプロイ] の設定で **[更新しない]** を指定している場合でも、コンピューターを再起動することができます。 これらのレジストリ キーは、ご利用の環境の必要に応じて構成してください。

## <a name="enable-updates-for-other-microsoft-products"></a>他の Microsoft 製品の更新プログラムを有効にする

既定では、Windows Update は Windows の更新プログラムのみを提供します。 **[Windows の更新時に他の Microsoft 製品の更新プログラムも入手します]** をオンにすると、SQL Server や他のファースト パーティ ソフトウェアのセキュリティ修正プログラムなど、他の製品の更新プログラムも提供されます。 このオプションをグループ ポリシーで構成することはできません。 他のファースト パーティの修正プログラムを有効にするシステム上で次の PowerShell を実行すると、Update Management はその設定に従います。

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS 構成設定

**Update Management**  は、WSUS の構成設定に従います。 Update Management と連携させるために構成できる WSUS 設定の一覧を次に示します。

### <a name="intranet-microsoft-update-service-location"></a>イントラネットの Microsoft 更新サービスの場所

「[イントラネットの Microsoft 更新サービスの場所を指定する](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)」で、更新プログラムをスキャンおよびダウンロードするためのソースを指定できます。

## <a name="next-steps"></a>次の手順

Windows Update の設定を構成した後、「[Azure VM の更新プログラムとパッチの管理](automation-tutorial-update-management.md)」の手順に従って、更新プログラムのデプロイをスケジュールすることができます。