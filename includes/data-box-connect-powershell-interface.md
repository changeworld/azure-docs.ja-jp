---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 11/15/2021
ms.author: alkohli
ms.openlocfilehash: fa4d3974a016fc57624c2c51d9aaf703583f8764
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557115"
---
Data Box が実行されているソフトウェアのバージョンに応じて、デバイスの PowerShell インターフェイスに接続するために異なる手順を実行することが必要な場合があります。 これらすべての場合の最初の手順は、Data Box で実行されているソフトウェア バージョンを識別することです。

### <a name="identify-software-version-of-the-device"></a>デバイスのソフトウェア バージョンを識別する

> [!NOTE]
> バージョン番号は、v 4.9 以降のローカル UI に表示されます。 ローカル UI にここで説明するバージョン番号が表示されていない場合は、Microsoft サポート に問い合わせ、バージョン番号を識別してから、デバイスの PowerShell インターフェイスに接続する手順に従ってください。

デバイスで実行されているソフトウェアのバージョンとデバイスのシリアル番号を識別するには、次のいずれかの方法を使用します。

 - **[サインイン]** ページに移動します。 このページには、ソフトウェアのバージョンが表示されます。
 
    ![Data Box のローカル Web UI の [サインイン] ページのシリアル番号とソフトウェア バージョン](media/data-box-connect-powershell-interface/device-serial-number-software-version-sign-in-local-ui.png)

 - ローカル Web UI で、 **[ヘルプ]** アイコンを選択 します。 **[ヘルプ]** ウィンドウに、ソフトウェア バージョンとデバイスのシリアル番号が表示されます。 デバイスのシリアル番号とソフトウェア バージョンの両方をメモします。
 
    ![Data Box のローカル Web UI の [ヘルプ] ウィンドウのシリアル番号とソフトウェア バージョン](media/data-box-connect-powershell-interface/device-serial-number-software-version-help-pane-local-ui.png)



次の手順は、Data Box により実行されているソフトウェアのバージョンによって決まります。
 
### <a name="v41-and-later"></a>[v4.1 以降](#tab/c)

これらのバージョンのソフトウェアでは、まず、Data Box にアクセスするホストに証明書をインストールしてから、デバイスの PowerShell インターフェイスに接続する必要があります。 証明書をインストールする際には、デバイスに同梱されている既定の証明書を使用するか、独自の証明書を使用できます。 それぞれの場合、実行する具体的な手順は少し異なります。


### <a name="step-1-install-certificate-on-host-or-client"></a>手順 1: ホストまたはクライアントに証明書をインストールする

#### <a name="use-default-certificates"></a>既定の証明書を使用する 

Data Box に既定の証明書 (デバイスに同梱) を使用する場合は、次の手順に従ってください。

1. Azure portal 上で、Data Box 注文リソースに移動します。 **[全般] > [デバイスの詳細]** に移動します。 **[API access to the device]\(デバイスへの API アクセス\)** タブで、 **[ダウンロード]** を選択します。

    ![Azure portal 上で Data Box の証明書をダウンロードする](media/data-box-connect-powershell-interface/download-certificate-data-box-portal.png)

1. 証明書を、Data Box に接続しているホストにコピーします。 Windows ホストを使用している場合、Explorer を開きます。 ホストにコピーした証明書ファイルを右クリックし、 **[証明書のインストール]** を選択します。 

