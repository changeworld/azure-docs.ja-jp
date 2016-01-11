<properties
	pageTitle="Linux で Azure Files を使用する方法 | Microsoft Azure"
        description="この詳しい手順を示したチュートリアルでは、クラウドに Azure ファイル共有を作成します。Linux を実行している Azure の仮想マシン (VM) または SMB 3.0 をサポートするオンプレミスのアプリケーションから、ファイル共有のコンテンツを管理し、ファイル共有をマウントします。"
        services="storage"
        documentationCenter="na"
        authors="jasontang501"
        manager="jahogg"
        editor="" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="na"
      ms.topic="article"
      ms.date="12/17/2015"
      ms.author="jutang;tamram" />


# Linux で Azure File Storage を使用する方法 

## 概要

Azure File Storage は、標準の SMB プロトコルを使用したクラウドでのファイル共有を提供します。Azure Files を使用すると、ファイル サーバーを利用しているエンタープライズ アプリケーションを Azure に移行できます。Azure で実行されているアプリケーションでは、Linux を実行している Azure の仮想マシンからファイル共有を簡単にマウントできます。また、File ストレージの最新のリリースでは、SMB 3.0 をサポートしているオンプレミス アプリケーションからファイル共有をマウントできます。

Azure のファイル共有は、 [Azure ポータル](portal.azure.com)、Azure Storage の PowerShell コマンドレット、Azure Storage のクライアント ライブラリ、または Azure Storage の REST API を使用して作成することができます。また、ファイル共有は SMB 共有であるため、それらには標準のファイル システム API を使用してアクセスできます。

File Storage は、BLOB、Table、および Queue Storage と同じテクノロジ上に構築されているため、可用性、持続性、スケーラビリティ、および Azure Storage プラットフォームに組み込まれている geo 冗長性を利用できます。File Storage のパフォーマンスのターゲットと制限事項の詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](storage-scalability-targets.md)」を参照してください。

File Storage は現在一般に提供されており、SMB 2.1 と SMB 3.0 の両方をサポートしています。File Storage の詳細については、「[ファイル サービスの REST API](https://msdn.microsoft.com/library/azure/dn167006.aspx)」を参照してください。

>[AZURE.NOTE]Linux の SMB クライアントでは暗号化はまだサポートされていないため、Linux からファイル共有をマウントするには、クライアントがファイル共有と同じ Azure リージョンに存在する必要があります。ただし、Linux での暗号化のサポートは、SMB 機能を担当している Linux 開発者によって実装される予定です。今後の暗号化をサポートする Linux ディストリビューションによって、任意の場所から Azure File 共有をマウントできるようになります。

## ビデオ: Linux で Azure File ストレージを使用する方法

このビデオでは、Linux で Azure のファイル共有を作成して使用する方法について説明しています。

> [AZURE.VIDEO azure-file-storage-with-linux]

## 使用する Linux ディストリビューションの選択 ##

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

たとえば、Linux image Ubuntu Server 15.04 (Azure イメージ ギャラリーから入手可能) を使用して Azure VM を作成した場合、次のようにファイルをマウントできます。

    azureuser@azureconubuntu:~$ sudo apt-get install cifs-utils
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

## ファイル共有の管理 ##

[Azure ポータル](portal.azure.com)では、Azure File Storage を管理するためのユーザー インターフェイスを使用できます。Web ブラウザーから、次の操作を実行できます。

- ファイル共有からのファイルのアップロードおよびダウンロード
- 各ファイル共有の実際の使用状況の監視
- ファイル共有のサイズ クォータの調整
- Windows クライアントからファイル共有をマウントするために使用する `net use` コマンドのコピー 

また、Linux から Azure のクロスプラットフォームのコマンドライン インターフェイス (Azure CLI) を使用してファイル共有を管理することもできます。Azure CLI には、File Storage など、Azure Storage を処理できるオープン ソースのクロスプラットフォーム コマンド セットが用意されています。豊富なデータ アクセス機能だけでなく、Azure ポータルにあるものと同じ機能の多くを使用できます。たとえば、「[Azure Storage での Azure CLI の使用](storage-azure-cli.md)」をご覧ください。

## File ストレージを使用した開発 ##

開発者は、[Azure Storage Client Library for Java](https://github.com/azure/azure-storage-java) を使用して、File ストレージでアプリケーションを作成できます。コード サンプルについては、「[Java からファイル ストレージを使用する方法](storage-java-how-to-use-file-storage.md)」をご覧ください。

[Azure Storage Client Library for Node.js](https://github.com/Azure/azure-storage-node) を使用して File ストレージに対して開発することもできます。

## フィードバックと詳細情報 ##

Linux ユーザーからのご意見をお待ちしています。

Azure File storage for Linux ユーザーのグループによって、File ストレージを Linux で評価および使用するときにフィードバックを共有できるフォーラムが提供されています。[Azure File Storage Linux ユーザー](mailto:azurefileslinuxusers@microsoft.com)にメールを送信して、ユーザーのグループに参加してください。

## 次のステップ

Azure File ストレージの詳細については、次のリンクを参照してください。

### 概念に関する記事とビデオ

- [Azure File Storage: Windows および Linux 用の円滑なクラウド SMB ファイル システム](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
- [Windows で Azure File Storage を使用する方法](storage-dotnet-how-to-use-files.md)
- [AzCopy コマンド ライン ユーティリティを使ったデータの転送](storage-use-azcopy)

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

<!---HONumber=AcomDC_1223_2015-->