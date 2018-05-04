---
title: Windows で Azure ファイル共有をマウントして共有にアクセスする | Microsoft Docs
description: Windows で Azure ファイル共有をマウントして共有にアクセスします。
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/11/2018
ms.author: renash
ms.openlocfilehash: e283619c7e634a1fbba5940e5c8545b0ee4de3d1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="mount-an-azure-file-share-and-access-the-share-in-windows"></a>Windows で Azure ファイル共有をマウントして共有にアクセスする
[Azure Files](storage-files-introduction.md) は、Microsoft の使いやすいクラウド ファイル システムです。 Windows と Windows Server で、Azure ファイル共有をマウントできます。 この記事では、Windows 上で Azure ファイル共有をマウントするための 3 つの異なる方法を示します。エクスプローラー UI を使用する方法、PowerShell を使用する方法、コマンド プロンプトを使用する方法です。 

Azure ファイル共有がホストされている Azure リージョンの外の Azure ファイル共有 (オンプレミスの共有、他の Azure リージョン内の共有など) をマウントするために、OS は SMB 3.0 をサポートする必要があります。 

Azure ファイル共有は、Azure VM とオンプレミスのどちらかで実行されている Windows インストール済み環境でマウントできます。 次の表は、ファイル共有のマウントがサポートされる環境を OS バージョンごとに示したものです。

| Windows のバージョン        | SMB のバージョン | Azure VM でマウント可能 | オンプレミスでマウント可能 |
|------------------------|-------------|-----------------------|----------------------|
| Windows Server 半期チャネル<sup>1</sup> | SMB 3.0 | [はい] | [はい] |
| Windows 10<sup>2</sup>  | SMB 3.0 | [はい] | [はい] |
| Windows Server 2016    | SMB 3.0     | [はい]                   | [はい]                  |
| Windows 8.1            | SMB 3.0     | [はい]                   | [はい]                  |
| Windows Server 2012 R2 | SMB 3.0     | [はい]                   | [はい]                  |
| Windows Server 2012    | SMB 3.0     | [はい]                   | [はい]                  |
| Windows 7              | SMB 2.1     | [はい]                   | いいえ                    |
| Windows Server 2008 R2 | SMB 2.1     | [はい]                   | いいえ                    |

<sup>1</sup>Windows Server バージョン 1709。  
<sup>2</sup>Windows 10 バージョン 1507、1607、1703、および 1709。

> [!Note]  
> 常に、各 Windows バージョンの最新のサポート技術情報を参照することをお勧めします。

## <a name="aprerequisites-for-mounting-azure-file-share-with-windows"></a></a>Windows で Azure ファイル共有をマウントするための前提条件 
* **ストレージ アカウント名**: Azure ファイル共有をマウントするには、ストレージ アカウントの名前が必要になります。

* **ストレージ アカウント キー**: Azure File 共有をマウントするには、プライマリ (またはセカンダリ) ストレージ キーが必要です。 現時点では、SAS キーは、マウントではサポートされていません。

* **ポート 445 が開いていること**: Azure Files は SMB プロトコルを使用します。 SMB は、TCP ポート 445 経由で通信します。ファイアウォールがクライアント マシンの TCP ポート 445 をブロックしていないことを確認してください。 Portqry を使用して、TCP ポート 445 が開いているかどうかを確認できます。 TCP ポート 445 がフィルター処理済みとして表示される場合、TCP ポートはブロックされています。 クエリの使用例を次に示します。

    `g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

    TCP ポート 445 がネットワーク パスに基づく規則によってブロックされている場合、次の出力が表示されます。

    `TCP port 445 (Microsoft-ds service): FILTERED`

    Portqry の使用方法の詳細については、「[Portqry.exe コマンド ライン ユーティリティの説明](https://support.microsoft.com/help/310099)」を参照してください。


## <a name="persisting-connections-across-reboots"></a>再起動後も維持されるように接続を永続化する
### <a name="cmdkey"></a>CmdKey
永続接続を確立する最も簡単な方法は、"CmdKey" コマンド ライン ユーティリティを使ってストレージ アカウントの資格情報を Windows に保存することです。 ストレージ アカウントの資格情報を VM に永続化するコマンド ラインの例を次に示します。
```
C:\>cmdkey /add:<yourstorageaccountname>.file.core.windows.net /user:<domainname>\<yourstorageaccountname> /pass:<YourStorageAccountKeyWhichEndsIn==>
```
> [!Note]
> ここでの <domainname> は "AZURE" です。

格納されている資格情報を CmdKey で一覧表示することもできます。

```
C:\>cmdkey /list
```
出力結果は次のようになります。

```
Currently stored credentials:

Target: Domain:target=<yourstorageaccountname>.file.core.windows.net
Type: Domain Password
User: AZURE\<yourstorageaccountname>
```
いったん資格情報が永続化された後は、共有への接続時にそれらを指定する必要はありません。 資格情報を一切指定せずに接続することができます。

## <a name="mount-the-azure-file-share-with-file-explorer"></a>エクスプローラーを使用した Azure ファイル共有のマウント
> [!Note]  
> 以下の手順は Windows 10 で操作する場合であり、それ以前のリリースでは少し異なることがある点に注意してください。 

1. **エクスプローラーを開きます**。[スタート] メニューから開くことも、Windows + E キーを押すショートカットで開くこともできます。

2. **ウィンドウの左側の [PC] 項目に移動します。これで、リボンで使用できるメニューが変更されます。[コンピューター] メニューの [ネットワーク ドライブの割り当て] を選択します**。
    
    ![[ネットワーク ドライブの割り当て] ドロップダウン メニューのスクリーンショット](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

3. **Azure Portal の [接続] ウィンドウで UNC パスをコピーします。** 

    ![Azure Files の [接続] ウィンドウの UNC パス](./media/storage-how-to-use-files-windows/portal_netuse_connect.png)

4. **ドライブ文字を選択し、UNC パスを入力します。** 
    
    ![[ネットワーク ドライブの割り当て] ダイアログのスクリーンショット](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

5. **ユーザー名として先頭に `Azure\` を付けたストレージ アカウント名を使用し、パスワードとしてストレージ アカウント キーを使用します。**
    
    ![ネットワーク資格情報ダイアログのスクリーンショット](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

6. **Azure ファイル共有を自由に使用します**。
    
    ![Azure ファイル共有がマウントされました](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

7. **Azure ファイル共有をマウント解除 (または切断) することになったら、エクスプローラーの [ネットワークの場所] の下にある共有のエントリを右クリックし、[切断] を選択します**。

## <a name="mount-the-azure-file-share-with-powershell"></a>PowerShell を使用した Azure ファイル共有のマウント
1. **次のコマンドを使用して、Azure ファイル共有をマウントします**。`<storage-account-name>`、`<share-name>`、`<storage-account-key>`、`<desired-drive-letter>` を適切な情報に置き換えてください。

    ```PowerShell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
    New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\<share-name>" -Credential $credential
    ```

2. **Azure ファイル共有を自由に使用します**。

3. **作業が完了したら、次のコマンドを使用して Azure ファイル共有をマウント解除します**。

    ```PowerShell
    Remove-PSDrive -Name <desired-drive-letter>
    ```

> [!Note]  
> `New-PSDrive` で `-Persist` パラメーターを使用すると、マウント中は Azure ファイル共有が他の OS でも表示されるようにすることができます。

## <a name="mount-the-azure-file-share-with-command-prompt"></a>コマンド プロンプトを使用した Azure ファイル共有のマウント
1. **次のコマンドを使用して、Azure ファイル共有をマウントします**。`<storage-account-name>`、`<share-name>`、`<storage-account-key>`、`<desired-drive-letter>` を適切な情報に置き換えてください。

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. **Azure ファイル共有を自由に使用します**。

3. **作業が完了したら、次のコマンドを使用して Azure ファイル共有をマウント解除します**。

    ```
    net use <desired-drive-letter>: /delete
    ```

> [!Note]  
> Windows で資格情報を保持することによって、再起動時に自動的に再接続されるように Azure ファイル共有を構成することができます。 次のコマンドを実行すると、資格情報が保持されます。
>   ```
>   cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>
>   ```

## <a name="next-steps"></a>次の手順
Azure Files の詳細については、次のリンクをご覧ください。

* [FAQ](../storage-files-faq.md)
* [Windows 上でのトラブルシューティング](storage-troubleshoot-windows-file-connection-problems.md)      

### <a name="conceptual-articles-and-videos"></a>概念に関する記事とビデオ
* [Azure Files: Windows および Linux 用の円滑なクラウド SMB ファイル システム](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Linux で Azure Files を使用する方法](../storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-azure-files"></a>Azure Files 用のツールのサポート
* [Microsoft Azure Storage で AzCopy を使用する方法](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Azure Storage での Azure CLI の使用](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [Azure Files に関する問題のトラブルシューティング - Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Azure Files に関する問題のトラブルシューティング - Linux](storage-troubleshoot-linux-file-connection-problems.md)

### <a name="blog-posts"></a>ブログ記事
* [Azure Files が一般公開されました](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Azure Files の内部](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Microsoft Azure File サービスの概要](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Azure Files へのデータの移行](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>リファレンス
* [.NET 用ストレージ クライアント ライブラリ リファレンス](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [File サービスの REST API リファレンス](http://msdn.microsoft.com/library/azure/dn167006.aspx)
