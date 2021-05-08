---
title: Lab Services で外部ファイル ストレージを使用する | Microsoft Docs
description: Lab Services で外部ファイル ストレージを使用するラボを設定する方法について説明します。
author: emaher
ms.topic: article
ms.date: 03/30/2021
ms.author: enewman
ms.openlocfilehash: 888e04db76567051f8c5eae7cf94c77e684cb146
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111099"
---
# <a name="using-external-file-storage-in-lab-services"></a>Lab Services で 外部ファイル ストレージを使用する

この記事では、Azure Lab Services を使用する際の外部ファイル ストレージでのいくつかのオプションについて説明します。  [Azure Files](https://azure.microsoft.com/services/storage/files/) では、[SMB 2.1 と SMB 3.0 を介してアクセスできる](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows)クラウド内のフル マネージド ファイル共有を提供します。  Azure Files 共有は、仮想ネットワーク内でパブリックまたはプライベートで接続できます。  また、ファイル共有への接続に学生の AD 資格情報を使用するように構成することもできます。  Linux マシン用の NFS ボリュームを含む Azure NetApp Files を使用することは、Azure Lab Services での外部ファイル ストレージのもう 1 つの選択肢です。  

## <a name="deciding-which-solution-to-use"></a>使用するソリューションを決める

各ソリューションには、さまざまな要件と機能があります。  次の表に、各ソリューションで考慮すべき重要な点を示します。  

|     解決策    |    知っておくべき重要なこと    |
| -------------- | ------------------------ |
| [パブリック エンドポイントを使用した Azure Files 共有](#azure-files-share) | <ul><li>すべてのユーザーに読み取り/書き込みアクセス権があります。</li><li>VNet ピアリングは必要ありません。</li><li>ラボ VM だけでなく、すべての VM からアクセスできます。</li><li>Linux を使用している場合、学生はストレージ アカウント キーにアクセスできます。</li></ul> |
| [プライベート エンドポイントを使用した Azure Files 共有](#azure-files-share) | <ul><li>すべてのユーザーに読み取り/書き込みアクセス権があります。</li><li>VNet ピアリングが必要です。</li><li>ストレージ　アカウントと同じネットワーク (またはピアリングされたネットワーク) 上の VM からのみアクセスできます。</li><li>Linux を使用している場合、学生はストレージ アカウント キーにアクセスできます。</li></ul> |
| [ID ベースの承認を使用した Azure Files](#azure-files-with-identity-base-authorization) | <ul><li>フォルダーまたはファイルに対して、読み取りまたは読み取り/書き込みアクセス許可を設定できます。</li><li>VNet ピアリングが必要です。</li><li>ストレージ アカウントは Active Directory に接続されている必要があります。</li><li>ラボ VM はドメインに参加している必要があります。</li><li>学生がファイル共有に接続するためにストレージ アカウント キーは使用されません。</li></ul> |
| [NFS ボリュームを含む NetApp Files](#netapp-files-with-nfs-volumes) | <ul><li>ボリュームに対して読み取りまたは読み取り/書き込みのいずれかのアクセス権を設定できます。</li><li>アクセス許可は、学生用 VM の IP アドレスを使用して設定されます。</li><li>VNet ピアリングが必要です。</li><li>NetApp Files サービスを使用するために登録が必要な場合があります。</li><li>Linux のみ。</li></ul>

外部ストレージの使用コストは、Azure Lab Services を使用するコストに含まれていません。  価格の詳細については、「[Azure Files の料金](https://azure.microsoft.com/pricing/details/storage/files/)」と「[Azure NetApp Files の価格](https://azure.microsoft.com/pricing/details/netapp/)」を参照してください。

## <a name="azure-files-share"></a>Azure Files 共有

Azure Files 共有には、パブリックまたはプライベート エンドポイントを使用してアクセスします。  Azure FIles 共有は、パスワードとしてストレージ アカウント キーを使用してマウントされます。  この方法を使用すると、すべてのユーザーにファイル共有への読み取り/書き込みアクセス権が与えられます。

Azure Files 共有にパブリック エンドポイントを使用する場合は、次の点に注意してください。

- ストレージ アカウントの仮想ネットワークは、ラボ アカウントとピアリングする必要はありません。  ファイル共有は、テンプレート VM が発行される前であればいつでも作成できます。
- ユーザーがストレージ アカウント キーを持っていれば、どのマシンからでもファイル共有にアクセスできます。  
- Linux の学生は、ストレージ アカウント キーを確認できます。  Azure Files 共有をマウントするための資格情報は、Linux VM 上の `{file-share-name}.cred` に格納されており、sudo で読み取ることができます。  学生には Azure Lab Service VM で sudo アクセスが既定で付与されるため、ストレージ アカウント キーを読み取ることができます。  ストレージ アカウントのエンドポイントがパブリックの場合、学生は学生用 VM の外部にあるファイル共有にアクセスできます。  クラスが終了したらストレージ アカウント キーをローテーションし、プライベート ファイル共有を使用することを検討してください。

Azure Files 共有にプライベート エンドポイントを使用する場合は、次の点に注意してください。

- アクセスは、プライベート ネットワークから送信されるトラフィックに限定され、パブリック インターネット経由ではアクセスできません。  プライベート仮想ネットワーク内の VM、プライベート仮想ネットワークにピアリングされたネットワーク内の VM、またはプライベート　ネットワーク用に VPN に接続されているマシンのみが、ファイル共有にアクセスできます。  
- Linux の学生は、ストレージ アカウント キーを確認できます。  Azure Files 共有をマウントするための資格情報は、Linux VM 上の `{file-share-name}.cred` に格納されており、sudo で読み取ることができます。  学生には Azure Lab Service VM で sudo アクセスが既定で付与されるため、ストレージ アカウント キーを読み取ることができます。  クラスが終了した後で、ストレージ アカウント キーをローテーションすることを検討してください。
- この方法では、ファイル共有仮想ネットワークがラボ アカウントとピアリングされている必要があります。  Azure Storage アカウントの仮想ネットワークは、ラボを作成する **前に**、ラボ アカウントの仮想ネットワークとピアリングされている必要があります。

> [!NOTE]
> 5 TB を超えるファイル共有は、[[ローカル冗長ストレージ (LRS) アカウント]](/azure/storage/files/storage-files-how-to-create-large-file-share#restrictions) でのみ使用できます。

Azure Files 共有に接続された VM を作成するには、次の手順に従ってください。

1. [Azure Storage アカウント](/azure/storage/files/storage-how-to-create-file-share)を作成します。 [接続方法] ページで、パブリック エンドポイントまたはプライベート エンドポイントを選択します。
2. 使用する場合は、仮想ネットワークからファイル共有にアクセスできるようにするために、[プライベート エンドポイント](/azure/private-link/create-private-endpoint-storage-portal)を作成します。  [プライベート DNS ゾーン](/azure/dns/private-dns-privatednszone)を作成するか、既存のものを使用します。 プライベート Azure DNS ゾーンでは、仮想ネットワーク内での名前解決が提供されます。
3. [Azure ファイル共有](/azure/storage/files/storage-how-to-create-file-share)を作成します。 ファイル共有には、ストレージ アカウントのパブリック ホスト名を指定してアクセスできます。
4. Azure ファイル共有をテンプレート VM にマウントします。
    - [[Windows]](/azure/storage/files/storage-how-to-use-files-windows)
    - [[Linux]](/azure/storage/files/storage-how-to-use-files-linux)。  学生用 VM でのマウントの問題を回避するには、「[Linux で Azure Files を使用する](#using-azure-files-with-linux)」を参照してください。
5. テンプレート VM を[発行](how-to-create-manage-template.md#publish-the-template-vm)します。

> [!IMPORTANT]
> ポート 445 を介した発信 SMB 接続がファイアウォールでブロックされていないことを確認します。 既定では、SMB は Azure VM に対して許可されます。

### <a name="using-azure-files-with-linux"></a>Linux で Azure Files を使用する

"_既定の手順_" を使用して Azure ファイル共有をマウントした場合、テンプレートが発行されると、学生用 VM ではファイル共有が消えたように見えます。  この問題に対処する変更後のスクリプトを以下に示します。  

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

Azure ファイル共有を `/mnt` ディレクトリにマウントするテンプレート VM が既に発行されている場合、学生は次のいずれかの操作を実行できます。

- `/mnt` をマウントする命令を `/etc/fstab` ファイルの先頭に移動する。  
- `/mnt/{file-share-name}` をマウントする命令を `/prm-mnt/{file-share-name}` などの別のディレクトリに変更する。

学生は `mount -a` を実行して、ディレクトリを再マウントする必要があります。

Linux でのファイル共有の使用に関する一般的な情報については、「[Linux で Azure Files を使用する](/azure/storage/files/storage-how-to-use-files-linux)」を参照してください。

## <a name="azure-files-with-identity-base-authorization"></a>ID ベースの承認を使用した Azure Files

次の場合、AD 認証を使用して Azure Files 共有にアクセスすることもできます。

1. 学生用 VM がドメインに参加している。
2. AD 認証が、ファイル共有をホストする [Azure Storage アカウントで有効](/azure/storage/files/storage-files-active-directory-overview)になっている。  

ネットワーク ドライブは、ストレージ アカウントのキーではなく、ユーザーの ID を使用して仮想マシンにマウントされます。  ストレージ アカウントへのアクセスには、パブリックまたはプライベート エンドポイントを使用できます。

この方法のいくつかの重要な点について確認してみましょう。

- アクセス許可は、ディレクトリまたはファイル レベルで設定できます。
- ファイル共有への認証には、現在のユーザー資格情報が使用されます。

Azure Files 共有にパブリック エンドポイントを使用する場合は、次の点に注意してください。

- ストレージ アカウントの仮想ネットワークは、ラボ アカウントにピアリングされている必要はありません。  ファイル共有は、テンプレート VM が発行される前であればいつでも作成できます。

Azure Files 共有にプライベート エンドポイントを使用する場合は、次の点に注意してください。

- アクセスは、プライベート ネットワークから送信されるトラフィックに限定され、パブリック インターネット経由ではアクセスできません。  プライベート仮想ネットワーク内の VM、プライベート仮想ネットワークにピアリングされたネットワーク内の VM、またはプライベート　ネットワーク用に VPN に接続されているマシンのみが、ファイル共有にアクセスできます。  
- この方法では、ファイル共有仮想ネットワークがラボ アカウントとピアリングされている必要があります。  Azure Storage アカウントの仮想ネットワークは、ラボを作成する **前に**、ラボ アカウントの仮想ネットワークとピアリングされている必要があります。

次の手順に従って、AD 認証が有効になった Azure Files 共有を作成し、ラボ VM をドメインに参加させます。

1. [Azure Storage アカウント](/azure/storage/files/storage-how-to-create-file-share)を作成します。
2. 使用する場合は、仮想ネットワークからファイル共有にアクセスできるようにするために、[プライベート エンドポイント](/azure/private-link/create-private-endpoint-storage-portal)を作成します。  [プライベート DNS ゾーン](/azure/dns/private-dns-privatednszone)を作成するか、既存のものを使用します。 プライベート Azure DNS ゾーンでは、仮想ネットワーク内での名前解決が提供されます。
3. [Azure ファイル共有](/azure/storage/files/storage-how-to-create-file-share)を作成します。
4. ID ベースの承認を有効にするには、次の手順に従ってください。  Azure AD に同期されるオンプレミスの AD を使用する場合は、「[SMB を使用した Azure ファイル共有へのオンプレミスの Active Directory Domain Services 認証](/azure/storage/files/storage-files-identity-auth-active-directory-enable)」の手順に従ってください。  Azure AD のみを使用する場合は、「[Azure Files に対する Azure Active Directory Domain Services 認証を有効にする](/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable)」の手順に従います。
    >[!IMPORTANT]
    >AD を管理するチームに相談して、手順に記載されているすべての前提条件が満たされていることを確認してください。
5. Azure で SMB 共有のアクセス許可ロールを割り当てます。  各ロールに付与されるアクセス許可の詳細については、[共有レベルのアクセス許可](/azure/storage/files/storage-files-identity-ad-ds-assign-permissions)に関するページを参照してください。
    1. "ストレージ ファイル データの SMB 共有の管理者特権共同作成者" ロールは、ファイル共有のコンテンツのアクセス許可を設定するユーザーまたはグループに割り当てる必要があります。
    2. "ストレージ ファイル データの SMB 共有の共同作成者" ロールは、ファイル共有のファイルを追加または編集する必要がある学生に割り当てる必要があります。
    3. "ストレージ ファイル データの SMB 共有の閲覧者" ロールは、ファイル共有のファイルの読み取りだけが必要な学生に割り当てる必要があります。
6. ファイル共有に対してディレクトリまたはファイル レベルのアクセス許可を設定します。  アクセス許可は、ファイル共有へのネットワーク アクセスが可能なドメインに参加しているマシンから設定する必要があります。  **ディレクトリまたはファイル レベルのアクセス許可を変更するには、AAD の資格情報ではなく、ストレージ キーを使用してファイル共有をマウントします。**  [Set-Acl](/powershell/module/microsoft.powershell.security/set-acl) PowerShell コマンドや [icacls](/windows-server/administration/windows-commands/icacls) は、アクセス許可を割り当てるときに使用することが推奨されているツールです。
7. ストレージ アカウントの[仮想ネットワーク](how-to-connect-peer-virtual-network.md)をラボ アカウントにピアリングします。
8. [クラスルーム ラボを作成します](how-to-manage-classroom-labs.md)。
9. 学生がネットワーク ドライブに接続するために実行できるスクリプトをテンプレート VM に保存します。  サンプル スクリプトを取得するには、次のようにします。
    1. Azure portal でストレージ アカウントを開きます。
    1. メニューの **[ファイル サービス]** で **[ファイル共有]** を選択します。
    1. 接続先の共有を見つけ、右端にある省略記号ボタンを選択し、 **[接続]** を選択します。
    1. Windows、Linux、および macOS 用の命令が含まれた **[接続]** ポップアップが開きます。  Windows を使用している場合は、 **[認証方法]** を **[Active Directory]** に設定します。
    1. 例に含まれているコードをコピーし、Windows の場合は `.ps1` ファイルに、Linux の場合は `.sh` ファイルに保存します。
10. テンプレート マシンで、スクリプトをダウンロードして実行し、[学生のマシンをドメインに参加](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Scripts/ActiveDirectoryJoin/README.md#usage)させます。  `Join-AzLabADTemplate` スクリプトを実行すると、自動的に[テンプレート VM が発行](how-to-create-manage-template.md#publish-the-template-vm)されます。  
    > [!NOTE]
    > テンプレート マシンは、ドメインに参加しません。 インストラクターは、共有上のファイルを表示するために、発行された学生用 VM を自分で割り当てる必要があります。
11. Windows を使用している学生は、ファイル共有へのパスが与えられたら、自分の資格情報で[エクスプローラー](/azure/storage/files/storage-how-to-use-files-windows)を使用して、Azure Files 共有に接続できます。  または、学生は、上の手順で作成したスクリプトを実行して、ネットワーク ドライブに接続することもできます。  Linux を使用している学生の場合は、上で作成したスクリプトを実行します。

## <a name="netapp-files-with-nfs-volumes"></a>NFS ボリュームを含む NetApp Files

[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) は、エンタープライズ クラスでハイパフォーマンスの従量制課金ファイル ストレージ サービスです。

- アクセス ポリシーはボリュームごとに設定できます。
- アクセス許可ポリシーは、ボリュームごとに IP ベースです。
- 学生が、他の学生がアクセスできない独自のボリュームを必要とする場合は、ラボの発行後にアクセス許可ポリシーを割り当てる必要があります。
- Azure Lab Services のコンテキストでは、Linux マシンのみがサポートされています。
- Azure NetApp Files 容量プールの仮想ネットワークは、ラボを作成する **前に**、ラボ アカウントの仮想ネットワークとピアリングされている必要があります。

Azure Lab Services で Azure NetApp Files 共有を使用するには、下の手順に従います。

1. 必要に応じて、[Azure NetApp Files](https://aka.ms/azurenetappfiles) にオンボードします。
2. NetApp Files 容量プールと NFS ボリュームを作成するには、[Azure NetApp Files と NFS ボリュームの設定](/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes)に関するページを参照してください。  サービス レベルの詳細については、「[Azure NetApp Files のサービス レベル](/azure/azure-netapp-files/azure-netapp-files-service-levels)」を参照してください。
3. NetApp Files 容量プールの[仮想ネットワーク](how-to-connect-peer-virtual-network.md)をラボ アカウントにピアリングします。
4. [クラスルーム ラボを作成します](how-to-manage-classroom-labs.md)。
5. テンプレート VM で、NFS ファイル共有を使用するために必要なコンポーネントをインストールします。
    - Ubuntu:

        ```bash
        sudo apt update
        sudo apt install nfs-common
        ```

    - CentOS

        ```bash
        sudo yum install nfs-utils
        ```

6. テンプレート VM で、下のスクリプトを `mount_fileshare.sh` として保存して、[NetApp Files 共有をマウントします](/azure/azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines)。  `capacity_pool_ipaddress` 変数に容量プールのマウント ターゲット IP アドレスを割り当てます。  ボリュームのマウント命令を取得して、適切な値を見つけます。  このスクリプトには、NetApp Files ボリュームのパスおよび名前が必要です。  `chmod u+x mount_fileshare.sh` を忘れずに実行して、ユーザーがスクリプトを実行できるようにします。

    ```bash
    #!/bin/bash
    
    if [ $# -eq 0 ];  then
        echo "Must provide volume name."
        exit 1
    fi
    
    volume_name=$1
    capacity_pool_ipaddress=0.0.0.0 # IP address of capacity pool
    
    # Do not use 'mnt' for mount directory.
    # Using ‘mnt’ may cause issues on student VMs.
    mount_directory="prm-mnt" 
    
    sudo mkdir -p /$mount_directory
    sudo mkdir /$mount_directory/$folder_name
    
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp $capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name
    sudo bash -c "echo ""$capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0"" >> /etc/fstab"
    ```

7. すべての学生が同じ NetApp Files ボリュームへのアクセスを共有している場合は、発行の前にテンプレート マシンで `mount_fileshare.sh` スクリプトを実行できます。  学生がそれぞれ独自のボリュームを取得する場合は、スクリプトを保存して、後で学生が実行できるようにします。
8. テンプレート VM を[発行](how-to-create-manage-template.md#publish-the-template-vm)します。
9. ファイル共有の[ポリシーを構成](/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)します。  エクスポート ポリシーでは、単一の VM または複数の VM がボリュームにアクセスすることを許可できます。  読み取り専用または読み取り/書き込みアクセス権を付与できます。
10. 学生は VM を起動し、スクリプトを実行してファイル共有をマウントする必要があります。  スクリプトを実行する必要があるのは 1 回だけです。  コマンドは `./mount_fileshare.sh myvolumename` のようになります。

## <a name="next-steps"></a>次のステップ

次の手順は、すべてのラボの設定で共通です。

- [テンプレートの作成と管理](how-to-create-manage-template.md)
- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)