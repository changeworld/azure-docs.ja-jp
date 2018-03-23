---
title: PowerShell を使用して X.509 証明書を作成する方法 | Microsoft Docs
description: PowerShell を使用してローカルで X.509 証明書を作成し、シミュレーション環境の Azure IoT Hub で X.509 ベースのセキュリティを有効にする方法。
services: iot-hub
documentationcenter: ''
author: dsk-2015
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2017
ms.author: dkshir
ms.openlocfilehash: b2f78e8debd367f86ee9bb06bf7de50590c61ad7
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/13/2018
---
# <a name="powershell-scripts-to-manage-ca-signed-x509-certificates"></a>CA が署名した X.509 証明書を管理する PowerShell スクリプト

IoT Hub の X.509 証明書ベースのセキュリティでは、ルート証明書に加えてリーフ証明書までのすべての中間証明書を含む [X.509 証明書チェーン](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification)から始める必要があります。 この*方法*ガイドでは、[OpenSSL](https://www.openssl.org/) を使用して X.509 証明書を作成し、署名するサンプル PowerShell スクリプトについて手順を追って説明します。 このガイドで説明する手順の多くは、実際には製造プロセスで発生するため、このガイドは実験目的でのみ使用することをお勧めします。 このガイドの証明書は、*X.509 証明書認証*を使用する Azure IoT Hub でセキュリティをシミュレートするために使用できます。 このガイドの手順では、Windows コンピューターのローカルで証明書を作成します。 

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、OpenSSL バイナリを取得済みであることを前提としています。 次のいずれかの方法で取得できます。
    - OpenSSL ソース コードをダウンロードし、ローカル コンピューター上でバイナリをビルドします。または 
    - [サードパーティの OpenSSL バイナリ](https://wiki.openssl.org/index.php/Binaries) (たとえば、[SourceForge のこちらのプロジェクトのバイナリ](https://sourceforge.net/projects/openssl/)) をダウンロードしてインストールします。

<a id="createcerts"></a>

## <a name="create-x509-certificates"></a>X.509 証明書を作成する
次の手順は、X.509 ルート証明書をローカルで作成する方法の例です。 

1. *管理者*として PowerShell ウィンドウを開きます。 
2. 作業ディレクトリに移動します。 次のスクリプトを実行して、グローバル変数を設定します。 
    ```PowerShell
    $openSSLBinSource = "<full_path_to_the_binaries>\OpenSSL\bin"
    $errorActionPreference    = "stop"

    # Note that these values are for test purpose only
    $_rootCertSubject         = "CN=Azure IoT Root CA"
    $_intermediateCertSubject = "CN=Azure IoT Intermediate {0} CA"
    $_privateKeyPassword      = "123"

    $rootCACerFileName          = "./RootCA.cer"
    $rootCAPemFileName          = "./RootCA.pem"
    $intermediate1CAPemFileName = "./Intermediate1.pem"
    $intermediate2CAPemFileName = "./Intermediate2.pem"
    $intermediate3CAPemFileName = "./Intermediate3.pem"

    $openSSLBinDir              = Join-Path $ENV:TEMP "openssl-bin"

    # Whether to use ECC or RSA.
    $useEcc                     = $true
    ```
3. 次のスクリプトを実行して、OpenSSL バイナリを作業ディレクトリにコピーし、環境変数を設定します。

    ```PowerShell
    function Initialize-CAOpenSSL()
    {
        Write-Host ("Beginning copy of openssl binaries to {0} (and setting up env variables...)" -f $openSSLBinDir)
        if (-not (Test-Path $openSSLBinDir))
        {
            mkdir $openSSLBinDir | Out-Null
        }

        robocopy $openSSLBinSource $openSSLBinDir * /s 
        robocopy $openSSLBinSource . * /s 

        Write-Host "Setting up PATH and other environment variables."
        $ENV:PATH += "; $openSSLBinDir"
        $ENV:OPENSSL_CONF = Join-Path $openSSLBinDir "openssl.cnf"

        Write-Host "Success"
    }
    Initialize-CAOpenSSL
    ```
4. 次のスクリプトを実行して、指定された*サブジェクト名*の証明書が既にインストールされているかどうか、コンピューターで OpenSSL が正しく構成されているかどうかを検索します。
    ```PowerShell
    function Get-CACertBySubjectName([string]$subjectName)
    {
        $certificates = gci -Recurse Cert:\LocalMachine\ |? { $_.gettype().name -eq "X509Certificate2" }
        $cert = $certificates |? { $_.subject -eq $subjectName -and $_.PSParentPath -eq "Microsoft.PowerShell.Security\Certificate::LocalMachine\My" }
        if ($NULL -eq $cert)
        {
            throw ("Unable to find certificate with subjectName {0}" -f $subjectName)
        }
    
        write $cert
    }
    function Test-CAPrerequisites()
    {
        $certInstalled = $null
        try
        {
            $certInstalled = Get-CACertBySubjectName $_rootCertSubject
        }
        catch {}

        if ($NULL -ne $certInstalled)
        {
            throw ("Certificate {0} already installed.  Cleanup CA certs 1st" -f $_rootCertSubject)
        }

        if ($NULL -eq $ENV:OPENSSL_CONF)
        {
            throw ("OpenSSL not configured on this system.  Run 'Initialize-CAOpenSSL' (even if you've already done so) to set everything up.")
        }
        Write-Host "Success"
    }
    Test-CAPrerequisites
    ```
    すべてが正しく構成されている場合は、"Success" メッセージが表示されます。 

<a id="createcertchain"></a>

## <a name="create-x509-certificate-chain"></a>X.509 証明書チェーンを作成する
次の PowerShell スクリプトを実行して、ルート CA を含む証明書チェーンを作成します。たとえば、このサンプルでは、"CN=Azure IoT Root CA" を使用します。 このスクリプトで、Windows OS 証明書ストアも更新され、作業ディレクトリに証明書ファイルが作成されます。 
    1. 次のスクリプトで、指定した*サブジェクト名*と署名機関について自己署名証明書を作成する PowerShell 関数が作成されます。 
    ```PowerShell
    function New-CASelfsignedCertificate([string]$subjectName, [object]$signingCert, [bool]$isASigner=$true)
    {
        # Build up argument list
        $selfSignedArgs =@{"-DnsName"=$subjectName; 
                           "-CertStoreLocation"="cert:\LocalMachine\My";
                           "-NotAfter"=(get-date).AddDays(30); 
                          }

        if ($isASigner -eq $true)
        {
            $selfSignedArgs += @{"-KeyUsage"="CertSign"; }
            $selfSignedArgs += @{"-TextExtension"= @(("2.5.29.19={text}ca=TRUE&pathlength=12")); }
        }
        else
        {
            $selfSignedArgs += @{"-TextExtension"= @("2.5.29.37={text}1.3.6.1.5.5.7.3.2,1.3.6.1.5.5.7.3.1", "2.5.29.19={text}ca=FALSE&pathlength=0")  }
        }

        if ($signingCert -ne $null)
        {
            $selfSignedArgs += @{"-Signer"=$signingCert }
        }

        if ($useEcc -eq $true)
        {
            $selfSignedArgs += @{"-KeyAlgorithm"="ECDSA_nistP256";
                             "-CurveExport"="CurveName" }
        }

        # Now use splatting to process this
        Write-Host ("Generating certificate {0} which is for prototyping, NOT PRODUCTION.  It will expire in 30 days." -f $subjectName)
        write (New-SelfSignedCertificate @selfSignedArgs)
    }
    ``` 
    2. 次の PowerShell 関数で、前の関数と OpenSSL バイナリを使用して中間の X.509 証明書が作成されます。 
    ```PowerShell
    function New-CAIntermediateCert([string]$subjectName, [Microsoft.CertificateServices.Commands.Certificate]$signingCert, [string]$pemFileName)
    {
        $certFileName = ($subjectName + ".cer")
        $newCert = New-CASelfsignedCertificate $subjectName $signingCert
        Export-Certificate -Cert $newCert -FilePath $certFileName -Type CERT | Out-Null
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\CA" -FilePath $certFileName | Out-Null

        # Store public PEM for later chaining
        openssl x509 -inform deer -in $certFileName -out $pemFileName

        del $certFileName
   
        write $newCert
    }  
    ```
    3. 次の PowerShell 関数で、X.509 証明書チェーンが作成されます。 詳細については、[証明書チェーン](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification)に関するページを参照してください。
    ```PowerShell
    function New-CACertChain()
    {
        Write-Host "Beginning to install certificate chain to your LocalMachine\My store"
        $rootCACert =  New-CASelfsignedCertificate $_rootCertSubject $null
    
        Export-Certificate -Cert $rootCACert -FilePath $rootCACerFileName  -Type CERT
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\Root" -FilePath $rootCACerFileName

        openssl x509 -inform der -in $rootCACerFileName -out $rootCAPemFileName

        $intermediateCert1 = New-CAIntermediateCert ($_intermediateCertSubject -f "1") $rootCACert $intermediate1CAPemFileName
        $intermediateCert2 = New-CAIntermediateCert ($_intermediateCertSubject -f "2") $intermediateCert1 $intermediate2CAPemFileName
        $intermediateCert3 = New-CAIntermediateCert ($_intermediateCertSubject -f "3") $intermediateCert2 $intermediate3CAPemFileName
        Write-Host "Success"
    }    
    ```
    このスクリプトで、*RootCA.cer* というファイルが作業ディレクトリに作成されます。 
    4. 最後に、PowerShell ウィンドウで `New-CACertChain` コマンドを実行し、上記の PowerShell 関数を使用して X.509 証明書チェーンを作成します。 


<a id="signverificationcode"></a>

## <a name="proof-of-possession-of-your-x509-ca-certificate"></a>X.509 CA 証明書の所有証明

このスクリプトで、X.509 証明書の*所有証明*フローを実行します。 

デスクトップ上の PowerShell ウィンドウで次のコードを実行します。
   
   ```PowerShell
   function New-CAVerificationCert([string]$requestedSubjectName)
   {
       $cnRequestedSubjectName = ("CN={0}" -f $requestedSubjectName)
       $verifyRequestedFileName = ".\verifyCert4.cer"
       $rootCACert = Get-CACertBySubjectName $_rootCertSubject
       Write-Host "Using Signing Cert:::" 
       Write-Host $rootCACert
   
       $verifyCert = New-CASelfsignedCertificate $cnRequestedSubjectName $rootCACert $false

       Export-Certificate -cert $verifyCert -filePath $verifyRequestedFileName -Type Cert
       if (-not (Test-Path $verifyRequestedFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $verifyRequestedFileName)
       }
   
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnRequestedSubjectName, (Join-Path (get-location).path $verifyRequestedFileName)) 
   }
   New-CAVerificationCert "<your verification code>"
   ```

このコードにより、指定されたサブジェクト名が付けられ、CA に署名された *VerifyCert4.cer* というファイル名の証明書が作業ディレクトリに作成されます。 この証明書ファイルは、この CA の署名アクセス許可 (つまり秘密キー) を持っている IoT Hub の検証に役立ちます。


<a id="createx509device"></a>

## <a name="create-leaf-x509-certificate-for-your-device"></a>デバイスのリーフ X.509 証明書を作成する

ここでは、リーフ デバイス証明書と対応する証明書チェーンを作成する PowerShell スクリプトを使用する方法について説明します。 

ローカル コンピューターの PowerShell ウィンドウで、次のスクリプトを実行して、CA が署名した X.509 証明書をこのデバイス用に作成します。

   ```PowerShell
   function New-CADevice([string]$deviceName, [string]$signingCertSubject=$_rootCertSubject)
   {
       $cnNewDeviceSubjectName = ("CN={0}" -f $deviceName)
       $newDevicePfxFileName = ("./{0}.pfx" -f $deviceName)
       $newDevicePemAllFileName      = ("./{0}-all.pem" -f $deviceName)
       $newDevicePemPrivateFileName  = ("./{0}-private.pem" -f $deviceName)
       $newDevicePemPublicFileName   = ("./{0}-public.pem" -f $deviceName)
   
       $signingCert = Get-CACertBySubjectName $signingCertSubject ## "CN=Azure IoT CA Intermediate 1 CA"

       $newDeviceCertPfx = New-CASelfSignedCertificate $cnNewDeviceSubjectName $signingCert $false
   
       $certSecureStringPwd = ConvertTo-SecureString -String $_privateKeyPassword -Force -AsPlainText

       # Export the PFX of the cert we've just created.  The PFX is a format that contains both public and private keys.
       Export-PFXCertificate -cert $newDeviceCertPfx -filePath $newDevicePfxFileName -password $certSecureStringPwd
       if (-not (Test-Path $newDevicePfxFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $newDevicePfxFileName)
       }

       # Begin the massaging.  First, turn the PFX into a PEM file which contains public key, private key, and other attributes.
       Write-Host ("When prompted for password by openssl, enter the password as {0}" -f $_privateKeyPassword)
       openssl pkcs12 -in $newDevicePfxFileName -out $newDevicePemAllFileName -nodes

       # Convert the PEM to get formats we can process
       if ($useEcc -eq $true)
       {
           openssl ec -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       else
       {
           openssl rsa -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       openssl x509 -in $newDevicePemAllFileName -out $newDevicePemPublicFileName
 
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnNewDeviceSubjectName, (Join-Path (get-location).path $newDevicePemPublicFileName)) 
   }
   ```

デバイスの作成に使用したフレンドリ名を使用して、PowerShell ウィンドウで `New-CADevice "<yourTestDevice>"` を実行します。 CA の秘密キーのパスワードを求められたら、「123」と入力します。 これで、作業ディレクトリに _<yourTestDevice>.pfx_ ファイルが作成されます。

## <a name="clean-up-certificates"></a>証明書のクリーンアップ

スタート バーまたは**設定**アプリで、**コンピューター証明書の管理**を検索して選択します。 **Azure IoT CA TestOnly*** によって発行された任意の証明書を削除します。 これらの証明書は、次の 3 つの場所に存在する必要があります。 

* 証明書 - ローカル コンピューター > 個人 > 証明書
* 証明書 - ローカル コンピューター > 信頼されたルート証明機関 > 証明書
* 証明書 - ローカル コンピューター > 中間証明機関 > 証明書

   ![Azure IoT CA TestOnly 証明書を削除する](./media/iot-hub-security-x509-create-certificates/cleanup.png)