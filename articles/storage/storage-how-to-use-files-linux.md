<properties
	pageTitle="Linux で Azure File ストレージを使用する方法 | Microsoft Azure"
        description="クラウドにファイル共有を作成して、Azure VM または Linux で実行されているオンプレミスのアプリケーションからマウントします。"
        services="storage"
        documentationCenter="na"
        authors="jutang"
        manager="jahogg"
        editor="" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="na"
      ms.topic="article"
      ms.date="09/28/2015"
      ms.author="jutang;tamram" />


# Linux で Azure File ストレージを使用する方法 

## 概要

Azure File ストレージは、標準的な SMB プロトコルを使用してクラウドでのファイル共有を提供します。File ストレージは現在一般に提供されており、SMB 3.0 と SMB 2.1 の両方をサポートしています。

Azure のファイル共有は、Azure プレビュー ポータル、Azure Storage の PowerShell コマンドレット、Azure Storage のクライアント ライブラリ、または Azure Storage の REST API を使用して作成することができます。また、ファイル共有は SMB 共有であるため、それらには標準の使い慣れたファイル システム API を使用してアクセスできます。

Azure で実行されているアプリケーションでは、Azure の仮想マシンのファイル共有を簡単にマウントできます。また、File ストレージの最新のリリースでは、SMB 3.0 をサポートしているオンプレミス アプリケーションからファイル共有をマウントできます。

Linux の SMB クライアントでは暗号化はまだサポートされていないため、Linux からファイル共有をマウントするには、クライアントがファイル共有と同じ Azure リージョンに存在する必要があります。ただし、Linux での暗号化のサポートは、SMB 機能を担当している Linux 開発者によって実装される予定です。今後の暗号化をサポートする Linux ディストリビューションによって、任意の場所から Azure File 共有をマウントできるようになります。

## 使用する Linux ディストリビューション ##

Azure で Linux 仮想マシンを作成するときに、Azure イメージ ギャラリーの SMB 2.1 以降をサポートする Linux イメージを指定できます。推奨される Linux イメージの一覧を次に示します。

- Ubuntu Server 14.04	
- Ubuntu Server 15.04	
- CentOS 7.1	
- Open SUSE 13.2	
- SUSE Linux Enterprise Server 12
- SUSE Linux Enterprise Server 12 (Premium Image)

## ファイル共有をマウントする ##

使用するディストリビューションに組み込みのクライアントがない場合に Linux を実行する仮想マシンからファイル共有をマウントするには、SMB/CIFS クライアントをインストールする必要があります。次に示すのは、1 つの選択肢である cifs-utils をインストールするための Ubuntu のコマンドです。

    sudo apt-get install cifs-utils

次に、マウント ポイント (mkdir mymountpoint) を作成した後、次のような mount コマンドを発行する必要があります。

     sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename ./mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

共有は、/etc/fstab に設定を追加することでマウントすることもできます。

ここでの 0777 は、すべてのユーザーに実行/読み取り/書き込みアクセス許可を与えるディレクトリ/ファイルのアクセス許可を表します。Linux ファイルのアクセス許可に関するドキュメントに従って、他のファイル アクセス許可コードに置き換えることができます。
 
再起動後にマウントされているファイル共有を保持するために、次のような設定を /etc/fstab に追加できます。

    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username= myaccountname,password= StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

具体的な例を次に示します。

Azure マーケットプレースで入手できる Linux イメージ Ubuntu Server 15.04 を使用して Azure VM を作成した場合は、次のようにファイルをマウントできます。

    azureuser@azureconubuntu:~$ sudo apt-get install apt-file
    azureuser@azureconubuntu:~$ sudo mkdir /mnt/mountpoint
    azureuser@azureconubuntu:~$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@azureconubuntu:~$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

CentOS 7.1 を使用する場合は、次に示すようにファイルをマウントできます。

    [azureuser@AzureconCent ~]$ sudo yum install samba-client samba-common cifs-utils
    [azureuser@AzureconCent ~]$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    [azureuser@AzureconCent ~]$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

Open SUSE 13.2 を使用する場合は、次に示すようにファイルをマウントできます。

    azureuser@AzureconSuse:~> sudo zypper install samba*  
    azureuser@AzureconSuse:~> sudo mkdir /mnt/mountpoint
    azureuser@AzureconSuse:~> sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@AzureconSuse:~> df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint


## 次のステップ

Azure File ストレージの詳細については、次のリンクを参照してください。

### 概念についての記事

- [Windows で Azure File ストレージを使用する方法](storage-dotnet-how-to-use-files.md)

### File ストレージ用のツールのサポート

- [Microsoft Azure Storage で AzCopy を使用する方法](storage-use-azcopy.md)
- [Azure Storage での Azure CLI の使用](storage-azure-cli.md#create-and-manage-file-shares)

### リファレンス

- [File サービスの REST API リファレンス](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### ブログ記事

- [Azure File ストレージの一般提供開始](http://go.microsoft.com/fwlink/?LinkID=626728&clcid=0x409)
- [Azure File ストレージの詳細情報](http://go.microsoft.com/fwlink/?LinkID=626729&clcid=0x409) 
- [Microsoft Azure File サービスの概要](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [Microsoft Azure Files への接続の維持](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

<!---HONumber=Oct15_HO1-->