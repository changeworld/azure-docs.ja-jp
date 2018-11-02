---
title: Azure VM でリモート デスクトップ サービスが起動しない | Microsoft Docs
description: 仮想マシンに接続するときにリモート デスクトップ サービスに関する問題のトラブルシューティングを行う方法について説明する |Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 39b793e2722766f3f28829b4dc48534054abd97e
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2018
ms.locfileid: "49989022"
---
# <a name="remote-desktop-services-is-not-starting-on-an-azure-vm"></a>Azure VM でリモート デスクトップ サービスが起動しない

この記事では、リモート デスクトップ サービス (TermService) が開始されていないか開始に失敗するときに、Azure 仮想マシン (VM) への接続の問題をトラブルシューティングする方法について説明します。

>[!NOTE]
>Azure には、リソースの作成と操作に関して、[Resource Manager とクラシック](../../azure-resource-manager/resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、Resource Manager デプロイ モデルの使用について説明します。 新しいデプロイでは、クラシック デプロイ モデルではなく、このモデルを使用することをお勧めします。

## <a name="symptoms"></a>現象

VM に接続しようとすると、次のシナリオが発生します。

- VM のスクリーンショットに、完全に読み込まれ、資格情報を待機しているオペレーティング システムが表示されている。
- VM 上のすべてのアプリケーションが期待どおりに動作しアクセス可能である。
- VM は Microsoft リモート デスクトップ プロトコル (RDP) ポート (既定では 3389) の TCP 接続に応答します。
- RDP 接続を作成しようとしても資格情報を要求されない。

## <a name="cause"></a>原因

この問題は、リモート デスクトップ サービスが仮想マシンで実行されていないために発生します。 原因は、次のどのシナリオかによって異なります。

- TermService サービスが**無効**に設定された。
- TermService サービスがクラッシュまたはハングする。

## <a name="solution"></a>解決策

この問題を解決するには、次の解決策のいずれかを使用するか、解決策を 1 つずつ実行してください。

### <a name="solution-1-using-the-serial-console"></a>解決策 1: シリアル コンソールを使用する

1. **[サポートとトラブルシューティング]** > **[Serial console (Preview)]\(シリアル コンソール (プレビュー))** を選択して [[シリアル コンソール]](serial-console-windows.md) にアクセスします。 VM で機能が有効な場合、VM を正常に接続できます。

2. CMD インスタンス用の新しいチャネルを作成します。 **CMD** と入力してチャネルを開始し、チャネル名を取得します。

3. CMD インスタンスを実行するチャネル (この場合ではチャネル 1) に切り替えます。

   ```
   ch -si 1
   ```

4. **Enter** キーをもう一度押し、VM の有効なユーザー名とパスワード (ローカルまたはドメイン ID) を入力します。

5. TermService サービスの状態についてクエリを実行します。

   ```
   sc query TermService
   ```

6. サービス状態の表示が **[停止済み]** の場合、サービスを開始してみてください。

   ```
   sc start TermService
   ```

7. サービスが正常に開始されているかどうかを確認するために、サービスに対してもう一度クエリを実行します。

   ```
   sc query TermService
   ```

### <a name="solution-2-using-a-recovery-vm-to-enable-the-service"></a>解決策 2: 復旧 VM を使用してサービスを有効にする

[OS ディスクをバックアップ](../windows/snapshot-copy-managed-disk.md)し、[OS ディスクを復旧 VM に接続](../windows/troubleshoot-recovery-disks-portal.md)します。 次に、昇格された CMD インスタンスを開き、復旧 VM で次のスクリプトを実行します。

>[!NOTE]
>接続されている OS ディスクに割り当てられているドライブ文字が F であると想定しています。この文字を実際の VM の適切な値に置き換えてください。 これが完了した後は、復旧 VM からディスクをデタッチし、[VM を再作成します](../windows/create-vm-specialized.md)。 詳細なトラブルシューティングを行う場合は、**解決策 1** を使用します。これは、シリアル コンソールが有効になっているためです。

```
reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

REM Enable Serial Console
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} displaybootmenu yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} timeout 10
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} bootems yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /ems {<Boot Loader Identifier>} ON
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

REM Get the current ControlSet from where the OS is booting
for /f "tokens=3" %x in ('REG QUERY HKLM\BROKENSYSTEM\Select /v Current') do set ControlSet=%x
set ControlSet=%ControlSet:~2,1%

REM Suggested configuration to enable OS Dump
set key=HKLM\BROKENSYSTEM\ControlSet00%ControlSet%\Control\CrashControl
REG ADD %key% /v CrashDumpEnabled /t REG_DWORD /d 2 /f
REG ADD %key% /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
REG ADD %key% /v NMICrashDump /t REG_DWORD /d 1 /f

REM Set default values back on the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Image Path>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v type /t REG_DWORD /d 16 /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v type /t REG_DWORD /d 16 /f

REM Enable default dependencies from the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg unload HKLM\BROKENSYSTEM
```

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
