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
ms.openlocfilehash: a9967aec61aaab5bc6b4517407f36e2a6c7342c8
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238864"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Azure VM でリモート デスクトップ サービスが起動しない

この記事では、リモート デスクトップ サービス (TermService) が起動していないか起動に失敗するときに、Azure 仮想マシン (VM) への接続の問題のトラブルシューティングを行う方法について説明します。

>[!NOTE]
>Azure には、リソースの作成と操作に関して、[Resource Manager とクラシック](../../azure-resource-manager/resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、Resource Manager デプロイ モデルの使用について説明します。 新しいデプロイでは、クラシック デプロイ モデルではなく、このモデルを使用することをお勧めします。

## <a name="symptoms"></a>現象

VM に接続しようとすると、次のシナリオが発生します。

- VM のスクリーンショットに、完全に読み込まれ、資格情報を待機しているオペレーティング システムが表示されている。

    ![VM の状態のスクリーンショット](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- イベント ビューアーを使用して VM のイベント ログをリモート表示すると、リモート デスクトップ サービス (TermService) が起動していないか起動に失敗していることが確認できます。 サンプル ログを次に示します。

    **ログの名前**:      システム </br>
    **ソース**:        サービス コントロール マネージャー </br>
    **日付**:          12/16/2017 11:19:36 AM</br>
    **イベント ID**:      7022</br>
    **タスクのカテゴリ**: なし</br>
    **レベル**:         エラー</br>
    **キーワード**:      クラシック</br>
    **ユーザー**:          N/A</br>
    **コンピューター**:      vm.contoso.com</br>
    **説明**: Remote Desktop Services サービスは起動時に停止しました。 

    これらのエラーを検索するシリアル アクセス コンソール機能を、次のクエリを使用して使用することもできます。 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>原因
 
この問題は、リモート デスクトップ サービスが VM で実行されていないために発生します。 原因は、次のどのシナリオかによって異なります。 

- TermService サービスが**無効**に設定されている。 
- TermService サービスがクラッシュまたはハングする。 

## <a name="solution"></a>解決策

この問題のトラブルシューティングを行うには、シリアル コンソールを使用するか、VM の OS ディスクを復旧 VM にアタッチして [VM をオフライン修復](#repair-the-vm-offline)します。

### <a name="use-serial-console"></a>シリアル コンソールの使用

1. **[サポートとトラブルシューティング]** > **[シリアル コンソール]** を選択して [[シリアル コンソール]](serial-console-windows.md) にアクセスします。 VM で機能が有効な場合、VM を正常に接続できます。

2. CMD インスタンス用の新しいチャネルを作成します。 **CMD** と入力してチャネルを開始し、チャネル名を取得します。

3. CMD インスタンスを実行しているチャネルに切り替えます。 例では、チャネル 1 です。

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
    サービスを開始できない場合、表示されたエラーに応じて解決策を実行します。

    |  Error |  推奨事項 |
    |---|---|
    |5 - ACCESS DENIED |「[Access Denied エラーが原因で TermService サービスが停止する](#termService-service-is-stopped-because-of-access-denied-error)」を参照してください。 |
    |1058 - ERROR_SERVICE_DISABLED  |「[TermService サービスが無効化されている](#termService-service-is-disabled)」を参照してください。  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。|
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。    |
    |1070 - ERROR_SERVICE_START_HANG   | 問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。  |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | 「[TermService サービスが無効化されている](#termService-service-is-disabled)」を参照してください。  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。 |
    |1753   |問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。   |

#### <a name="termservice-service-is-stopped-because-of-access-denied-error"></a>Access Denied エラーが原因で TermService サービスが停止する

1. [シリアル コンソール](serial-console-windows.md#)に接続し、PowerShell インスタンスを開きます。
2. 次のスクリプトを実行して、プロセス モニター ツールをダウンロードします。

        remove-module psreadline  
        $source = "https://download.sysinternals.com/files/ProcessMonitor.zip" 
        $destination = "c:\temp\ProcessMonitor.zip" 
        $wc = New-Object System.Net.WebClient 
        $wc.DownloadFile($source,$destination) 
3. procmon トレースを開始します。

        procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML 
4. Access Denied エラーの原因となっているサービスを開始して、問題を再現します。 

        sc start TermService 
        
    失敗した時点で、プロセス モニター トレースを終了します。

        procmon /Terminate 
5.  **c:\temp\ProcMonTrace.PML** ファイルを収集し、procmon を使用して開き、次のスクリーンショットのように  **Result is ACCESS DENIED** でフィルター処理します。

    ![プロセス モニターでの [Result] によるフィルター処理](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. 出力されるレジストリ キー、フォルダー、ファイルを修正します。 通常、この問題は、サービスで使用されるログオン アカウントに、これらのオブジェクトにアクセスするのに必要な ACL アクセス許可がないために発生します。 ログオン アカウントの適切な ACL アクセス許可は、正常な VM で確認できます。 

#### <a name="termservice-service-is-disabled"></a>TermService サービスが無効化されている

1.  サービスを既定のスタートアップ値に復元します。

        sc config TermService start= demand 
        
2.  サービスを開始します。

        sc start TermService 
3.  サービスの状態をもう一度クエリして、サービスが実行されていることを確認します。     sc query TermService 
4.  リモート デスクトップを使用して VM に接続してみます。


### <a name="repair-the-vm-offline"></a>VM をオフライン修復する

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>復旧 VM に OS ディスクを接続する

1. [復旧 VM に OS ディスクを接続します](../windows/troubleshoot-recovery-disks-portal.md)。
2. 復旧 VM へのリモート デスクトップ接続を開始します。 接続したディスクが [ディスクの管理] コンソールで **[オンライン]** になっていることを確認します。 接続された OS ディスクに割り当てられたドライブ文字をメモします。
3.  管理者特権のコマンド プロンプト インスタンス (**[管理者として実行]**) を開き、次のスクリプトを実行します。 接続されている OS ディスクに割り当てられているドライブ文字が F であると想定しています。この文字を実際の VM の適切な値に置き換えてください。 

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
        REM Set default values back on the broken service 
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
4. [OS ディスクをデタッチして、VM を再作成](../windows/troubleshoot-recovery-disks-portal.md)してから、問題が解決されたかどうかを確認します。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
