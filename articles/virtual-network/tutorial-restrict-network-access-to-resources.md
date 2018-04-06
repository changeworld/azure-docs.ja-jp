---
title: PaaS リソースへのネットワーク アクセスを制限する - Azure Portal | Microsoft Docs
description: Azure Portal を使って仮想ネットワーク サービスのエンドポイントで Azure Storage、Azure SQL Database などの Azure リソースへのアクセスを制限する方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 9a64a5c1f63dc05cba6fdfa310b694e34bdba7d1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Azure Portal を使用して仮想ネットワーク サービスのエンドポイントで PaaS リソースへのネットワーク アクセスを制限する

仮想ネットワーク サービスのエンドポイントを使うと、一部の Azure サービス リソースへのネットワーク アクセスを、仮想ネットワーク サブネットに制限できます。 また、リソースに対するインターネット アクセスを排除することもできます。 サービス エンドポイントにより、お使いの仮想ネットワークからサポートされている Azure サービスへの直接接続が提供されるため、自分の仮想ネットワークのプライベート アドレス空間を使って、Azure サービスにアクセスできます。 サービス エンドポイントを介して Azure リソースに送信されるトラフィックは、常に Microsoft Azure のバックボーン ネットワーク上に留まります。 この記事では、次のことについて説明します:

> [!div class="checklist"]
> * 1 つのサブネットを含む仮想ネットワークを作成する
> * サブネットを追加し、サービス エンドポイントを有効にする
> * Azure リソースを作成し、サブネットからのみネットワーク アクセスできるようにする
> * 各サブネットに仮想マシン (VM) を展開する
> * サブネットからリソースへのアクセスを確認する
> * サブネットおよびインターネットからリソースへのアクセスが拒否されたことを確認する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="log-in-to-azure"></a>Azure にログインする 

