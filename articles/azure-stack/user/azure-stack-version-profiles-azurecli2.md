---
title: CLI を使用した Azure Stack への接続 | Microsoft Docs
description: クロスプラットフォーム コマンドライン インターフェイス (CLI) を使用して、Azure Stack でリソースを管理およびデプロイする方法
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 519046081a7f9778fb430daa0cd418cf9863a2b0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57975629"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>Azure Stack での Azure CLI による API バージョンのプロファイルの使用

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

この記事の手順に従うと、Linux、Mac、Windows クライアントのプラットフォームから Azure Stack Development Kit (ASDK) のリソースを管理するように Azure コマンド ライン インターフェイス (CLI) を設定できます。

## <a name="prepare-for-azure-cli"></a>Azure CLI の準備

開発用マシン上で Azure CLI を使用するには、Azure Stack の CA ルート証明書が必要です。 CLI を使用してリソースを管理する場合に、この証明書を使用します。

 - ASDK の外部のワークステーションから CLI を使用する場合は、**Azure Stack の CA ルート証明書**が必要です。  

 - **仮想マシンのエイリアス エンドポイント**は、VM をデプロイするときに、1 つのパラメーターとしてイメージ発行者、プラン、SKU、およびバージョンを参照する "UbuntuLTS" または "Win2012Datacenter" などのエイリアスを提供します。  

これらの値の取得方法については、以下のセクションで説明します。

### <a name="export-the-azure-stack-ca-root-certificate"></a>Azure Stack の CA ルート証明書をエクスポートする

統合システムを使用している場合は、CA ルート証明書をエクスポートする必要はありません。 ASDK で CA ルート証明書をエクスポートする必要があります。

ASDK ルート証明書を PEM 形式でエクスポートするには:

1. [Azure Stack に Windows VM を作成](azure-stack-quick-windows-portal.md)します。

2. このマシンにサインインし、管理者特権の PowerShell プロンプトを開き、次のスクリプトを実行します。

    ```powershell  
      $label = "AzureStackSelfSignedRootCert"
      Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
      $root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
      if (-not $root)
      {
          Write-Error "Certificate with subject CN=$label not found"
          return
      }

    Write-Host "Exporting certificate"
    Export-Certificate -Type CERT -FilePath root.cer -Cert $root

    Write-Host "Converting certificate to PEM format"
    certutil -encode root.cer root.pem
    ```

3. ローカル マシンに証明書をコピーします。


### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>仮想マシンのエイリアス エンドポイントを設定する

仮想マシンのエイリアス ファイルをホストする、パブリックにアクセス可能なエンドポイントを設定できます。 仮想マシンのエイリアス ファイルは、イメージの共通名が指定された JSON ファイルです。 この名前は、Azure CLI パラメーターとして VM をデプロイするときに使用します。

1. カスタム イメージを発行する場合は、発行時に指定した発行者、プラン、SKU、およびバージョン情報をメモしておいてください。 Marketplace のイメージである場合は、```Get-AzureVMImage``` コマンドレットを使用して情報を表示できます。  

2. GitHub から[サンプル ファイル](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json)をダウンロードします。

3. Azure Stack にストレージ アカウントを作成します。 完了したら、BLOB コンテナーを作成します。 アクセス ポリシーを [パブリック] に設定します。  

4. その新しいコンテナーに JSON ファイルをアップロードします。 完了すると、BLOB の URL を表示できるようになります。 BLOB 名を選択してから、BLOB プロパティから URL を選択します。

### <a name="install-or-upgrade-cli"></a>CLI をインストールまたはアップグレードする

開発ワークステーションにサインインし、CLI をインストールします。 Azure Stack には、Azure CLI のバージョン 2.0 以降が必要です。 API プロファイルの最新バージョンには、CLI の最新バージョンが必要です。  CLI は、「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」で説明されている手順を使用してインストールできます。 インストールが正常に完了したことを確認するには、ターミナルまたはコマンド プロンプト ウィンドウを開いて次のコマンドを実行します。

```shell
az --version
```

お使いのコンピューターにインストールされている Azure CLI と依存するその他のライブラリのバージョンが表示されます。

### <a name="install-python-on-windows"></a>Python を Windows にインストールする

