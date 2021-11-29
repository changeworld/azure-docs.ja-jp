---
title: Azure Lab Services で外部ファイル ストレージを使用する | Microsoft Docs
description: Lab Services で外部ファイル ストレージを使用するラボを設定する方法について説明します。
author: emaher
ms.topic: how-to
ms.date: 03/30/2021
ms.author: enewman
ms.openlocfilehash: b77f478e604366a455b4f7f3d3173dbc62eeba68
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132722603"
---
# <a name="use-external-file-storage-in-lab-services"></a>Lab Services で 外部ファイル ストレージを使用する

この記事では、Azure Lab Services を使用する際の外部ファイル ストレージでのいくつかのオプションについて説明します。 [Azure Files](https://azure.microsoft.com/services/storage/files/) では、[SMB 2.1 と SMB 3.0 を介してアクセスできる](../storage/files/storage-how-to-use-files-windows.md)クラウド内のフル マネージド ファイル共有を提供します。 Azure Files 共有は、仮想ネットワーク内でパブリックまたはプライベートで接続できます。 また、ファイル共有への接続に学生の Active Directory 資格情報を使用するように共有を構成することもできます。 Linux マシンを使用している場合は、Azure Lab Services での外部ファイル ストレージに、NFS ボリュームを含む Azure NetApp Files を使用することもできます。  

## <a name="which-solution-to-use"></a>使用するソリューション

各ソリューションには、さまざまな要件と機能があります。 次の表に、各ソリューションで考慮すべき重要な点を示します。  

|     解決策    |    知っておくべき重要なこと    |
| -------------- | ------------------------ |
| [パブリック エンドポイントを使用した Azure Files 共有](#azure-files-share) | <ul><li>すべてのユーザーに読み取り/書き込みアクセス権があります。</li><li>仮想ネットワークのピアリングは必要ありません。</li><li>ラボ VM だけでなく、すべての VM からアクセスできます。</li><li>Linux を使用している場合、学生はストレージ アカウント キーにアクセスできます。</li></ul> |
| [プライベート エンドポイントを使用した Azure Files 共有](#azure-files-share) | <ul><li>すべてのユーザーに読み取り/書き込みアクセス権があります。</li><li>仮想ネットワークのピアリングが必要です。</li><li>ストレージ アカウントと同じネットワーク (またはピアリングされたネットワーク) 上の VM にのみアクセスできます。</li><li>Linux を使用している場合、学生はストレージ アカウント キーにアクセスできます。</li></ul> |
| [ID ベースの承認を使用した Azure Files](#azure-files-with-identity-based-authorization) | <ul><li>フォルダーまたはファイルに対して、読み取りまたは読み取り/書き込みアクセス許可を設定できます。</li><li>仮想ネットワークのピアリングが必要です。</li><li>ストレージ アカウントは Active Directory に接続されている必要があります。</li><li>ラボ VM はドメインに参加している必要があります。</li><li>学生がファイル共有に接続するためにストレージ アカウント キーは使用されません。</li></ul> |
| [NFS ボリュームを含む Azure NetApp Files](#azure-netapp-files-with-nfs-volumes) | <ul><li>ボリュームに対して読み取りまたは読み取り/書き込みのいずれかのアクセス権を設定できます。</li><li>アクセス許可は、学生用 VM の IP アドレスを使用して設定されます。</li><li>仮想ネットワークのピアリングが必要です。</li><li>Azure NetApp Files サービスを使用するために登録が必要な場合があります。</li><li>Linux のみ。</li></ul>

外部ストレージの使用コストは、Azure Lab Services を使用するコストに含まれていません。  価格の詳細については、「[Azure Files の料金](https://azure.microsoft.com/pricing/details/storage/files/)」と「[Azure NetApp Files の価格](https://azure.microsoft.com/pricing/details/netapp/)」を参照してください。

## <a name="azure-files-share"></a>Azure Files 共有

Azure Files 共有には、パブリックまたはプライベート エンドポイントを使用してアクセスします。 ストレージ アカウント キーをパスワードとして使用して共有をマウントします。 この方法を使用すると、すべてのユーザーにファイル共有への読み取り/書き込みアクセス権が与えられます。

Azure Files 共有にパブリック エンドポイントを使用する場合は、次の点に注意してください。

- ストレージ アカウントの仮想ネットワークは、ラボ アカウントとピアリングする必要はありません。  ファイル共有は、テンプレート VM が発行される前であればいつでも作成できます。
- ユーザーがストレージ アカウント キーを持っていれば、どのマシンからでもファイル共有にアクセスできます。  
- Linux の学生は、ストレージ アカウント キーを確認できます。 Azure Files 共有をマウントするための資格情報は、Linux VM 上の `{file-share-name}.cred` に格納されており、sudo で読み取ることができます。 学生には Azure Lab Services VM で sudo アクセスが既定で付与されるので、ストレージ アカウント キーを読み取ることができます。 ストレージ アカウントのエンドポイントがパブリックの場合、学生は学生用 VM の外部にあるファイル共有にアクセスできます。 クラスが終了したらストレージ アカウント キーをローテーションし、プライベート ファイル共有を使用することを検討してください。

Azure Files 共有にプライベート エンドポイントを使用する場合は、次の点に注意してください。

- アクセスは、プライベート ネットワークから送信されるトラフィックに限定され、パブリック インターネット経由ではアクセスできません。 プライベート仮想ネットワーク内の VM、プライベート仮想ネットワークにピアリングされたネットワーク内の VM、またはプライベート ネットワーク用に VPN に接続されているマシンのみが、ファイル共有にアクセスできます。  
- Linux の学生は、ストレージ アカウント キーを確認できます。 Azure Files 共有をマウントするための資格情報は、Linux VM 上の `{file-share-name}.cred` に格納されており、sudo で読み取ることができます。 学生には Azure Lab Services VM で sudo アクセスが既定で付与されるので、ストレージ アカウント キーを読み取ることができます。 クラスが終了した後で、ストレージ アカウント キーをローテーションすることを検討してください。
- この方法では、ファイル共有仮想ネットワークがラボ アカウントとピアリングされている必要があります。 Azure Storage アカウントの仮想ネットワークは、ラボを作成する前に、ラボ アカウントの仮想ネットワークとピアリングされている必要があります。

> [!NOTE]
> 既定では、Standard ファイル共有を最大 5 TiB にまたがるようにすることができます。 最大 100 TiB にまたがるファイル共有を作成する方法については、「[Azure ファイル共有を作成する](../storage/files/storage-how-to-create-file-share.md)」を参照してください。

Azure Files 共有に接続された VM を作成するには、こちらの手順に従ってください。

1. [Azure Storage アカウント](../storage/files/storage-how-to-create-file-share.md)を作成します。 **[接続方法]** ページで、**パブリック エンドポイント** または **プライベート エンドポイント** を選択します。
2. プライベート メソッドを選択した場合は、仮想ネットワークからファイル共有にアクセスできるようにするために、[プライベート エンドポイント](../private-link/tutorial-private-endpoint-storage-portal.md)を作成します。
3. [Azure ファイル共有](../storage/files/storage-how-to-create-file-share.md)を作成します。 パブリック エンドポイントを使用している場合、ファイル共有には、ストレージ アカウントのパブリック ホスト名を指定してアクセスできます。  プライベート エンドポイントを使用している場合、ファイル共有にはプライベート IP アドレスを使用して到達できます。  
4. Azure ファイル共有をテンプレート VM にマウントします。
    - [Windows](../storage/files/storage-how-to-use-files-windows.md)
    - [Linux](../storage/files/storage-how-to-use-files-linux.md)。 学生用 VM でのマウントの問題を回避するには、「[Linux で Azure Files を使用する](#use-azure-files-with-linux)」を参照してください。
5. テンプレート VM を[発行](how-to-create-manage-template.md#publish-the-template-vm)します。

> [!IMPORTANT]
> ポート 445 を介した発信 SMB 接続が Windows Defender ファイアウォールでブロックされていないことを確認します。 既定では、SMB は Azure VM に対して許可されます。

### <a name="use-azure-files-with-linux"></a>Linux で Azure Files を使用する

既定の手順を使用して Azure Files 共有をマウントした場合、テンプレートが発行されると、学生用 VM ではファイル共有が消えたように見えます。 次の変更したスクリプトでは、この問題に対処しています。  

パブリック エンドポイントを使用したファイル共有の場合
```bash
#!/bin/bash

# Assign variables values for your storage account and file share
storage_account_name=""
storage_account_key=""
fileshare_name=""

# Do not use 'mnt' for mount directory.
# Using ‘mnt’ will cause issues on student VMs.
mount_directory="prm-mnt" 

sudo mkdir /$mount_directory/$fileshare_name
if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
fi
if [ ! -f "/etc/smbcredentials/$storage_account_name.cred" ]; then
    sudo bash -c "echo ""username=$storage_account_name"" >> /etc/smbcredentials/$storage_account_name.cred"
    sudo bash -c "echo ""password=$storage_account_key"" >> /etc/smbcredentials/$storage_account_name.cred"
fi
sudo chmod 600 /etc/smbcredentials/$storage_account_name.cred

sudo bash -c "echo ""//$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name cifs nofail,vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino"" >> /etc/fstab"
sudo mount -t cifs //$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name -o vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino
```

プライベート エンドポイントを使用したファイル共有の場合
```bash
#!/bin/bash

# Assign variables values for your storage account and file share
storage_account_name=""
storage_account_ip=""
storage_account_key=""
fileshare_name=""

# Do not use 'mnt' for mount directory.
# Using ‘mnt’ will cause issues on student VMs.
mount_directory="prm-mnt" 

sudo mkdir /$mount_directory/$fileshare_name
if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
fi
if [ ! -f "/etc/smbcredentials/$storage_account_name.cred" ]; then
    sudo bash -c "echo ""username=$storage_account_name"" >> /etc/smbcredentials/$storage_account_name.cred"
    sudo bash -c "echo ""password=$storage_account_key"" >> /etc/smbcredentials/$storage_account_name.cred"
fi
sudo chmod 600 /etc/smbcredentials/$storage_account_name.cred

sudo bash -c "echo ""//$storage_account_ip/$fileshare_name /$mount_directory/$fileshare_name cifs nofail,vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino"" >> /etc/fstab"
sudo mount -t cifs //$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name -o vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino
```

Azure Files 共有を `/mnt` ディレクトリにマウントするテンプレート VM が既に発行されている場合、学生は次のいずれかを実行できます。

- `/mnt` をマウントする命令を `/etc/fstab` ファイルの先頭に移動する。  
- `/mnt/{file-share-name}` をマウントする命令を `/prm-mnt/{file-share-name}` などの別のディレクトリに変更する。

学生は `mount -a` を実行して、ディレクトリを再マウントする必要があります。

一般的な情報については、「[Linux で Azure Files を使用する](../storage/files/storage-how-to-use-files-linux.md)」を参照してください。

## <a name="azure-files-with-identity-based-authorization"></a>ID ベースの承認を使用した Azure Files

次の両方に当てはまる場合、Azure Files 共有には、Active Directory 認証を使用してアクセスできます。

- 学生の VM がドメインに参加している。
- Active Directory 認証が、ファイル共有をホストする [Azure Storage アカウントで有効](../storage/files/storage-files-active-directory-overview.md)になっている。  

ネットワーク ドライブは、ストレージ アカウントのキーではなく、ユーザーの ID を使用して仮想マシンにマウントされます。 パブリックまたはプライベート エンドポイントにより、ストレージ アカウントにアクセスできます。

以下の重要な点に注意してください。

- ディレクトリまたはファイル レベルでアクセス許可を設定できます。
- ファイル共有への認証には、現在のユーザー資格情報を使用できます。

パブリック エンドポイントの場合、ストレージ アカウントの仮想ネットワークは、ラボ アカウントにピアリングされている必要はありません。 テンプレート VM が発行される前であればいつでも、ファイル共有を作成できます。

プライベート エンドポイントの場合:

- アクセスは、プライベート ネットワークから送信されるトラフィックに限定され、パブリック インターネット経由ではアクセスできません。 プライベート仮想ネットワーク内の VM、プライベート仮想ネットワークにピアリングされたネットワーク内の VM、またはプライベート ネットワーク用に VPN に接続されているマシンのみが、ファイル共有にアクセスできます。  
- この方法では、ファイル共有仮想ネットワークがラボ アカウントとピアリングされている必要があります。 Azure Storage アカウントの仮想ネットワークは、ラボを作成する前に、ラボ アカウントの仮想ネットワークとピアリングされている必要があります。

Active Directory 認証に対して有効になっている Azure Files を作成し、ラボ VM をドメインに参加させるには、次の手順に従います。

1. [Azure Storage アカウント](../storage/files/storage-how-to-create-file-share.md)を作成します。
2. プライベート メソッドを選択した場合は、仮想ネットワークからファイル共有にアクセスできるようにするために、[プライベート エンドポイント](../private-link/tutorial-private-endpoint-storage-portal.md)を作成します。 [プライベート DNS ゾーン](../dns/private-dns-privatednszone.md)を作成するか、既存のものを使用します。 プライベート Azure DNS ゾーンでは、仮想ネットワーク内での名前解決が提供されます。
3. [Azure ファイル共有](../storage/files/storage-how-to-create-file-share.md)を作成します。
4. ID ベースの承認を有効にするには、次の手順に従ってください。 オンプレミスで Active Directory を使用し、Azure Active Directory (Azure AD) と同期させている場合は、「[SMB を使用した Azure ファイル共有へのオンプレミスの Active Directory Domain Services 認証](../storage/files/storage-files-identity-auth-active-directory-enable.md)」を参照してください。 Azure AD のみを使用している場合は、「[Azure Files に対する Azure Active Directory Domain Services 認証を有効にする](../storage/files/storage-files-identity-auth-active-directory-domain-service-enable.md)」を参照してください。
    >[!IMPORTANT]
    >Active Directory インスタンスを管理するチームに相談して、手順に記載されているすべての前提条件が満たされていることを確認してください。
5. Azure で SMB 共有のアクセス許可ロールを割り当てます。 各ロールに付与されるアクセス許可の詳細については、[共有レベルのアクセス許可](../storage/files/storage-files-identity-ad-ds-assign-permissions.md)に関するページを参照してください。
    - **ストレージ ファイル データの SMB 共有の管理者特権共同作成者** ロールは、ファイル共有のコンテンツのアクセス許可を設定するユーザーまたはグループに割り当てる必要があります。
    - **ストレージ ファイル データの SMB 共有の共同作成者** ロールは、ファイル共有のファイルを追加または編集する必要がある学生に割り当てる必要があります。
    - **ストレージ ファイル データの SMB 共有の閲覧者** ロールは、ファイル共有のファイルの読み取りだけが必要な学生に割り当てる必要があります。
6. ファイル共有に対してディレクトリ レベルまたはファイル レベルのアクセス許可を設定します。 ファイル共有へのネットワーク アクセスが可能なドメインに参加しているマシンからアクセス許可を設定する必要があります。 ディレクトリ レベルまたはファイル レベルのアクセス許可を変更するには、Azure AD の資格情報ではなく、ストレージ キーを使用してファイル共有をマウントします。 アクセス許可を割り当てるには、[Set-Acl](/powershell/module/microsoft.powershell.security/set-acl) PowerShell コマンドを使用するか、Windows で [icacls](/windows-server/administration/windows-commands/icacls) を使用します。
7. ストレージ アカウントの[仮想ネットワーク](how-to-connect-peer-virtual-network.md)をラボ アカウントにピアリングします。
8. [クラスルーム ラボを作成します](how-to-manage-classroom-labs.md)。
9. 学生がネットワーク ドライブに接続するために実行できるスクリプトをテンプレート VM に保存します。 サンプル スクリプトを取得するには、次のようにします。
    1. Azure portal でストレージ アカウントを開きます。
    1. **[File サービス]** で **[ファイル共有]** を選びます。
    1. 接続先の共有を見つけ、右端にある省略記号ボタンを選択し、 **[接続]** を選択します。
    1. Windows、Linux、macOS の手順が表示されます。 Windows を使用している場合は、 **[認証方法]** を **[Active Directory]** に設定します。
    1. 例に含まれているコードをコピーし、Windows の場合は `.ps1` ファイルに、Linux の場合は `.sh` ファイルに保存します。
10. テンプレート マシンで、スクリプトをダウンロードして実行し、[学生のマシンをドメインに参加](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Scripts/ActiveDirectoryJoin/README.md#usage)させます。 `Join-AzLabADTemplate` スクリプトを実行すると、自動的に[テンプレート VM が発行](how-to-create-manage-template.md#publish-the-template-vm)されます。  
    > [!NOTE]
    > テンプレート マシンはドメインに参加していません。 共有上のファイルを確認するには、インストラクターが学生用 VM を自分で使用する必要があります。
11. Windows を使用している学生は、ファイル共有へのパスが与えられたら、自分の資格情報で[エクスプローラー](../storage/files/storage-how-to-use-files-windows.md)を使用して、Azure Files 共有に接続できます。 または、学生は、前のスクリプトを実行して、ネットワーク ドライブに接続することもできます。 Linux を使用している学生の場合は、前のスクリプトを実行します。

## <a name="azure-netapp-files-with-nfs-volumes"></a>NFS ボリュームを含む Azure NetApp Files

[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) は、エンタープライズ クラスでハイパフォーマンスの従量制課金ファイル ストレージ サービスです。

- アクセス ポリシーはボリュームごとに設定できます。
- アクセス許可ポリシーは、ボリュームごとに IP ベースです。
- 学生が、他の学生がアクセスできない独自のボリュームを必要とする場合は、ラボの発行後にアクセス許可ポリシーを割り当てる必要があります。
- Azure Lab Services のコンテキストでは、Linux マシンのみがサポートされています。
- Azure NetApp Files 容量プールの仮想ネットワークは、ラボを作成する **前に**、ラボ アカウントの仮想ネットワークとピアリングされている必要があります。

Azure Lab Services で Azure NetApp Files 共有を使用するには、次の操作を行います。

1. Azure NetApp Files 容量プールと 1 つまたは複数の NFS ボリュームを作成するには、「[Azure NetApp Files を設定し、NFS ボリュームを作成する](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md)」を参照してください。 サービス レベルの詳細については、「[Azure NetApp Files のサービス レベル](../azure-netapp-files/azure-netapp-files-service-levels.md)」を参照してください。
2. Azure NetApp Files 容量プールの[仮想ネットワーク](how-to-connect-peer-virtual-network.md)をラボ アカウントにピアリングします。
3. [クラスルーム ラボを作成します](how-to-manage-classroom-labs.md)。
4. テンプレート VM で、NFS ファイル共有を使用するために必要なコンポーネントをインストールします。
    - Ubuntu:

        ```bash
        sudo apt update
        sudo apt install nfs-common
        ```

    - CentOS:

        ```bash
        sudo yum install nfs-utils
        ```

5. テンプレート VM で、次のスクリプトを `mount_fileshare.sh` として保存して、[Azure NetApp Files 共有をマウントします](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)。 `capacity_pool_ipaddress` 変数に容量プールのマウント ターゲット IP アドレスを割り当てます。 ボリュームのマウント命令を取得して、適切な値を見つけます。 このスクリプトには、Azure NetApp Files ボリュームのパス名が必要です。 ユーザーがスクリプトを実行できるようにするには、`chmod u+x mount_fileshare.sh` を実行します。

    ```bash
    #!/bin/bash
    
    if [ $# -eq 0 ];  then
        echo "Must provide volume name."
        exit 1
    fi
    
    volume_name=$1
    capacity_pool_ipaddress=0.0.0.0 # IP address of capacity pool
    
    # Do not use 'mnt' for mount directory.
    # Using ‘mnt’ might cause issues on student VMs.
    mount_directory="prm-mnt" 
    
    sudo mkdir -p /$mount_directory
    sudo mkdir /$mount_directory/$folder_name
    
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp $capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name
    sudo bash -c "echo ""$capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0"" >> /etc/fstab"
    ```

6. すべての学生が同じ Azure NetApp Files ボリュームへのアクセスを共有している場合は、発行の前にテンプレート マシンで `mount_fileshare.sh` スクリプトを実行できます。 学生がそれぞれ独自のボリュームを取得する場合は、スクリプトを保存して、後で学生が実行できるようにします。
7. テンプレート VM を[発行](how-to-create-manage-template.md#publish-the-template-vm)します。
8. ファイル共有の[ポリシーを構成](../azure-netapp-files/azure-netapp-files-configure-export-policy.md)します。 エクスポート ポリシーでは、単一の VM または複数の VM がボリュームにアクセスすることを許可できます。 読み取り専用または読み取り/書き込みアクセス権を付与できます。
9. 学生は VM を起動し、スクリプトを実行してファイル共有をマウントする必要があります。 スクリプトを実行する必要があるのは 1 回だけです。 コマンドは次のようになります: `./mount_fileshare.sh myvolumename`。

## <a name="next-steps"></a>次のステップ

以下の手順は、すべてのラボの設定で共通です。

- [テンプレートの作成と管理](how-to-create-manage-template.md)
- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)