Azure Portal (http://portal.azure.com) にログインします。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[ネットワーク]** を選択してから、**[仮想ネットワーク]** を選択します。
3. 以下の情報を入力するか選んだ後、**[作成]** を選びます。

    |Setting|値|
    |----|----|
    |Name| myVirtualNetwork |
    |アドレス空間| 10.0.0.0/16|
    |[サブスクリプション]| サブスクリプションを選択します。|
    |リソース グループ | **[新規作成]** を選択し、「*myResourceGroup*と入力します。|
    |場所| **[米国東部]** を選択します。 |
    |サブネット名| Public|
    |サブネットのアドレス範囲| 10.0.0.0/24|
    |サービス エンドポイント| 無効|

    ![仮想ネットワークに関する基本情報を入力する](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)


## <a name="enable-a-service-endpoint"></a>サービス エンドポイントを有効にする

1. ポータルの上部にある **[リソース、サービス、ドキュメントの検索]** ボックスに「*myVirtualNetwork*」と入力します。 検索結果に **[myVirtualNetwork]** が表示されたら、それを選択します。
2. 仮想ネットワークにサブネットを追加します。 次の図に示すように、**[設定]** で **[サブネット]** を選んで、**[+ サブネット]** を選びます。

    ![サブネットの追加](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. **[サブネットの追加]** で以下の情報を選ぶか入力し、**[OK]** を選びます。

    |Setting|値|
    |----|----|
    |Name| Private |
    |アドレス範囲| 10.0.1.0/24|
    |サービス エンドポイント| **[サービス]** で **Microsoft.Storage** を選びます|

## <a name="restrict-network-access-to-and-from-a-subnet"></a>サブネットとの間のネットワーク アクセスを制限する

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[ネットワーク]** を選び、**[ネットワーク セキュリティ グループ]** を選びます。
**[ネットワーク セキュリティ グループの作成]** で、以下の情報を入力するか選んだ後、**[作成]** を選びます。

    |Setting|値|
    |----|----|
    |Name| myNsgPrivate |
    |[サブスクリプション]| サブスクリプションを選択します。|
    |リソース グループ | **[既存のものを使用]** を選択し、*[myResourceGroup]* を選択します。|
    |場所| **[米国東部]** を選択します。 |

4. ネットワーク セキュリティ グループが作成された後、ポータルの上部にある **[リソース、サービス、ドキュメントの検索]** ボックスに「*myNsgPrivate*」と入力します。 検索結果に **myNsgPrivate** が表示されたら、それを選びます。
5. **[設定]** で **[送信セキュリティ規則]** を選びます。
6. **[+ 追加]**を選択します。
7. Azure Storage サービスに割り当てられたパブリック IP アドレスへの送信アクセスを許可する規則を作成します。 以下の情報を入力するか選んだ後、**[OK]** を選びます。

    |Setting|値|
    |----|----|
    |ソース| **VirtualNetwork** を選びます。 |
    |ソース ポート範囲| * |
    |変換先 | **[Service Tag]\(サービス タグ\)** を選びます|
    |宛先サービス タグ | **[ストレージ]** を選びます|
    |宛先ポート範囲| * |
    |プロトコル|任意|
    |アクションを表示します。|ALLOW|
    |優先順位|100|
    |Name|Allow-Storage-All|
8. すべてのパブリック IP アドレスへの送信アクセスを許可する既定のセキュリティ規則を上書きする規則を作成します。 ステップ 6 と 7 を繰り返して、次の値を指定します。

    |Setting|値|
    |----|----|
    |ソース| **VirtualNetwork** を選びます。 |
    |ソース ポート範囲| * |
    |変換先 | **[Service Tag]\(サービス タグ\)** を選びます|
    |宛先サービス タグ| **[インターネット]** を選びます|
    |宛先ポート範囲| * |
    |プロトコル|任意|
    |アクションを表示します。|拒否|
    |優先順位|110|
    |Name|Deny-Internet-All|

9. **[設定]** で **[受信セキュリティ規則]** を選びます。
10. **[+ 追加]**を選択します。
11. 任意の場所からサブネットへのリモート デスクトップ プロトコル (RDP) 受信トラフィックを許可する規則を作成します。 この規則は、インターネットからのすべての受信トラフィックを拒否する既定のセキュリティ規則を上書きします。 後のステップで接続をテストできるように、サブネットへのリモート デスクトップ接続を許可します。 ステップ 6 と 7 を繰り返して、次の値を指定します。

    |Setting|値|
    |----|----|
    |ソース| 任意 |
    |ソース ポート範囲| * |
    |変換先 | **[Service Tag]\(サービス タグ\)** を選びます|
    |宛先サービス タグ| **VirtualNetwork** を選びます。|
    |宛先ポート範囲| 3389 |
    |プロトコル|任意|
    |アクションを表示します。|ALLOW|
    |優先順位|120|
    |Name|Allow-RDP-All|

12. **[設定]** で、**[サブネット]** を選択します。
13. **[+ 関連付け]** を選びます
14. **[サブネットの関連付け]** で **[仮想ネットワーク]** を選んだ後、**[仮想ネットワークの選択]** で **myVirtualNetwork** を選びます。
15. **[サブネットの選択]** で **Private** を選び、**[OK]** を選びます。

## <a name="restrict-network-access-to-a-resource"></a>リソースへのネットワーク アクセスを制限する

サービス エンドポイントが有効な Azure サービスを介して作成されたリソースへのネットワーク アクセスを制限するために必要な手順は、サービスによって異なります。 各サービスの具体的な手順については、それぞれのサービスのドキュメントをご覧ください。 この記事の残りの部分では、例として、Azure ストレージ アカウントのネットワーク アクセスを制限する手順を示します。

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[ストレージ]**、**[ストレージ アカウント - Blob、File、Table、Queue]** の順に選択します。
3. 次の情報を入力するか選び、それ以外の情報は既定値を選んで、**[作成]** を選びます。

    |Setting|値|
    |----|----|
    |Name| すべての Azure の場所で一意の名前 (3 ～ 24 文字で、数字と小文字のみを使用) を入力します。|
    |アカウントの種類|StorageV2 (汎用 v2)|
    |レプリケーション| ローカル冗長ストレージ (LRS)|
    |[サブスクリプション]| サブスクリプションを選択します。|
    |リソース グループ | **[既存のものを使用]** を選択し、*[myResourceGroup]* を選択します。|
    |場所| **[米国東部]** を選択します。 |

### <a name="create-a-file-share-in-the-storage-account"></a>ストレージ アカウントにファイル共有を作成する

1. ストレージ アカウントを作成した後、ポータルの上部にある **[リソース、サービス、ドキュメントの検索]** ボックスにストレージ アカウントの名前を入力します。 指定したストレージ アカウントの名前が検索結果に表示されたら、それを選びます。
2. 次の図に示すように、**[ファイル]** を選びます。

    ![ストレージ アカウント](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 
3. **[File サービス]** で **[+ File share]\(+ ファイル共有\)** を選びます。
4. **[名前]** に「*my-file-share*」と入力し、**[OK]** を選びます。
5. **[File サービス]** ボックスを閉じます。

### <a name="enable-network-access-from-a-subnet"></a>サブネットからのネットワーク アクセスを有効にする

既定では、ストレージ アカウントは、任意のネットワーク上のクライアントからのネットワーク接続を受け入れます。 特定のサブネットからのアクセスだけを許可し、他のすべてのネットワークからのネットワーク アクセスを拒否するには、次の手順のようにします。

1. ストレージ アカウントの **[設定]** で、**[ファイアウォールと仮想ネットワーク]** を選びます。
2. **[仮想ネットワーク]** で、**[選択されたネットワーク]** を選びます。
3. **[既存の仮想ネットワークを追加]** を選びます。
4. **[ネットワークの追加]** で次の値を選んで、**[追加]** を選びます。

    |Setting|値|
    |----|----|
    |[サブスクリプション]| サブスクリプションを選択します。|
    |仮想ネットワーク|**[仮想ネットワーク]** で **myVirtualNetwork** を選びます。|
    |サブネット| **[サブネット]** で **Private** を選びます|

    ![ファイアウォールと仮想ネットワーク](./media/tutorial-restrict-network-access-to-resources/storage-firewalls-and-virtual-networks.png) 

5. **[保存]** を選択します。
6. **[ファイアウォールと仮想ネットワーク]** ボックスを閉じます。
7. 次の図に示すように、ストレージ アカウントの **[設定]** で、**[アクセス キー]** を選びます。

      ![ファイアウォールと仮想ネットワーク](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

8. **[キー]** の値をメモします。後の手順でファイル共有を VM のドライブ文字にマップするときに、この値を手入力する必要があります。

## <a name="create-virtual-machines"></a>仮想マシンを作成する

ストレージ アカウントへのネットワーク アクセスをテストするには、各サブネットに VM を展開します。

### <a name="create-the-first-virtual-machine"></a>最初の仮想マシンを作成する

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[コンピューティング]**、**[Windows Server 2016 Datacenter]** の順に選択します。
3. 以下の情報を入力するか選んだ後、**[OK]** を選びます。

    |Setting|値|
    |----|----|
    |Name| myVmPublic|
    |ユーザー名|任意のユーザー名を入力します。|
    |パスワード| 任意のパスワードを入力します。 パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。|
    |[サブスクリプション]| サブスクリプションを選択します。|
    |リソース グループ| **[既存のものを使用]** を選択し、**[myResourceGroup]** を選択します。|
    |場所| **[米国東部]** を選択します。|

    ![仮想マシンに関する基本情報を入力する](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)
4. 仮想マシンのサイズを選択して、**[選択]** を選択します。
5. **[設定]** で **[ネットワーク]** を選び、**myVirtualNetwork** を選びます。 次の図に示すように、**[サブネット]** を選んで、**Public** を選びます。

    ![仮想ネットワークを作成する](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)
6. **[概要]** ページで、**[作成]** を選択して、仮想マシンのデプロイを開始します。 VM の展開には数分かかりますが、VM の作成中に次の手順に進むことができます。

### <a name="create-the-second-virtual-machine"></a>2 番目の仮想マシンを作成する

ステップ 1 ～ 6 を繰り返します。ただし、ステップ 3 では仮想マシンの名前として *myVmPrivate* を指定し、ステップ 5 では **Private** サブネットを選びます。

VM のデプロイには数分かかります。 作成が完了して設定がポータルに表示されるまで、次の手順を続行しないでください。

## <a name="confirm-access-to-storage-account"></a>ストレージ アカウントへのアクセスを確認する

1. *myVmPrivate* VM の作成が完了すると、Azure にその設定が開きます。 次の図に示すように、**[接続]** ボタンを選んで VM に接続します。

    ![仮想マシンへの接続](./media/tutorial-restrict-network-access-to-resources/connect-to-virtual-machine.png)

2. **[接続]** ボタンを選択した後、リモート デスクトップ プロトコル (.rdp) ファイルが作成され、お使いのコンピューターにダウンロードされます。  
3. ダウンロードされた rdp ファイルを開きます。 メッセージが表示されたら、**[Connect]** を選択します。 VM の作成時に指定したユーザー名とパスワードを入力します。 場合によっては、**[その他]**、**[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。 
4. **[OK]**を選択します。
5. サインイン処理中に証明書の警告が表示される場合があります。 警告を受け取ったら、**[はい]** または **[続行]** を選択して接続処理を続行します。
6. *myVmPrivate* VM で、PowerShell を使って、Azure ファイル共有を Z ドライブにマップします。 次のコマンドを実行する前に、`<storage-account-key>` と `<storage-account-name>` を、前の手順で指定して「[ストレージ アカウントの作成](#create-a-storage-account)」で取得した値に置き換えます。

    ```powershell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
    New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
    ```
    
    PowerShell によって、次のサンプル出力のような出力が返されます。

    ```powershell
    Name           Used (GB)     Free (GB) Provider      Root
    ----           ---------     --------- --------      ----
    Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
    ```

    Azure ファイル共有は Z ドライブに正常にマップされました。

7. VM のコマンド プロンプトから他のパブリック IP アドレスへの送信接続がないことを確認します。

    ```
    ping bing.com
    ```
    
    応答はありません。これは、*Private* サブネットに関連付けられているネットワーク セキュリティ グループでは、Azure Storage サービスに割り当てられているアドレス以外のパブリック IP アドレスへの発信アクセスが許可されていないためです。

8. *myVmPrivate* VM へのリモート デスクトップ セッションを閉じます。

## <a name="confirm-access-is-denied-to-storage-account"></a>ストレージ アカウントへのアクセスが拒否されたことを確認する

1. ポータルの上部にある *[リソース、サービス、ドキュメントの検索]* ボックスに「**myVmPublic**」と入力します。
2. 検索結果に **myVmPublic** が表示されたら、それを選びます。
3. 「[ストレージ アカウントへのアクセスを確認する](#confirm-access-to-storage-account)」のステップ 1 ～ 6 を、*myVmPublic* VM に対して実行します。

    アクセスが拒否され、`New-PSDrive : Access is denied` エラーが発生します。 *myVmPublic* VM が *Public* サブネットに展開されているため、アクセスが拒否されました。 *Public* サブネットでは、Azure Storage に対してサービス エンドポイントが有効になっていません。ストレージ アカウントが許可しているのは *Public* サブネットからではなく、*Private* サブネットからのネットワーク アクセスです。

4. *myVmPublic* VM へのリモート デスクトップ セッションを閉じます。

5. お使いのコンピューターから、[Azure Portal](https://portal.azure.com) にアクセスします。
6. **[リソース、サービス、ドキュメントの検索]** ボックスに、作成したストレージ アカウントの名前を入力します。 指定したストレージ アカウントの名前が検索結果に表示されたら、それを選びます。
7. **[ファイル]** を選択します。
8. 次の図に示すようにエラーが表示されます。

    ![アクセス拒否エラー](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)

    お使いのコンピューターは *MyVirtualNetwork* 仮想ネットワークの *Private* サブネットに含まれていないため、アクセスが拒否されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループとそれに含まれるすべてのリソースが不要になったら、それらを削除します。

1. ポータル上部の **[検索]** ボックスに「*myResourceGroup*」と入力します。 検索結果に **[myResourceGroup]** が表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[TYPE THE RESOURCE GROUP NAME:]\(リソース グループ名を入力してください:\)** に「*myResourceGroup*」と入力し、**[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、仮想ネットワーク サブネットのサービス エンドポイントを有効にしました。 複数の Azure サービスでデプロイされているリソースに対して、サービス エンドポイントを有効にできることを学習しました。 Azure ストレージ アカウントを作成し、そのストレージ アカウントへのネットワーク アクセスを、仮想ネットワーク サブネット内のリソースだけに制限しました。 サービス エンドポイントを運用仮想ネットワークに作成する前に、[サービス エンドポイント](virtual-network-service-endpoints-overview.md)について十分に理解しておくことをお勧めします。

アカウントに複数の仮想ネットワークがある場合は、各仮想ネットワーク内のリソースが相互に通信できるように、2 つの仮想ネットワークを接続することもできます。 次のチュートリアルに進み、仮想ネットワークを接続する方法を学習してください。

> [!div class="nextstepaction"]
> [仮想ネットワークを接続する](./tutorial-connect-virtual-networks-portal.md)