1. [Python 3 をお使いのシステムに](https://www.python.org/downloads/)インストールします。

2. PIP をアップグレードします。 PIP は Python 用のパッケージ マネージャーです。 コマンド プロンプトまたは管理者特権の PowerShell プロンプトを開き、次のコマンドを入力します。

    ```PowerShell  
    python -m pip install --upgrade pip
    ```

3. **certifi** モジュールをインストールします。 [certifi](https://pypi.org/project/certifi/) モジュールは、SSL 証明書の信頼性の検証と TLS ホストの ID の確認を行うためのルート証明書のコレクションです。 コマンド プロンプトまたは管理者特権の PowerShell プロンプトを開き、次のコマンドを入力します。

    ```PowerShell
    pip install certifi
    ```

### <a name="install-python-on-linux"></a>Python を Linux にインストールする

1. Ubuntu 16.04 イメージには、既定で Python 2.7 と Python 3.5 がインストールされています。 次のコマンドを実行すると、Python 3 のバージョンを確認できます。

    ```bash  
    python3 --version
    ```

2. PIP をアップグレードします。 PIP は Python 用のパッケージ マネージャーです。 コマンド プロンプトまたは管理者特権の PowerShell プロンプトを開き、次のコマンドを入力します。

    ```bash  
    sudo -H pip3 install --upgrade pip
    ```

3. **certifi** モジュールをインストールします。 [certifi](https://pypi.org/project/certifi/) は、SSL 証明書の信頼性の検証と TLS ホストの ID の確認を行うためのルート証明書のコレクションです。 コマンド プロンプトまたは管理者特権の PowerShell プロンプトを開き、次のコマンドを入力します。

    ```bash
    pip3 install certifi
    ```

### <a name="install-python-on-macos"></a>Python を macOS にインストールする

1. [Python 3 をお使いのシステムに](https://www.python.org/downloads/)インストールします。 Python 3.7 リリースについては、Python.org はダウンロード用に 2 つのバイナリ インストーラー オプションを提供しています。 既定のバリアントは 64 ビット専用で、macOS 10.9 (Mavericks) 以降のシステム上で動作します。 ターミナルを開き、次のコマンドを入力して、Python のバージョンを確認します。

    ```bash  
    python3 --version
    ```

2. PIP をアップグレードします。 PIP は Python 用のパッケージ マネージャーです。 コマンド プロンプトまたは管理者特権の PowerShell プロンプトを開き、次のコマンドを入力します。

    ```bash  
    sudo -H pip3 install --upgrade pip
    ```

3. **certifi** モジュールをインストールします。 [certifi](https://pypi.org/project/certifi/) モジュールは、SSL 証明書の信頼性の検証と TLS ホストの ID の確認を行うためのルート証明書のコレクションです。 コマンド プロンプトまたは管理者特権の PowerShell プロンプトを開き、次のコマンドを入力します。

    ```bash
    pip3 install certifi
    ```

## <a name="windows-azure-ad"></a>Windows (Azure AD)

このセクションでは、ID 管理サービスとして Azure AD を使用していて、Windows マシン上で CLI を使用する場合の CLI の設定について説明します。

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Azure Stack の CA ルート証明書を信頼する

ASDK を使用する場合は、リモート マシン上で CA ルート証明書を信頼する必要があります。 統合システムではそれを行う必要はありません。

Azure Stack の CA ルート証明書を信頼するには、Python の既存の証明書に追加します。

1. マシンで証明書の場所を探します。 この場所は、Python をインストールした場所に応じて異なる場合があります。 コマンド プロンプトまたは管理者特権の PowerShell プロンプトを開き、次のコマンドを入力します。

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    証明書の場所を書き留めておきます。 たとえば、「 `~/lib/python3.5/site-packages/certifi/cacert.pem` 」のように入力します。 特定のパスは、お使いの OS やインストールした Python のバージョンによって異なります。

2. Azure Stack の CA ルート証明書を Python の既存の証明書に追加して信頼します。

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>Azure Stack への接続

1. `az cloud register` コマンドを実行して、Azure Stack 環境を登録します。

    一部のシナリオでは、インターネットへの直接送信接続がプロキシまたはファイアウォール経由でルーティングされ、SSL インターセプトが適用されます。 このような場合は、`az cloud register` コマンドが、"クラウドからエンドポイントを取得できない" といったエラーで失敗する可能性があります。 このエラーを回避するには、次の環境変数を設定します。

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. お客様の環境を登録します。 `az cloud register` を実行するときに、次のパラメーターを使用します。

    | 値 | 例 | 説明 |
    | --- | --- | --- |
    | 環境名 | AzureStackUser | ユーザー環境の場合は、`AzureStackUser` を使用します。 オペレーターの場合は、`AzureStackAdmin` を指定します。 |
    | Resource Manager エンドポイント | https://management.local.azurestack.external | Azure Stack Development Kit (ASDK) の **ResourceManagerUrl** は`https://management.local.azurestack.external/` になります。統合システムの **ResourceManagerUrl** は`https://management.<region>.<fqdn>/` になります。必要なメタデータを取得するには、`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` になります。統合システム エンドポイントに関する質問がある場合は、お客様のクラウド オペレーターにお問い合わせください。 |
    | ストレージ エンドポイント | local.azurestack.external | `local.azurestack.external` は、ASDK の場合です。 統合システムの場合は、お客様のシステムのエンドポイントを使用します。  |
    | Keyvalut のサフィックス | .vault.local.azurestack.external | `.vault.local.azurestack.external` は、ASDK の場合です。 統合システムの場合は、お客様のシステムのエンドポイントを使用します。  |
    | VM イメージのエイリアスのドキュメント エンドポイント | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 仮想マシン イメージのエイリアスを含むドキュメントの URI。 詳細については、「[仮想マシンのエイリアス エンドポイントを設定する](#set-up-the-virtual-machine-aliases-endpoint)」をご覧ください。 |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. 次のコマンドを使用して、アクティブな環境を設定します。

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Azure Stack 固有の API バージョンのプロファイルを使用するようにお使いの環境の構成を更新します。 構成を更新するには、次のコマンドを実行します。

    ```azurecli
    az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >1808 ビルドより前のバージョンの Azure Stack を実行している場合は、**2018-03-01-hybrid** の API バージョンのプロファイルではなく、**2017-03-09-profile** の API バージョンのプロファイルを使用する必要があります。 Azure CLI の最新バージョンを使用する必要があります。
 
1. `az login` コマンドを使用して、Azure Stack 環境にサインインします。 Azure Stack 環境には、ユーザーまたは[サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md)としてサインインできます。 

   - *ユーザー*としてサインインする場合:  

     `az login` コマンド内で直接ユーザー名とパスワードを指定するか、ブラウザーを使用して認証できます。 多要素認証が有効になっているアカウントの場合は、後者を実行する必要があります。

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > お使いのユーザー アカウントで多要素認証が有効になっている場合は、`-u` パラメーターを指定しないで、`az login` コマンドを使用できます。 このコマンドを実行すると、認証で使用する必要がある URL とコードを取得できます。

   - *サービス プリンシパル*を使ってサインインする｡ 
    
     サインインする前に、CLI または [Azure Portal でサービス プリンシパルを作成](azure-stack-create-service-principals.md)してロールに割り当てます。 次のコマンドを使用してサインインします。

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>接続のテスト

すべての設定が完了したら、Azure Stack 内で CLI を使ってリソースを作成してみましょう。 たとえば、アプリケーションのリソース グループを作成して仮想マシンを追加できます。 次のコマンドを使用して、"MyResourceGroup" という名前のリソース グループを作成します。

```azurecli
az group create -n MyResourceGroup -l local
```

リソース グループが正常に作成されると、上記のコマンドによって、新たに作成されたリソースの次のプロパティが出力されます。

![リソース グループ作成の出力](media/azure-stack-connect-cli/image1.png)

## <a name="windows-ad-fs"></a>Windows (AD FS)

このセクションでは、ID 管理サービスとして Active Directory フェデレーション サービス (AD FS) を使用していて、Windows マシン上で CLI を使用する場合の CLI の設定について説明します。

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Azure Stack の CA ルート証明書を信頼する

ASDK を使用する場合は、リモート マシン上で CA ルート証明書を信頼する必要があります。 統合システムではそれを行う必要はありません。

1. マシンで証明書の場所を探します。 この場所は、Python をインストールした場所に応じて異なる場合があります。 コマンド プロンプトまたは管理者特権の PowerShell プロンプトを開き、次のコマンドを入力します。

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    証明書の場所を書き留めておきます。 たとえば、「 `~/lib/python3.5/site-packages/certifi/cacert.pem` 」のように入力します。 特定のパスは、お使いの OS やインストールした Python のバージョンによって異なります。

2. Azure Stack の CA ルート証明書を Python の既存の証明書に追加して信頼します。

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>Azure Stack への接続

1. `az cloud register` コマンドを実行して、Azure Stack 環境を登録します。

    一部のシナリオでは、インターネットへの直接送信接続がプロキシまたはファイアウォール経由でルーティングされ、SSL インターセプトが適用されます。 このような場合は、`az cloud register` コマンドが、"クラウドからエンドポイントを取得できない" といったエラーで失敗する可能性があります。 このエラーを回避するには、次の環境変数を設定します。

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. お客様の環境を登録します。 `az cloud register` を実行するときに、次のパラメーターを使用します。

    | 値 | 例 | 説明 |
    | --- | --- | --- |
    | 環境名 | AzureStackUser | ユーザー環境の場合は、`AzureStackUser` を使用します。 オペレーターの場合は、`AzureStackAdmin` を指定します。 |
    | Resource Manager エンドポイント | https://management.local.azurestack.external | Azure Stack Development Kit (ASDK) の **ResourceManagerUrl** は`https://management.local.azurestack.external/` になります。統合システムの **ResourceManagerUrl** は`https://management.<region>.<fqdn>/` になります。必要なメタデータを取得するには、`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` になります。統合システム エンドポイントに関する質問がある場合は、お客様のクラウド オペレーターにお問い合わせください。 |
    | ストレージ エンドポイント | local.azurestack.external | `local.azurestack.external` は、ASDK の場合です。 統合システムの場合は、お客様のシステムのエンドポイントを使用します。  |
    | Keyvalut のサフィックス | .vault.local.azurestack.external | `.vault.local.azurestack.external` は、ASDK の場合です。 統合システムの場合は、お客様のシステムのエンドポイントを使用します。  |
    | VM イメージのエイリアスのドキュメント エンドポイント | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 仮想マシン イメージのエイリアスを含むドキュメントの URI。 詳細については、「[仮想マシンのエイリアス エンドポイントを設定する](#set-up-the-virtual-machine-aliases-endpoint)」をご覧ください。 |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. 次のコマンドを使用して、アクティブな環境を設定します。

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Azure Stack 固有の API バージョンのプロファイルを使用するようにお使いの環境の構成を更新します。 構成を更新するには、次のコマンドを実行します。

    ```azurecli
    az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >1808 ビルドより前のバージョンの Azure Stack を実行している場合は、**2018-03-01-hybrid** の API バージョンのプロファイルではなく、**2017-03-09-profile** の API バージョンのプロファイルを使用する必要があります。 Azure CLI の最新バージョンを使用する必要があります。

1. `az login` コマンドを使用して、Azure Stack 環境にサインインします。 Azure Stack 環境には、ユーザーまたは[サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md)としてサインインできます。 

   - *ユーザー*としてサインインする場合: 

     `az login` コマンド内で直接ユーザー名とパスワードを指定するか、ブラウザーを使用して認証できます。 多要素認証が有効になっているアカウントの場合は、後者を実行する必要があります。

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-active-directory-resource-id "https://management.adfs.azurestack.local/<tenantID>" --endpoint-active-directory-graph-resource-id "https://graph.local.azurestack.external/" --endpoint-active-directory "https://adfs.local.azurestack.external/adfs/" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>   --profile "2018-03-01-hybrid"
     ```

     > [!NOTE]
     > お使いのユーザー アカウントで多要素認証が有効になっている場合は、`-u` パラメーターを指定しないで、`az login` コマンドを使用できます。 このコマンドを実行すると、認証で使用する必要がある URL とコードを取得できます。

   - *サービス プリンシパル*を使ってサインインする｡ 
    
     サービス プリンシパルのログインに使用する .pem ファイルを用意します。

     プリンシパルが作成されたクライアント マシン上でサービス プリンシパル証明書 (`cert:\CurrentUser\My` にあり､cert 名はプリンシパル名と同じ) を秘密キーを含む pfx としてエクスポートします。

     pfx を pem に変換します (OpenSSL ユーティリティ を使用)。

     CLI にサインインします。
  
     ```azurecli  
     az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
     ```

### <a name="test-the-connectivity"></a>接続のテスト

すべての設定が完了したら、Azure Stack 内で CLI を使ってリソースを作成してみましょう。 たとえば、アプリケーションのリソース グループを作成して仮想マシンを追加できます。 次のコマンドを使用して、"MyResourceGroup" という名前のリソース グループを作成します。

```azurecli
az group create -n MyResourceGroup -l local
```

リソース グループが正常に作成されると、上記のコマンドによって、新たに作成されたリソースの次のプロパティが出力されます。

![リソース グループ作成の出力](media/azure-stack-connect-cli/image1.png)


## <a name="linux-azure-ad"></a>Linux (Azure AD)

このセクションでは、ID 管理サービスとして Azure AD を使用していて、Linux マシン上で CLI を使用する場合の CLI の設定について説明します。

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Azure Stack の CA ルート証明書を信頼する

ASDK を使用する場合は、リモート マシン上で CA ルート証明書を信頼する必要があります。 統合システムではそれを行う必要はありません。

Azure Stack の CA ルート証明書を Python の既存の証明書に追加して信頼します。

1. マシンで証明書の場所を探します。 この場所は、Python をインストールした場所に応じて異なる場合があります。 pip と certifi [モジュールをインストール](#install-python-on-linux)しておく必要があります。 Bash プロンプトから次の Python コマンドを使用できます。

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    証明書の場所を書き留めておきます (例: `~/lib/python3.5/site-packages/certifi/cacert.pem`)。 このパスは、オペレーティング システムと、インストールされている Python のバージョンによって異なります。

2. 証明書のパスを指定して、次の bash コマンドを実行します。

   - リモート Linux マシンの場合:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Azure Stack 環境内の Linux マシンの場合:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Azure Stack への接続

次の手順を使用して Azure Stack に接続します。

1. `az cloud register` コマンドを実行して、Azure Stack 環境を登録します。 一部のシナリオでは、インターネットへの直接送信接続がプロキシまたはファイアウォール経由でルーティングされ、SSL インターセプトが適用されます。 このような場合は、`az cloud register` コマンドが、"クラウドからエンドポイントを取得できない" といったエラーで失敗する可能性があります。 このエラーを回避するには、次の環境変数を設定します。

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. お客様の環境を登録します。 `az cloud register` を実行するときに、次のパラメーターを使用します。

    | 値 | 例 | 説明 |
    | --- | --- | --- |
    | 環境名 | AzureStackUser | ユーザー環境の場合は、`AzureStackUser` を使用します。 オペレーターの場合は、`AzureStackAdmin` を指定します。 |
    | Resource Manager エンドポイント | https://management.local.azurestack.external | Azure Stack Development Kit (ASDK) の **ResourceManagerUrl** は`https://management.local.azurestack.external/` になります。統合システムの **ResourceManagerUrl** は`https://management.<region>.<fqdn>/` になります。必要なメタデータを取得するには、`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` になります。統合システム エンドポイントに関する質問がある場合は、お客様のクラウド オペレーターにお問い合わせください。 |
    | ストレージ エンドポイント | local.azurestack.external | `local.azurestack.external` は、ASDK の場合です。 統合システムの場合は、お客様のシステムのエンドポイントを使用します。  |
    | Keyvalut のサフィックス | .vault.local.azurestack.external | `.vault.local.azurestack.external` は、ASDK の場合です。 統合システムの場合は、お客様のシステムのエンドポイントを使用します。  |
    | VM イメージのエイリアスのドキュメント エンドポイント | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 仮想マシン イメージのエイリアスを含むドキュメントの URI。 詳細については、「[仮想マシンのエイリアス エンドポイントを設定する](#set-up-the-virtual-machine-aliases-endpoint)」をご覧ください。 |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. アクティブな環境を設定します。 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Azure Stack 固有の API バージョンのプロファイルを使用するようにお使いの環境の構成を更新します。 構成を更新するには、次のコマンドを実行します。

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >1808 ビルドより前のバージョンの Azure Stack を実行している場合は、**2018-03-01-hybrid** の API バージョンのプロファイルではなく、**2017-03-09-profile** の API バージョンのプロファイルを使用する必要があります。 Azure CLI の最新バージョンを使用する必要があります。

5. `az login` コマンドを使用して、Azure Stack 環境にサインインします。 Azure Stack 環境には、ユーザーまたは[サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md)としてサインインできます。 

   * *ユーザー*としてサインインする場合: 

     `az login` コマンド内で直接ユーザー名とパスワードを指定するか、ブラウザーを使用して認証できます。 多要素認証が有効になっているアカウントの場合は、後者を実行する必要があります。

     ```azurecli
     az login \
       -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > お使いのユーザー アカウントで多要素認証が有効になっている場合は、`-u` パラメーターを指定しないで、`az login` コマンドを使用できます。 このコマンドを実行すると、認証で使用する必要がある URL とコードを取得できます。
   
   * *サービス プリンシパル*としてサインインする場合:
    
     サインインする前に、CLI または [Azure Portal でサービス プリンシパルを作成](azure-stack-create-service-principals.md)してロールに割り当てます。 次のコマンドを使用してサインインします。

     ```azurecli  
     az login \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
       --service-principal \
       -u <Application Id of the Service Principal> \
       -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>接続のテスト

すべての設定が完了したら、Azure Stack 内で CLI を使ってリソースを作成してみましょう。 たとえば、アプリケーションのリソース グループを作成して仮想マシンを追加できます。 次のコマンドを使用して、"MyResourceGroup" という名前のリソース グループを作成します。

```azurecli
    az group create -n MyResourceGroup -l local
```

リソース グループが正常に作成されると、上記のコマンドによって、新たに作成されたリソースの次のプロパティが出力されます。

![リソース グループ作成の出力](media/azure-stack-connect-cli/image1.png)

## <a name="linux-ad-fs"></a>Linux (AD FS)

このセクションでは、管理サービスとして Active Directory フェデレーション サービス (AD FS) を使用していて、Linux マシン上で CLI を使用する場合の CLI の設定について説明します。

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Azure Stack の CA ルート証明書を信頼する

ASDK を使用する場合は、リモート マシン上で CA ルート証明書を信頼する必要があります。 統合システムではそれを行う必要はありません。

Azure Stack の CA ルート証明書を Python の既存の証明書に追加して信頼します。

1. マシンで証明書の場所を探します。 この場所は、Python をインストールした場所に応じて異なる場合があります。 pip と certifi [モジュールをインストール](#install-python-on-linux)しておく必要があります。 Bash プロンプトから次の Python コマンドを使用できます。

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    証明書の場所を書き留めておきます (例: `~/lib/python3.5/site-packages/certifi/cacert.pem`)。 このパスは、オペレーティング システムと、インストールされている Python のバージョンによって異なります。

2. 証明書のパスを指定して、次の bash コマンドを実行します。

   - リモート Linux マシンの場合:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Azure Stack 環境内の Linux マシンの場合:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Azure Stack への接続

次の手順を使用して Azure Stack に接続します。

1. `az cloud register` コマンドを実行して、Azure Stack 環境を登録します。 一部のシナリオでは、インターネットへの直接送信接続がプロキシまたはファイアウォール経由でルーティングされ、SSL インターセプトが適用されます。 このような場合は、`az cloud register` コマンドが、"クラウドからエンドポイントを取得できない" といったエラーで失敗する可能性があります。 このエラーを回避するには、次の環境変数を設定します。

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. お客様の環境を登録します。 `az cloud register` を実行するときに、次のパラメーターを使用します。

    | 値 | 例 | 説明 |
    | --- | --- | --- |
    | 環境名 | AzureStackUser | ユーザー環境の場合は、`AzureStackUser` を使用します。 オペレーターの場合は、`AzureStackAdmin` を指定します。 |
    | Resource Manager エンドポイント | https://management.local.azurestack.external | Azure Stack Development Kit (ASDK) の **ResourceManagerUrl** は`https://management.local.azurestack.external/` になります。統合システムの **ResourceManagerUrl** は`https://management.<region>.<fqdn>/` になります。必要なメタデータを取得するには、`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` になります。統合システム エンドポイントに関する質問がある場合は、お客様のクラウド オペレーターにお問い合わせください。 |
    | ストレージ エンドポイント | local.azurestack.external | `local.azurestack.external` は、ASDK の場合です。 統合システムの場合は、お客様のシステムのエンドポイントを使用します。  |
    | Keyvalut のサフィックス | .vault.local.azurestack.external | `.vault.local.azurestack.external` は、ASDK の場合です。 統合システムの場合は、お客様のシステムのエンドポイントを使用します。  |
    | VM イメージのエイリアスのドキュメント エンドポイント | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 仮想マシン イメージのエイリアスを含むドキュメントの URI。 詳細については、「[仮想マシンのエイリアス エンドポイントを設定する](#set-up-the-virtual-machine-aliases-endpoint)」をご覧ください。 |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. アクティブな環境を設定します。 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Azure Stack 固有の API バージョンのプロファイルを使用するようにお使いの環境の構成を更新します。 構成を更新するには、次のコマンドを実行します。

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >1808 ビルドより前のバージョンの Azure Stack を実行している場合は、**2018-03-01-hybrid** の API バージョンのプロファイルではなく、**2017-03-09-profile** の API バージョンのプロファイルを使用する必要があります。 Azure CLI の最新バージョンを使用する必要があります。

5. `az login` コマンドを使用して、Azure Stack 環境にサインインします。 Azure Stack 環境には、ユーザーまたは[サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md)としてサインインできます。 

6. サインインします。 

   *  Web ブラウザーとデバイス コードを使用して**ユーザー**として:  

   ```azurecli  
    az login --use-device-code
   ```

   > [!NOTE]  
   >コマンドを実行すると、認証で使用する必要がある URL とコードを取得できます。

   * サービス プリンシパルとして:
        
     サービス プリンシパルのログインに使用する .pem ファイルを用意します。

      * プリンシパルが作成されたクライアント マシン上でサービス プリンシパル証明書 (`cert:\CurrentUser\My` にあり､cert 名はプリンシパル名と同じ) を秘密キーを含む pfx としてエクスポートします。
  
      * pfx を pem に変換します (OpenSSL ユーティリティ を使用)。

     CLI にサインインします。

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

### <a name="test-the-connectivity"></a>接続のテスト

すべての設定が完了したら、Azure Stack 内で CLI を使ってリソースを作成してみましょう。 たとえば、アプリケーションのリソース グループを作成して仮想マシンを追加できます。 次のコマンドを使用して、"MyResourceGroup" という名前のリソース グループを作成します。

```azurecli
  az group create -n MyResourceGroup -l local
```

リソース グループが正常に作成されると、上記のコマンドによって、新たに作成されたリソースの次のプロパティが出力されます。

![リソース グループ作成の出力](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>既知の問題

Azure Stack 内で CLI を使用する場合、次のような既知の問題があります。

 - CLI 対話モード。たとえば、Azure Stack では、`az interactive` コマンドはまだサポートされていません。
 - Azure Stack で使用できる仮想マシン イメージの一覧を取得するには、`az vm image list` コマンドの代わりに、`az vm image list --all` コマンドを使用します。 `--all` オプションを指定すると、Azure Stack 環境内で使用できるイメージのみが応答として返されます。
 - Azure で使用できる仮想マシン イメージの別名は、Azure Stack に適用できない場合があります。 仮想マシン イメージを使用する場合は、イメージの別名の代わりに、URN パラメーター全体 (Canonical:UbuntuServer:14.04.3-LTS:1.0.0) を使用する必要がありますします。 この URN は、`az vm images list` コマンドから派生したイメージ仕様と一致している必要があります。

## <a name="next-steps"></a>次の手順

- [Azure CLI を使用したテンプレートのデプロイ](azure-stack-deploy-template-command-line.md)
- [Azure Stack ユーザー (オペレーター) に対する Azure CLI の有効化](../azure-stack-cli-admin.md)
- [ユーザー アクセス許可の管理](azure-stack-manage-permissions.md) 
