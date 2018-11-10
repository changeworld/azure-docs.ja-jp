---
title: Azure Data Box Gateway でデータを転送する | Microsoft Docs
description: Data Box Gateway デバイスで共有を追加して接続する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: fa31397e0ecffbd245557a824bdd770724bbc91c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51249882"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway-preview"></a>チュートリアル: Azure Data Box Gateway でデータを転送する (プレビュー)


## <a name="introduction"></a>はじめに

この記事では、Data Box Gateway で共有を追加して接続する方法について説明します。 共有が追加されると、Data Box Gateway デバイスから Azure にデータを転送できます。

この手順の所要時間は約 10 分です。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 共有の追加
> * 共有への接続

> [!IMPORTANT]
> - Data Box Gateway はプレビュー段階にあります。 このソリューションを注文して展開する前に、[Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)を確認してください。 
 
## <a name="prerequisites"></a>前提条件

共有を Data Box Gateway に追加する前に、以下の点を確認してください。

* [Hyper-V での Data Box Gateway のプロビジョニング](data-box-gateway-deploy-provision-hyperv.md)または [VMware での Data Box Gateway のプロビジョニング](data-box-gateway-deploy-provision-vmware.md)に関するページの説明に従って仮想デバイスをプロビジョニングし、その仮想デバイスに接続されていること。 

    仮想デバイスは、[Azure Data Box Gateway の接続とアクティブ化](data-box-gateway-deploy-connect-setup-activate.md)に関するページの手順に従ってアクティブ化し、共有を作成してデータを転送する準備を完了していること。


## <a name="add-a-share"></a>共有の追加

共有を作成するには、[Azure Portal](https://portal.azure.com/) で次の手順を実行します。

1. Azure Portal に戻ります。 **[すべてのリソース]** に移動し、Data Box Gateway リソースを探します。
    
2. フィルター処理されたリソースの一覧で、Data Box Gateway リソースを選択して、**[概要]** に移動します。 デバイスのコマンド バーで **[+ 共有の追加]** をクリックします。
   
   ![共有の追加](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. **[共有の追加]** で共有設定を指定します。 共有の一意の名前を指定します。 

   共有名には、数字、英小文字、ハイフンのみを使用できます。 共有名の長さは 3 から 63 文字で、先頭は英字または数字にする必要があります。 各ハイフンの前後にはハイフン以外の文字を指定する必要があります。
    
5. 共有の **[種類]** を選択します。 種類には SMB (既定値) または NFS を選択できます。 SMB は Windows クライアントの場合に標準です。また、Linux クライアントの場合は NFS が使用されます。 SMB 共有と NFS 共有のどちらを選択するかに応じて、表示されるオプションの一部が変わります。 

6. 共有を配置するストレージ アカウントを指定する必要があります。 コンテナーがまだ存在しない場合は、ストレージ アカウントに共有名を持つコンテナーが作成されます。 コンテナーが既に存在する場合は、既存のコンテナーが使用されます。 
    
7. ブロック BLOB、ページ BLOB、またはファイルから **[ストレージ サービス]** を選択します。 選択されるサービスの種類は、Azure に存在するデータの形式によって変わります。 たとえば、このインスタンスでは、BLOB ブロックとして Azure にデータを配置するため、ブロック BLOB を選択します。 ページ BLOB を選択する場合は、データに 512 バイトが割り当てられていることを確認する必要があります。 VHDX には常に 512 バイトが割り当てられています。
   
8. この手順は、SMB 共有と NFS 共有のどちらを作成するかに応じて変わります。 
     
    - **SMB 共有を作成する場合**: [すべての権限を持つローカル ユーザー] フィールドで、**[新規作成]** または **[既存のものを使用]** から選択します。 新しいローカル ユーザーを作成する場合は、**ユーザー名**、**パスワード**、および**パスワードの確認**を入力します。 これで、ローカル ユーザーにアクセス許可が割り当てられます。 ここで割り当てたアクセス許可は、ファイル エクスプローラーを使用して変更できます。
    
        ![SMB 共有を追加する](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        この共有データに対して **[読み取り操作のみを許可する]** をオンにすると、読み取り専用ユーザーを指定するオプションが表示されます。
        
    - **NFS 共有を作成する場合**: 共有へのアクセスが許可されたクライアントの IP アドレスを指定する必要があります。

        ![NFS 共有を追加する](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. **[作成]** をクリックして共有を作成します。 
    
    共有の作成が進行中であることが通知されます。 指定した設定で共有を作成すると、**[共有]** ブレードは更新され、新しい共有が反映されます。 
    
    ![更新された共有の一覧](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>共有に接続する

共有に接続するには、Data Box Gateway に接続された Windows Server クライアントで次の手順を実行します。


1. コマンド ウィンドウを開きます。 コマンド プロンプトに、次のコマンドを入力します。

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    メッセージに従って共有のパスワードを入力します。 このコマンドのサンプル出力を次に示します。

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2817 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60' • 
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. Windows キーを押しながら R キーを押します。**[ファイル名指定して実行]** ウィンドウで、「`\\<device IP address>`」と入力します。 Click **OK**. これによりエクスプローラーが開きます。 フォルダーとして作成した共有が表示されます。 コンテンツを表示するには、共有 (フォルダー) を選択してダブルクリックします。
 
    ![SMB 共有に接続する](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    データが生成され、デバイスからクラウドにデータがプッシュされると、これらの共有にデータが書き込まれます。

### <a name="connect-to-an-nfs-share"></a>NFS 共有に接続する

Data Box Edge に接続されている Linux クライアントで次の手順を実行します。

1. クライアントに NFSv4 クライアントがインストールされていることを確認します。 NFS クライアントをインストールするには、次のコマンドを使用します。

   `sudo apt-get install nfs-common`

    詳細については、[NFSv4 クライアントのインストール](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client)に関するページを参照してください。

2. NFS クライアントをインストールした後、次のコマンドを使用して、作成した NFS 共有を Data Box Gateway デバイスにマウントします。

   `sudo mount <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    マウントを設定する前に、ローカル コンピューターのマウント ポイントとして機能するディレクトリが既に作成されており、ファイルやサブフォルダーが含まれていないことを確認します。

    NFS 経由で Gateway デバイス上の共有に接続する方法の例を次に示します。 仮想デバイスの IP は `10.10.10.60` で、共有 `mylinuxshare2` は ubuntuVM にマウントされており、マウント ポイントは `/home/databoxubuntuhost/gateway` です。

    `sudo mount -t nfs 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> 次の注意事項は、プレビュー リリースに適用されます。
> - 共有にファイルを作成した後に、ファイル名を変更することはできません。 
> - 共有からファイルを削除しても、ストレージ アカウントのエントリは削除されません。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Data Box Gateway に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * 共有の追加
> * 共有への接続


次のチュートリアルに進み、Data Box Gateway を管理する方法を学習してください。

> [!div class="nextstepaction"]
> [ローカル Web UI を使用して Data Box Gateway を管理する](https://aka.ms/dbg-docs)