1. **[証明書のインポート]** ウィザードを使用して、インストール手順を完了します。 詳細な手順については、[クライアントへの証明書のインポート](../articles/databox/data-box-bring-your-own-certificates.md#import-certificates-to-client)に関するページを参照してください。 

1. デバイスの IP アドレスをデバイスの FQDN へマップするエントリをホスト ファイルに追加します。 エントリの形式は次のようになります。 

    `<Device IP address>  <Device serial number>.microsoftdatabox.com`

    前の手順のデバイスのシリアル番号を指定します。 

    ![ローカル UI から Data Box シリアル番号を取得する](media/data-box-connect-powershell-interface/get-device-serial-number-portal.png)
 

#### <a name="use-your-own-certificates"></a>独自の証明書を使用する 

Data Box に独自の証明書を使用する場合は、次の手順に従ってください。
 
1. Data Box に接続するホスト マシンに、独自の署名チェーン証明書をインストールします。 エクスプローラーを使用して証明書を開き、ファイルを右クリックして、 **[証明書のインストール]** を選択します。 
1. 「[証明書をクライアントにインポートする](../articles/databox/data-box-bring-your-own-certificates.md#import-certificates-to-client)」の手順に従い、証明書をローカル コンピューター/ルートにインストールします。 
1. デバイスの IP アドレスをデバイスの FQDN へマップするエントリをホスト ファイルに追加します。 エントリの形式は次のようになります。 

    `<Device IP address>  <Name>.<DNS domain>` 

    デバイスの **名前** と **DNS ドメイン** を取得 するには、ローカル UI で **[証明書]** ページ に移動 します。 
1. 証明書をアップロードするには、ローカル UI で **[証明書]** ページに移動 します。 **[+ 証明書の追加]** を選択し、証明書を指定します。 「[証明書のアップロード](../articles/databox/data-box-bring-your-own-certificates.md#add-certificates-to-device)」を参照してください。
 

### <a name="step-2-connect-to-the-powershell-interface"></a>手順 2: PowerShell インターフェイスに接続する


1. `$Name` パラメーターを設定します。

    ```powershell
    $Name = <Name>.<DNS domain>
    ```
    前の手順のデバイスの **[名前]** と **[DNS ドメイン]** を指定します。

1. `$Name` パラメーターを使用して Data Box を `TrustedHosts` リストに追加します。 型:
 
    ```powershell
    Set-Item wsman:\localhost\Client\TrustedHosts $Name 
    ```

1. ローカル UI のパスワードは、Azure portal から取得します。 パスワードを含む、セキュリティで保護された文字列を作成します。

    ```powershell
    $Pwd = ConvertTo-SecureString <Password from Azure portal> -AsPlainText -Force 
    ```
   <!--

    ```powershell
    $Cred = New-Object System.Management.Automation.PSCredential("<ipv4_address of databox>\StorSimpleAdmin",$Pwd) 
    ```-->

1. 接続を確立します。

    ```powershell
    Enter-PSSession -ComputerName $Name -ConfigurationName Minishell -Credential ~\StorSimpleAdmin -UseSSL  
    ```
    出力例を次に示します。

    ```powershell
    PS C:\Users\Administrator> winrm quickconfig
    WinRM service is already running on this machine.
    WinRM is already set up for remote management on this computer.
    PS C:\Users\Administrator> $Name = "by506b4b5d0790.microsoftdatabox.com"
    PS C:\Users\Administrator> Set-Item WSMan:\localhost\Client\TrustedHosts $Name -Concatenate -Force
    PS C:\Users\Administrator> Enter-PSSession -ComputerName $Name -Credential ~\StorSimpleAdmin -ConfigurationName Minishell -UseSSL
    WARNING: Please engage Microsoft Support if you need to access this interface
    to troubleshoot any potential issues you may be experiencing.
    Changes made through this interface without involving Microsoft
    Support could result in an unsupported configuration.
    [by506b4b5d0790.microsoftdatabox.com]: PS>
    ```

### <a name="skip-certificate-validation"></a>証明書の検証をスキップする

証明書を使用していない場合 (証明書を使用することをお勧めします)、セッション オプション `-SkipCACheck -SkipCNCheck -SkipRevocationCheck` を使用して証明書の妥当性確認をスキップできます。

証明書の検証をスキップするには、次の手順に従います。

1. `$Name` パラメーターを設定します。

    ```powershell
    $Name = <Name>.<DNS domain>
    ```
 
1. PowerShell セッションを開く場合は、セッション オプションを使用します。

    ```powershell
    $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck 
    ```
1. 接続を確立します。

    ```powershell
    Enter-PSSession -ComputerName $Name -ConfigurationName Minishell -Credential ~\StorSimpleAdmin -UseSSL -SessionOption $sessOptions 
    ```

### <a name="v30-to-v40"></a>[v3.0 to v4.0](#tab/b)

1. **[管理者として実行]** を使用して PowerShell セッションを開きます。 
1. IP アドレスを使用して Data Box を `TrustedHosts` リストに追加します。 型:

    ```powershell
    Set-Item Wsman:\localhost\Client\TrustedHosts <IPv4_address for your Data Box> 
    ``` 

1. Azure portal 上で、Data Box 注文リソースのパスワードを取得します。 

1. プレーン テキストで指定されたパスワードをセキュリティで保護された文字列に変換します。 型:

    ```powershell
    $Pwd = ConvertTo-SecureString <Password from Azure portal> -AsPlainText -Force 
    ```
1. セッションのユーザー名とパスワードを指定し、資格情報オブジェクトを作成します。 既定のユーザー名は `StorSimpleAdmin` であり、前の手順で受け取ったパスワードです。

    ```powershell
    $Cred = New-Object System.Management.Automation.PSCredential("~\StorSimpleAdmin",$Pwd)
    ``` 
1. デバイスとのセッションを開始します。

    ```powershell
    Enter-PSSession -Computer <IPv4_address for your Data Box> -ConfigurationName Minishell -Credential $Cred 
    ```

    出力例を次に示します。
    
    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    
    PS C:\WINDOWS\system32> Set-Item wsman:\localhost\Client\TrustedHosts "10.128.45.52"
    
    WinRM Security Configuration.
    This command modifies the TrustedHosts list for the WinRM client. The computers in the
    TrustedHosts list might not be authenticated. The client might send credential information to
     these computers. Are you sure that you want to modify this list?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    PS C:\WINDOWS\system32> $Pwd = ConvertTo-SecureString "Password1" -AsPlainText -Force
    PS C:\WINDOWS\system32> $Cred = New-Object System.Management.Automation.PSCredential("~\StorSimpleAdmin",$Pwd)
    PS C:\WINDOWS\system32> Enter-PSSession -Computer "10.128.45.52" -ConfigurationName Minishell -Credential $Cred
    WARNING: Please engage Microsoft Support if you need to access this interface
    to troubleshoot any potential issues you may be experiencing.
    Changes made through this interface without involving Microsoft
    Support could result in an unsupported configuration.
    [10.128.45.52]: PS>
    ```




  

