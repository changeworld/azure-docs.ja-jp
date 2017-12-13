---
title: "CLI を使用した Azure Stack への接続 | Microsoft Docs"
description: "クロスプラットフォーム コマンドライン インターフェイス (CLI) を使用して、Azure Stack でリソースを管理およびデプロイする方法"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: sngun
ms.openlocfilehash: 9a0ad3d8c2cdd3cd1d46e789c2b65677ac5a10b1
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2017
---
# <a name="install-and-configure-cli-for-use-with-azure-stack"></a>Azure Stack で使用する CLI をインストールして構成する

この記事では、Azure コマンド ライン インターフェイス (CLI) を使用して、Linux および Mac クライアントのプラットフォームから Azure Stack 開発キットのリソースを管理するプロセスについて説明します。 

## <a name="install-cli"></a>CLI のインストール

次に、開発ワークステーションにサインインして CLI をインストールします。 Azure Stack には、Azure CLI バージョン 2.0 が必要です。 このバージョンは、「[Azure CLI 2.0 のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」で説明されている手順を使用してインストールできます。 インストールが正常に完了したことを確認するには、ターミナルまたはコマンド プロンプト ウィンドウを開いて次のコマンドを実行します。

```azurecli
az --version
```

お使いのコンピューターにインストールされている Azure CLI と依存するその他のライブラリのバージョンが表示されます。

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Azure Stack の CA ルート証明書を信頼する

Azure Stack の CA ルート証明書を Azure Stack オペレーターから取得して信頼します。 Azure Stack の CA ルート証明書を信頼するには、Python の既存の証明書に追加します。 Azure Stack 環境内で作成された Linux マシンから CLI を実行する場合は、次の bash コマンドを実行します。

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

Azure Stack 環境外のマシンから CLI を実行する場合は、まず [Azure Stack への VPN 接続](azure-stack-connect-azure-stack.md)を設定する必要があります。 先ほどエクスポートした PEM 証明書を開発ワークステーションにコピーし、お使いの開発ワークステーションの OS に応じて次のコマンドを実行します。

### <a name="linux"></a>Linux

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="macos"></a>macOS

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="windows"></a>Windows

```powershell
$pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

$root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$root.Import($pemFile)

Write-Host "Extracting needed information from the cert file"
$md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
$sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
$sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

$issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
$subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
$labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
$serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
$md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
$sha1Entry    = [string]::Format("# SHA1 Finterprint: {0}", $sha1Hash)
$sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path root.pem -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated for allowing the azure stack CA root certificate"
```

## <a name="get-the-virtual-machine-aliases-endpoint"></a>仮想マシンのエイリアス エンドポイントを取得する

ユーザーが CLI を使用して仮想マシンを作成するには、Azure Stack のオペレーターに連絡し、仮想マシンのエイリアス エンドポイント URI を取得する必要があります。 たとえば、Azure では、https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json という URI を使用します。 クラウド管理者は Azure Stack Marketplace で使用可能なイメージを使用して、Azure Stack 用に同様のエンドポイントを設定する必要があります。 次のセクションで示すように、ユーザーは、エンドポイント URI を `az cloud register` コマンドの `endpoint-vm-image-alias-doc` パラメーターに渡す必要があります。 
   

## <a name="connect-to-azure-stack"></a>Azure Stack への接続

次の手順を使用して Azure Stack に接続します。

1. `az cloud register` コマンドを実行して、Azure Stack 環境を登録します。
   
   a. *クラウド管理*環境を登録するには、次のコマンドを使用します。

      ```azurecli
      az cloud register \ 
        -n AzureStackAdmin \ 
        --endpoint-resource-manager "https://adminmanagement.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".adminvault.local.azurestack.external" \ 
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

   b. *ユーザー*環境を登録するには、次のコマンドを使用します。

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

2. 次のコマンドを使用して、アクティブな環境を設定します。

   a. *クラウド管理*環境の場合は、次のコマンドを使用します。

      ```azurecli
      az cloud set \
        -n AzureStackAdmin
      ```

   b. *ユーザー*環境の場合は、次のコマンドを使用します。

      ```azurecli
      az cloud set \
        -n AzureStackUser
      ```

3. Azure Stack 固有の API バージョンのプロファイルを使用するようにお使いの環境の構成を更新します。 構成を更新するには、次のコマンドを実行します。

   ```azurecli
   az cloud update \
     --profile 2017-03-09-profile
   ```

4. `az login` コマンドを使用して、Azure Stack 環境にサインインします。 Azure Stack 環境には、ユーザーまたは[サービス プリンシパル](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-application-objects)としてサインインできます。 

   * "*ユーザー*" としてサインインする場合: `az login` コマンド内で直接ユーザー名とパスワードを指定するか、ブラウザーを使用して認証できます。 多要素認証が有効になっているアカウントの場合は、後者を実行する必要があります。

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > お使いのユーザー アカウントで多要素認証が有効になっている場合は、`-u` パラメーターを入力せずに、`az login command` コマンドを使用できます。 コマンドを実行すると、認証で使用する必要がある URL とコードを取得できます。
   
   * "*サービス プリンシパル*" としてサインインする場合: サインインする前に、CLI または [Azure Portal でサービス プリンシパルを作成](azure-stack-create-service-principals.md)してロールに割り当てます。 次のコマンドを使用してサインインします。

      ```azurecli
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```

## <a name="test-the-connectivity"></a>接続のテスト

必要な設定がすべて整ったら、CLI を使って Azure Stack にリソースを作成してみましょう。 たとえば、アプリケーションのリソース グループを作成して仮想マシンを追加できます。 次のコマンドを使用して、"MyResourceGroup" という名前のリソース グループを作成します。

```azurecli
az group create \
  -n MyResourceGroup -l local
```

リソース グループが正常に作成されると、上記のコマンドによって、新たに作成されたリソースの次のプロパティが出力されます。

![リソース グループ作成の出力](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>既知の問題
Azure Stack で CLI を使うときに注意する必要がある既知の問題がいくつかあります。

* CLI 対話モード ( `az interactive`コマンド) はまだ Azure Stack でサポートされていません。
* Azure Stack で使用できる仮想マシン イメージの一覧を取得するには、`az vm image list` コマンドの代わりに、`az vm images list --all` コマンドを使用します。 `--all` オプションを指定すると、応答に、Azure Stack 環境で使用できるイメージのみが返されます。 
* Azure で使用できる仮想マシン イメージの別名は、Azure Stack に適用できない場合があります。 仮想マシン イメージを使用する場合は、イメージの別名の代わりに、URN パラメーター全体 (Canonical:UbuntuServer:14.04.3-LTS:1.0.0) を使用する必要がありますします。 この URN は、`az vm images list` コマンドから派生したイメージ仕様と一致している必要があります。
* 既定で、CLI 2.0 は、既定の仮想マシン イメージのサイズとして "Standard_DS1_v2" を使用します。 ただし、このサイズはまだ Azure Stack で使用できないため、仮想マシンを作成するときに `--size` パラメーターを明示的に指定する必要があります。 Azure Stack で使用できる仮想マシンのサイズの一覧を取得するには、`az vm list-sizes --location <locationName>` コマンドを使用します。


## <a name="next-steps"></a>次のステップ

[Azure CLI を使用したテンプレートのデプロイ](azure-stack-deploy-template-command-line.md)

[ユーザー アクセス許可の管理](azure-stack-manage-permissions.md)

