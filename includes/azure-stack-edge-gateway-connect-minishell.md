---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: 89e648099a5ac6d905f475319cc108dd0d05a6e9
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080998"
---
デバイスにリモート接続する手順は、クライアントのオペレーティング システムによって異なります。

### <a name="remotely-connect-from-a-windows-client"></a>Windows クライアントからリモートで接続する


#### <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

- Windows クライアントで、Windows PowerShell 5.0 以降が実行されている。
- Windows クライアントに、デバイスにインストールされているノード証明書に対応する署名チェーン (ルート証明書) がある。 詳細な手順については、[Windows クライアントに証明書をインストールする](../articles/databox-online/azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)に関するページを参照してください。
- Windows クライアントの `C:\Windows\System32\drivers\etc` にある `hosts` ファイルに、次の形式のノード証明書に対応するエントリがある。

    `<Device IP>    <Node serial number>.<DNS domain of the device>`

    `hosts` ファイルのエントリ例を以下に示します。
 
    `10.100.10.10    1HXQG13.wdshcsso.com`
  

#### <a name="detailed-steps"></a>詳細な手順

Windows クライアントからリモートで接続するには、次の手順に従います。

1. Windows PowerShell セッションを管理者として実行します。
2. Windows リモート管理サービスがクライアントで実行されていることを確認します。 コマンド プロンプトに、次のコマンドを入力します。

    `winrm quickconfig`

    詳細については、「[Windows リモート管理のためのインストールと構成](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management#quick-default-configuration)」をご覧ください。

3. デバイスの IP アドレスに変数を割り当てます。

    $ip = "<device_ip>"

    `<device_ip>` を デバイスの IP アドレスに置き換えます。

4. デバイスの IP アドレスをクライアントの信頼されたホスト一覧に追加するために、次のコマンドを入力します。

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. デバイスの Windows PowerShell セッションを開始します。

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL`

    信頼関係に関連するエラーが表示された場合は、デバイスにアップロードされたノード証明書の署名チェーンが、デバイスにアクセスしているクライアントにもインストールされているかどうかを確認します。

    証明書を使用していない場合 (証明書を使用することをお勧めします)、セッション オプション `-SkipCACheck -SkipCNCheck -SkipRevocationCheck` を使用してこの確認をスキップできます。

    ```powershell
    $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck 
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL -SessionOption $sessOptions    
    ```

    > [!NOTE] 
    > `-UseSSL` オプションを使用すると、PowerShell を介して *https* 経由でリモート処理が行われます。 PowerShell を介してリモート接続する場合は、常に *https* を使用することをお勧めします。 

6. パスワードの入力を求められたら、入力します。 ローカル Web UI へのサインインに使用するパスワードと同じものを使用してください。 既定のローカル Web UI パスワードは *Password1* です。 リモート PowerShell を使用してデバイスに正常に接続すると、次のサンプル出力が表示されます。  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

> [!IMPORTANT]
> 現在のリリースでは、Windows クライアント経由でのみデバイスの PowerShell インターフェイスに接続できます。 `-UseSSL` オプションは、Linux クライアントでは機能しません。

<!--### Remotely connect from a Linux client-->

<!--On the Linux client that you'll use to connect:

- [Install the latest PowerShell Core for Linux](/powershell/scripting/install/installing-powershell-core-on-linux) from GitHub to get the SSH remoting feature. 
- [Install only the `gss-ntlmssp` package from the NTLM module](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). For Ubuntu clients, use the following command:
    - `sudo apt-get install gss-ntlmssp`

For more information, go to [PowerShell remoting over SSH](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core).

Follow these steps to remotely connect from an NFS client.

1. To open PowerShell session, type:

    `pwsh`
 
2. For connecting using the remote client, type:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    When prompted, provide the password used to sign into your device.
 
> [!NOTE]
> This procedure does not work on Mac OS.-->