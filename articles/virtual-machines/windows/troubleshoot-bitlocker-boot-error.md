---
title: Azure VM での BitLocker ブート エラーのトラブルシューティング | Microsoft Docs
description: Azure VM での BitLocker ブート エラーをトラブルシューティングする方法について説明します
services: virtual-machines-windows
documentationCenter: ''
authors: genli
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 884959d7e59c351b4b6d8d09cb011fce88a4d30d
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669993"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>Azure VM での BitLocker ブート エラー

 この記事では、Microsoft Azure で Windows 仮想マシン (VM) を開始するときに発生する可能性がある BitLocker エラーについて説明します。

> [!NOTE] 
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシック](../../azure-resource-manager/resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、Resource Manager デプロイ モデルの使用について説明します。 新しいデプロイでは、クラシック デプロイ モデルではなく、このモデルを使用することをお勧めします。

 ## <a name="symptom"></a>症状

 Windows VM が起動しません。 [[ブート診断]](boot-diagnostics.md) ウィンドウでスクリーンショットを調べると、次のエラー メッセージのいずれかが表示されています。

- Plug in the USB driver that has the BitLocker key (BitLocker キーが格納されている USB ドライバーを差し込んでください)

- You’re locked out! (ロックアウトされています) Enter the recovery key to get going again (Keyboard Layout: US) The wrong sign-in info has been entered too many times, so your PC was locked to protect your privacy. (回復キーを入力して先に進んでください (キーボード レイアウト: US)。間違ったサインイン情報が何度も入力されたため、プライバシー保護のために PC はロックされました。) To retrieve the recovery key, go to http://windows.microsoft.com/recoverykeyfaq from another PC or mobile device. (回復キーを取得するには、別の PC またはモバイル デバイスから http://windows.microsoft.com/recoverykeyfaq に移動してください。) In case you need it, the key ID is XXXXXXX. (それが必要な場合、キー ID は XXXXXXX です。) Or, you can reset your PC. (または、PC をリセットできます。)

- Enter the password to unlock this drive [ ] Press the Insert Key to see the password as you type. (このドライブ [ ] をロック解除するためのパスワードを入力してください。入力したパスワードを表示するには Insert キーを押してください。)
- Enter your recovery key Load your recovery key from a USB device. (回復キーを入力してください。USB デバイスから回復キーを読み込んでください。)

## <a name="cause"></a>原因

この問題は、暗号化されたディスクの暗号化を解除するための BitLocker 回復キー (BEK) ファイルを VM が見つけることができない場合に発生する可能性があります。

## <a name="solution"></a>解決策

この問題を解決するには、VM を停止して割り当てを解除した後、再起動します。 この操作によって、VM は、Azure Key Vault から BEK ファイルを取得した後、それを暗号化されたディスクに保存します。 

この方法で問題が解決しない場合は、次の手順で BEK ファイルを手動で復元します。

1. バックアップとして、影響を受ける VM のシステム ディスクのスナップショットを取得します。 詳細については、[ディスクのスナップショット](./snapshot-copy-managed-disk.md)に関する記事を参照してください。
2. BitLocker によって暗号化されている[システム ディスクを復旧 VM にアタッチ](./troubleshoot-recovery-disks-portal.md)します。 これは、BitLocker で暗号化された VM でのみ利用可能なコマンドである[manage-bde](https://docs.microsoft.com/windows-server/administration/windows-commands/manage-bde) を実行するために必要です。

    マネージド ディスクをアタッチすると、エラー メッセージ "contains encryption settings and therefore cannot be used as a data disk” が表示される場合があります。 この状況では、次のスクリプトを実行して、ディスクのアタッチを再試行します。

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"

    New-AzureRmDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzureRmDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzureRMVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzureRmVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzureRMVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     Blob イメージから復元された VM にマネージド ディスクをアタッチすることはできません。

3. ディスクがアタッチされたら、復旧 VM へのリモート デスクトップ接続を行って、いくつかの Azure PowerShell スクリプトを実行できるようにします。 復旧 VM 上に[最新バージョンの Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) がインストールされていることを確認します。

4. 管理者特権の Azure PowerShell セッション (管理者として実行) を開きます。 次のコマンドを実行して、Azure サブスクリプションにサインインします。

    ```Powershell
    Add-AzureRMAccount -SubscriptionID [SubscriptionID]
    ```

5. 次のスクリプトを実行して、BEK ファイルの名前を確認します。

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    出力データの例を次に示します。 アタッチされたディスク用の BEK ファイル名を探します。 ここでは、アタッチされるディスクのドライブ文字は F と仮定しているため、BEK ファイルは EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK になります。

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    2 つの重複するボリュームが表示されている場合は、タイムスタンプが新しいほうのボリュームが、復旧 VM で使用される現在の BEK ファイルになります。

    **Content Type** の値が **Wrapped BEK** の場合は、「[Key Encryption Key (KEK) のシナリオ](#key-encryption-key-scenario)」に進んでください。

    これで、ドライブ用の BEK ファイルの名前がわかったので、secret-file-name.BEK ファイルを作成して、BEK ファイル ドライブのロックを解除する必要があります。 

6.  BEK ファイルを復元ディスクにダウンロードします。 次のサンプルは、BEK ファイルを C:\BEK フォルダーに保存します。 スクリプトを実行する前に、`C:\BEK\` パスが存在することを確認します。

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK"
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  アタッチされたディスクのロックを BEK ファイルを使用して解除するには、次のコマンドを実行します。

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    この例では、アタッチされた OS ディスクはドライブ F です。適切なドライブ文字を使用していることを確認してください。 

    - BEK キーを使用してディスクのロックが正常に解除されれば、 BItLocker の問題は解決されたと考えることができます。 

    - BEK キーを使用してもディスクのロックが解除されない場合は、次のコマンドを実行することで、保護の中断を使用して一時的に BitLocker をオフにすることができます。
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - システム ディスクを使用して VM を再構築する場合は、ドライブの暗号化を完全に解除する必要があります。 そのためには、次のコマンドを実行します。

        ```powershell
        manage-bde -off F:
        ```
8.  復旧 VM からディスクをデタッチし、影響を受ける VM にシステム ディスクとしてディスクを再アタッチします。 詳細については、「[OS ディスクを復旧 VM に接続して Windows VM のトラブルシューティングを行う](troubleshoot-recovery-disks.md)」を参照してください。

### <a name="key-encryption-key-scenario"></a>Key Encryption Key のシナリオ

Key Encryption Key のシナリオでは、次の手順に従います。

1. **[ユーザー] > [キーのアクセス許可] > [暗号化操作] > [Unwrap Key]\(キーのラップ解除\)** で、ログインするユーザー アカウントでは、Key Vault アクセス ポリシーの "ラップ解除" アクセス許可が必要であることを確認します。
2. 次のスクリプトを .PS1 ファイルに保存します。

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies
    $adal = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $authResult = $authContext.AcquireToken($resourceAppIdURI, $clientId, $redirectUri, "Auto")
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $wrappedBekSecretBase64 = $keyVaultSecret.SecretValueText
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)
    ```
3. パラメーターを設定します。 このスクリプトは、KEK シークレットを処理して BEK キーを作成した後、それを復旧 VM 上のローカル フォルダーに保存します。

4. スクリプトが開始されると、次の出力が表示されます。

        GAC    Version        Location                                                                              
        ---    -------        --------                                                                              
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\AzureRM.profile\4.0.0\Microsoft.Identity...
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\AzureRM.profile\4.0.0\Microsoft.Identity...

    スクリプトが終了すると、次の出力が表示されます。

        VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
        version=2015-06-01 with -1-byte payload
        VERBOSE: received 360-byte response of content type application/json; charset=utf-8


5. アタッチされたディスクのロックを BEK ファイルを使用して解除するには、次のコマンドを実行します。

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    この例では、アタッチされた OS ディスクはドライブ F です。適切なドライブ文字を使用していることを確認してください。 

    - BEK キーを使用してディスクのロックが正常に解除されれば、 BItLocker の問題は解決されたと考えることができます。 

    - BEK キーを使用してもディスクのロックが解除されない場合は、次のコマンドを実行することで、保護の中断を使用して一時的に BitLocker をオフにすることができます。
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - システム ディスクを使用して VM を再構築する場合は、ドライブの暗号化を完全に解除する必要があります。 そのためには、次のコマンドを実行します。

        ```powershell
        manage-bde -off F:
        ```

6. 復旧 VM からディスクをデタッチし、影響を受ける VM にシステム ディスクとしてディスクを再アタッチします。 詳細については、「[OS ディスクを復旧 VM に接続して Windows VM のトラブルシューティングを行う](troubleshoot-recovery-disks.md)」を参照してください。
