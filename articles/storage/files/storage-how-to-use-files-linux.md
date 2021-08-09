---
title: Linux で Azure Files を使用する | Microsoft Docs
description: Linux で SMB 経由で Azure File 共有をマウントする方法について説明します。 前提条件の一覧を参照してください。 Linux クライアントでの SMB のセキュリティに関する考慮事項を確認します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/05/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7e02d85fe5385b8918fbfdb037382aeeef444267
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110088356"
---
# <a name="use-azure-files-with-linux"></a>Linux で Azure Files を使用する
[Azure Files](storage-files-introduction.md) は、Microsoft の使いやすいクラウド ファイル システムです。 Azure ファイル共有は、[SMB カーネル クライアント](https://wiki.samba.org/index.php/LinuxCIFS)を使用して Linux ディストリビューションにマウントできます。

Linux で Azure ファイル共有をマウントするには、SMB 3.1.1 を使用することをお勧めします。 既定では、転送中の暗号化が Azure Files で必要になりますが、これがサポートされるのは SMB 3.0 以降となります。 Azure Files では SMB 2.1 (転送中の暗号化をサポートしていない) もサポートしていますが、セキュリティ上の理由から、別の Azure リージョンまたはオンプレミスから SMB 2.1 を使用して Azure ファイル共有をマウントすることはできません。 アプリケーションで SMB 2.1 が特に必要な場合を除き、SMB 3.1.1 を使用してください。

| Distribution | SMB 3.1.1 | SMB 3.0 |
|-|-----------|---------|
| Linux カーネル バージョン | <ul><li>3\.1.1 の基本サポート: 4.17</li><li>既定でのマウント: 5.0</li><li>AES-128-GCM 暗号化: 5.3</li></ul> | <ul><li>3\.0 の基本サポート: 3.12</li><li>AES-128-CCM 暗号化: 4.11</li></ul> |
| [Ubuntu](https://wiki.ubuntu.com/Releases) | AES-128-GCM 暗号化: 18.04.5 LTS 以上 | AES-128-CCM 暗号化: 16.04.4 LTS 以上 |
| [Red Hat Enterprise Linux (RHEL)](https://access.redhat.com/articles/3078) | <ul><li>基本: 8.0 以上</li><li>既定でのマウント: 8.2 以上</li><li>AES-128-GCM 暗号化: 8.2 以上</li></ul> | 7.5+ |
| [Debian](https://www.debian.org/releases/) | 基本: 10 以上 | AES-128-CCM 暗号化: 10 以上 |
| [SUSE Linux Enterprise Server](https://www.suse.com/support/kb/doc/?id=000019587) | AES-128-GCM 暗号化: 15 SP2 以上 | AES-128-CCM 暗号化: 12 SP2 以上 |

お使いの Linux ディストリビューションが上記の表に記載されていない場合は、`uname` コマンドを使用して Linux カーネル バージョンを確認してください。

```bash
uname -r
```

> [!Note]  
> SMB 2.1 のサポートが追加されたのは、Linux カーネル バージョン 3.7 となります。 お使いの Linux カーネルのバージョンが 3.7 より大きい場合、SMB 2.1 がサポートされています。

## <a name="prerequisites"></a>前提条件
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**cifs-utils パッケージがインストールされていることを確認します。**  
    cifs-utils パッケージは、選択した Linux ディストリビューションのパッケージ マネージャーを使用してインストールできます。 

    **Ubuntu** および **Debian** では、`apt` パッケージ マネージャーを使用します。

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    **Red Hat Enterprise Linux 8 以上** では、`dnf` パッケージ マネージャーを使用します。

    ```bash
    sudo dnf install cifs-utils
    ```

    それより前のバージョンの **Red Hat Enterprise Linux** では、`yum` パッケージ マネージャーを使用します。

    ```bash
    sudo yum install cifs-utils 
    ```

    **SUSE Linux Enterprise Server** では、`zypper` パッケージ マネージャーを使用します。

    ```bash
    sudo zypper install cifs-utils
    ```

    他のディストリビューションでは、適切なパッケージ マネージャーを使用するか、[ソースからコンパイル](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)します。

* **Azure コマンド ライン インターフェイス (CLI) の最新バージョン。** Azure CLI をインストールする方法の詳細については、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照し、ご利用のオペレーティング システムを選択してください。 PowerShell 6 以降で Azure PowerShell モジュールを使用することは可能ですが、この記事の手順は Azure CLI 用に提供されています。

* **ポート 445 が開いていることを確認する**: SMB は、TCP ポート 445 経由で通信します。ファイアウォールによってクライアント コンピューターの TCP ポート 445 がブロックされないことを確認してください。  `<your-resource-group>` と `<your-storage-account>` を置き換え、次のスクリプトを実行します。

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    接続に成功した場合、次のような出力が表示されます。

    ```ouput
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    企業ネットワーク上でポート 445 を開くことができない場合、または ISP によってブロックされている場合は、VPN 接続または ExpressRoute を使用してポート 445 を回避することができます。 詳細については、[直接の Azure ファイル共有アクセスのネットワークに関する考慮事項](storage-files-networking-overview.md)に関するページを参照してください。

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Azure ファイル共有を mount を使用してオンデマンドでマウントする
Linux OS でファイル共有をマウントすると、リモートのファイル共有がローカル ファイル システム内のフォルダーとして表示されます。 ファイル共有は、システム上の任意の場所にマウントできます。 次の例では、`/mount` パス下にマウントします。 これは、`$mntRoot` 変数を変更することで任意のパスに変更できます。

`<resource-group-name>`、`<storage-account-name>`、`<file-share-name>` をお使いの環境に適した情報に置き換えます。

```bash
resourceGroupName="<resource-group-name>"
storageAccountName="<storage-account-name>"
fileShareName="<file-share-name>"

mntRoot="/mount"
mntPath="$mntRoot/$storageAccountName/$fileShareName"

sudo mkdir -p $mntPath
```

次に、`mount` コマンドを使用してファイル共有をマウントします。 次の例では、`$smbPath` コマンドがストレージ アカウントのファイル エンドポイントの完全修飾ドメイン名を使用して設定されています。また、`$storageAccountKey` には、ストレージ アカウント キーが設定されています。 

# <a name="smb-311"></a>[SMB 3.1.1](#tab/smb311)
> [!Note]  
> Linux カーネル バージョン 5.0 以降でネゴシエートされる既定のプロトコルは SMB 3.1.1 です。 5\.0 より前のバージョンの Linux カーネルを使用する場合は、mount のオプション リストに `vers=3.1.1` を指定してください。  

```bash
# This command assumes you have logged in with az login
httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

sudo mount -t cifs $smbPath $mntPath -o username=$storageAccountName,password=$storageAccountKey,serverino
```

# <a name="smb-30"></a>[SMB 3.0](#tab/smb30)
```bash
# This command assumes you have logged in with az login
httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

# <a name="smb-21"></a>[SMB 2.1](#tab/smb21)
```bash
# This command assumes you have logged in with az login
httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

sudo mount -t cifs $smbPath $mntPath -o vers=2.1,username=$storageAccountName,password=$storageAccountKey,serverino
```

---

`mount` コマンドのマウント オプションには、`uid`/`gid` または `dir_mode` および `file_mode` を使用してアクセス許可を設定できます。 アクセス許可を設定する方法の詳細については、Wikipedia の「[UNIX numeric notation](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation)」 (UNIX の数値表記) を参照してください。

必要に応じて、同じ Azure ファイル共有を複数のマウント ポイントにマウントすることもできます。 Azure ファイル共有の使用を完了したら、`sudo umount $mntPath` を使用して共有のマウントを解除してください。

## <a name="automatically-mount-file-shares"></a>ファイル共有を自動的にマウントする
Linux OS でファイル共有をマウントすると、リモートのファイル共有がローカル ファイル システム内のフォルダーとして表示されます。 ファイル共有は、システム上の任意の場所にマウントできます。 次の例では、`/mount` パス下にマウントします。 これは、`$mntRoot` 変数を変更することで任意のパスに変更できます。

```bash
mntRoot="/mount"
sudo mkdir -p $mntRoot
```

Azure ファイル共有を Linux にマウントするには、ファイル共有のユーザー名としてストレージ アカウント名を、また、パスワードとしてストレージ アカウント キーを使用します。 ストレージ アカウントの資格情報は時間の経過に伴って変化する可能性があるため、ストレージ アカウントの資格情報は、マウントの構成とは別に保管しておく必要があります。 

次の例は、資格情報を格納するファイルの作成方法を示しています。 `<resource-group-name>` と `<storage-account-name>` をお使いの環境に適した情報に置き換えることを忘れないでください。

```bash
resourceGroupName="<resource-group-name>"
storageAccountName="<storage-account-name>"

# Create a folder to store the credentials for this storage account and
# any other that you might set up.
credentialRoot="/etc/smbcredentials"
sudo mkdir -p "/etc/smbcredentials"

# Get the storage account key for the indicated storage account.
# You must be logged in with az login and your user identity must have 
# permissions to list the storage account keys for this command to work.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# Create the credential file for this individual storage account
smbCredentialFile="$credentialRoot/$storageAccountName.cred"
if [ ! -f $smbCredentialFile ]; then
    echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
    echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
else 
    echo "The credential file $smbCredentialFile already exists, and was not modified."
fi

# Change permissions on the credential file so only root can read or modify the password file.
sudo chmod 600 $smbCredentialFile
```

ファイル共有を自動的にマウントするには、`/etc/fstab` ユーティリティによる静的マウントを使用する方法と `autofs` ユーティリティによる動的マウントを使用する方法とがあります。 

### <a name="static-mount-with-etcfstab"></a>/etc/fstab を使用した静的マウント
以前の環境を使用して、ストレージ アカウントやファイル共有のフォルダーを指定のマウント フォルダー下に作成します。 `<file-share-name>` は、Azure ファイル共有の適切な名前に置き換えてください。

```bash
fileShareName="<file-share-name>"

mntPath="$mntRoot/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath
```

最後に、Azure ファイル共有のレコードを `/etc/fstab` ファイルに作成します。 次のコマンドでは、Linux のファイルとフォルダーに既定のアクセス許可である 0755 が使用されています。これは、所有者 (ファイルまたはディレクトリの Linux 所有者に基づく) には読み取り、書き込み、および実行が、所有者グループのユーザーには読み取りおよび実行が、システム上の他のユーザーには読み取りおよび実行が許可されることを意味します。 別途、mount の `uid` と `gid` または `dir_mode` と `file_mode` で必要に応じてアクセス許可を設定することもできます。 アクセス許可を設定する方法の詳細については、Wikipedia の「[UNIX numeric notation](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation)」 (UNIX の数値表記) を参照してください。

```bash
httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
    echo "$smbPath $mntPath cifs nofail,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
else
    echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
fi

sudo mount -a
```

> [!Note]  
> Linux カーネル バージョン 5.0 以降でネゴシエートされる既定のプロトコルは SMB 3.1.1 です。 `vers` マウント オプションを使用して別のプロトコル バージョンを指定できます (プロトコルのバージョンは `3.1.1`、`3.0`、`2.1`)。

### <a name="dynamically-mount-with-autofs"></a>autofs を使用した動的マウント
`autofs` ユーティリティを使用してファイル共有を動的にマウントするには、パッケージ マネージャーを使用して、任意の Linux ディストリビューションにユーティリティをインストールします。  

**Ubuntu** と **Debian** ディストリビューションでは、`apt` パッケージ マネージャーを使用します。

```bash
sudo apt update
sudo apt install autofs
```

**Red Hat Enterprise Linux 8 以上** では、`dnf` パッケージ マネージャーを使用します。
```bash
sudo dnf install autofs
```

それより前のバージョンの **Red Hat Enterprise Linux** では、`yum` パッケージ マネージャーを使用します。

```bash
sudo yum install autofs 
```

**SUSE Linux Enterprise Server** では、`zypper` パッケージ マネージャーを使用します。
```bash
sudo zypper install autofs
```

次に、`autofs` 構成ファイルを更新します。 

```bash
fileShareName="<file-share-name>"

httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

echo "$fileShareName -fstype=cifs,credentials=$smbCredentialFile :$smbPath" > /etc/auto.fileshares

echo "/fileshares /etc/auto.fileshares --timeout=60" > /etc/auto.master
```

最後の手順は、`autofs` サービスを再起動することです。

```bash
sudo systemctl restart autofs
```

## <a name="securing-linux"></a>Linux のセキュリティ保護
SMB を使って Azure ファイル共有をマウントするためには、ポート 445 にアクセスできることが必要です。 ポート 445 は、SMB 1 に固有のセキュリティ リスクから、多くの組織でブロックされています。 SMB 1 は、数多くの Linux ディストリビューションに備わっているレガシ ファイル システム プロトコルで、CIFS (Common Internet File System) と呼ばれることもあります。 SMB 1 は、旧式で効率が悪く、また何よりもセキュリティに不安があるプロトコルです。 さいわいなことに、Azure Files は SMB 1 をサポートしておらず、Linux カーネル バージョン 4.18 以降では、Linux で SMB 1 を無効にすることができます。 運用環境で SMB ファイル共有を使用する前に、Linux クライアント上で SMB 1 を無効にすることを常に[強くお勧めします](https://aka.ms/stopusingsmb1)。

Linux カーネル 4.18 以降、レガシの理由から `cifs` と呼ばれる SMB カーネル モジュールでは、`disable_legacy_dialects` と呼ばれる新しいモジュール パラメーター (各種外部ドキュメントでは "*parm*" と呼ばれることが多い) を公開しています。 Linux カーネル 4.18 で導入されましたが、一部のベンダーでは、サポート対象の旧カーネルにこの変更を移植しています。 便宜のため、次の表では、よく知られた Linux ディストリビューションでこのモジュール パラメーターを使用できるかどうかについて詳しく示しています。

| Distribution | SMB 1 を無効にできる |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | No |
| Ubuntu 18.04 | Yes |
| Ubuntu 19.04+ | Yes |
| Debian 8-9 | No |
| Debian 10+ | Yes |
| Fedora 29+ | Yes |
| CentOS 7 | No | 
| CentOS 8+ | Yes |
| Red Hat Enterprise Linux 6.x-7.x | No |
| Red Hat Enterprise Linux 8+ | Yes |
| openSUSE Leap 15.0 | No |
| openSUSE Leap 15.1+ | Yes |
| openSUSE Tumbleweed | Yes |
| SUSE Linux Enterprise 11.x-12.x | No |
| SUSE Linux Enterprise 15 | No |
| SUSE Linux Enterprise 15.1 | No |

次のコマンドを使用して、Linux ディストリビューションで `disable_legacy_dialects` モジュール パラメーターがサポートされているかどうかを確認できます。

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

このコマンドを実行すると、次のメッセージが出力されます。

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

SMB 1 を無効にする前に、SMB モジュールがご利用のシステムに現在読み込まれていないことを確認する必要があります (これは、SMB 共有をマウントしている場合に自動的に発生します)。 これを行うには、次のコマンドを使用します。SMB が読み込まれていない場合は、何も出力されません。

```bash
lsmod | grep cifs
```

モジュールをアンロードするには、最初にすべての SMB 共有のマウントを解除します (前述のように、`umount` コマンドを使用します)。 次のコマンドを使用して、システム上にマウントされているすべての SMB 共有を特定できます。

```bash
mount | grep cifs
```

すべての SMB ファイル共有のマウントを解除したら、モジュールをアンロードしても安全です。 そのためには、 `modprobe` コマンドを使用します。

```bash
sudo modprobe -r cifs
```

`modprobe` コマンドを使用して、SMB 1 をアンロードしてモジュールを手動で読み込むことができます。

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

最後に、`/sys/module/cifs/parameters` の読み込み済みパラメーターを参照すると、パラメーターを使用して SMB モジュールが読み込まれていることを確認できます。

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Ubuntu および Debian ベースのディストリビューションで SMB 1 を永続的に無効にするには、設定を使用して `/etc/modprobe.d/local.conf` という新しいファイルを作成する必要があります (他のモジュールのカスタム オプションがまだない場合)。 これを行うには、次のコマンドを使います。

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

SMB モジュールを読み込むと、これが動作していることを確認できます。

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="next-steps"></a>次のステップ
Azure Files の詳細については、次のリンクをご覧ください。

* [Azure Files のデプロイの計画](storage-files-planning.md)
* [FAQ](./storage-files-faq.md)
* [トラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)
