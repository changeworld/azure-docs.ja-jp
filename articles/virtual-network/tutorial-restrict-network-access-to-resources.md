---
title: PaaS リソースへのアクセスを制限する - チュートリアル - Azure portal
description: このチュートリアルでは、Azure Portal を使って仮想ネットワーク サービス エンドポイントで Azure Storage、Azure SQL Database などの Azure リソースへのアクセスを制限する方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/11/2020
ms.author: kumud
ms.openlocfilehash: 22494d292077f4b6018a4512b45b5fe2caa9c8ee
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057203"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>チュートリアル: Azure Portal を使用して仮想ネットワーク サービス エンドポイントで PaaS リソースへのネットワーク アクセスを制限する

仮想ネットワーク サービス エンドポイントを使うと、一部の Azure サービス リソースへのネットワーク アクセスを、仮想ネットワーク サブネットに制限できます。 また、リソースに対するインターネット アクセスを排除することもできます。 サービス エンドポイントにより、使用している仮想ネットワークからサポートされている Azure サービスへの直接接続が提供されるため、ご自身の仮想ネットワークのプライベート アドレス スペースを使用して、Azure サービスにアクセスできるようになります。 サービス エンドポイントを介して Azure リソースに送信されるトラフィックは、常に Microsoft Azure のバックボーン ネットワーク上に留まります。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 1 つのサブネットを含む仮想ネットワークを作成する
> * サブネットを追加し、サービス エンドポイントを有効にする
> * Azure リソースを作成し、サブネットからのみネットワーク アクセスできるようにする
> * 各サブネットに仮想マシン (VM) をデプロイする
> * サブネットからリソースへのアクセスを確認する
> * サブネットおよびインターネットからリソースへのアクセスが拒否されたことを確認する

好みに応じて、[Azure CLI](tutorial-restrict-network-access-to-resources-cli.md) または [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md) を使ってこのチュートリアルの手順を実行することもできます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure Portal (https://portal.azure.com ) にログインします。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[ネットワーク]** を選択してから、 **[仮想ネットワーク]** を選択します。
3. **[+ 追加]** をクリックし、次の情報を入力します。 

   |設定|値|
   |----|----|
   |サブスクリプション| サブスクリプションを選択します。|
   |Resource group | **[新規作成]** を選択し、「*myResourceGroup* と入力します。|
   |名前| 「*myVirtualNetwork*」と入力します |
   |リージョン| **[(米国) 米国東部]** を選択します |

   ![仮想ネットワークに関する基本情報を入力する](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)

4. **[次へ: IP アドレス >]** をクリックします
   
   |設定|値|
   |----|----|
   |IPv4 アドレス空間| 既定値のままにします |
   |サブネット名| **[default]\(既定\)** をクリックし、名前を "default (既定)" から "Public (パブリック)" に変更します|
   |サブネット アドレス範囲| 既定値のままにします|

5. **[次へ: セキュリティ >]** をクリックします。 
   
   |設定|値|
   |----|----|   
   |BastionHost| 無効|
   |DDoS 保護| 無効|
   |ファイアウォール| 無効|

6. 完了したら、 **[確認と作成]** をクリックします。
7. 検証チェックに成功したら、 **[作成]** をクリックします
8. デプロイの完了を待って、 **[リソースに移動]** をクリックするか次のセクションに進みます。 

## <a name="enable-a-service-endpoint"></a>サービス エンドポイントを有効にする

サービス エンドポイントはサービスごと、サブネットごとに有効にします。 サブネットを作成し、そのサブネットに対してサービス エンドポイントを有効にするには、次の手順に従います。

1. まだ仮想ネットワーク リソース ページが表示されていない場合は、新しく作成したネットワークを検索してください。ポータルの上部にある **[リソース、サービス、ドキュメントの検索]** ボックスに「*myVirtualNetwork*」と入力してリストから選択します。
2. 左側の **[設定]** メニューで **[サブネット]** を選択し、 **[+ サブネット]** を選択します (下図)。

    ![サブネットの追加](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. **[サブネットの追加]** で以下の情報を選ぶか入力し、 **[OK]** を選びます。

    |設定|値|
    |----|----|
    |Name| プライベート |
    |アドレス範囲| 既定値のままにします|
    |サービス エンドポイント| **[Microsoft.Storage]** を選択します|
    |サービス エンドポイント ポリシー | 既定値の 0 のままにします |

> [!CAUTION]
> リソースが含まれる既存のサブネットのサービス エンドポイントを有効にする前に、「[サブネット設定の変更](virtual-network-manage-subnet.md#change-subnet-settings)」を参照してください。

4. **[保存]** をクリックし、右側の [サブネット] ウィンドウを閉じます。 新しく作成したサブネットが一覧に表示されます。

## <a name="restrict-network-access-for-a-subnet"></a>サブネットのネットワーク アクセスを制限する

既定では、サブネット内のすべての仮想マシン インスタンスはすべてのリソースと通信できます。 ネットワーク セキュリティ グループを作成し、それをサブネットに関連付けることで、サブネット内のすべてのリソース間の通信を制限できます。

1. Azure portal の左上隅にある **[すべてのサービス]** を選択します。
2. **[ネットワーク]** を選択し、 **[ネットワーク セキュリティ グループ]** を選択 (または検索) します。
3. **[ネットワーク セキュリティ グループ]** ページの **[+ 追加]** をクリックします。
4. 次の情報を入力します 

    |設定|値|
    |----|----|
    |サブスクリプション| サブスクリプションを選択します。|
    |Resource group | 一覧から *[myResourceGroup]* を選択します|
    |名前| 「**myNsgPrivate**」と入力します |
    |場所| **[米国東部]** を選択します。 |

5. **[確認と作成]** をクリックし、検証チェックに成功したら、 **[作成]** をクリックします。
6. ネットワーク セキュリティ グループの作成後、 **[リソースに移動]** をクリックするか、*myNsgPrivate* を検索します。
7. 左側の **[設定]** で **[送信セキュリティ規則]** を選択します。
8. **[+ 追加]** を選択します。
9. Azure Storage サービスへの送信方向の通信を許可するルールを作成します。 次の情報を入力または選択し、 **[追加]** を選択します。

    |設定|値|
    |----|----|
    |source| **VirtualNetwork** を選びます。 |
    |Source port ranges| * |
    |宛先 | **[Service Tag]\(サービス タグ\)** を選びます|
    |宛先サービス タグ | **[ストレージ]** を選びます|
    |宛先ポート範囲| 既定値の *8080* のままにします |
    |Protocol|Any|
    |アクション|Allow|
    |優先度|100|
    |名前|**Allow-Storage-All** という名前に変更します|

10. インターネットへの通信を拒否する別の送信セキュリティ ルールを作成します。 このルールは、送信方向のインターネット通信を許可する、すべてのネットワーク セキュリティ グループ内の既定のルールをオーバーライドします。 次の値を使用して、手順 6. から手順 9. を実行します。

    |設定|値|
    |----|----|
    |source| **VirtualNetwork** を選びます。 |
    |Source port ranges| * |
    |宛先 | **[Service Tag]\(サービス タグ\)** を選びます|
    |宛先サービス タグ| **[インターネット]** を選びます|
    |宛先ポート範囲| * |
    |Protocol|Any|
    |アクション|**既定値を *[Deny]\(拒否\)* に変更します** |
    |Priority|110|
    |Name|*Deny-Internet-All* に変更します|

11. 任意の場所からサブネットへのリモート デスクトップ プロトコル (RDP) トラフィックを許可する "*インバウンド セキュリティ規則*" を作成します。 この規則は、インターネットからのすべての受信トラフィックを拒否する既定のセキュリティ規則をオーバーライドします。 後のステップで接続をテストできるように、サブネットへのリモート デスクトップ接続を許可します。 
12. **[設定]** で **[受信セキュリティ規則]** を選びます。
13. **[+ 追加]** を選択し、次の値を使用します。

    |設定|値|
    |----|----|
    |source| Any |
    |Source port ranges| * |
    |宛先 | **VirtualNetwork** を選びます。|
    |宛先ポート範囲| *3389* に変更します |
    |Protocol|Any|
    |アクション|Allow|
    |Priority|120|
    |Name|*Allow-RDP-All* に変更します|

   >[!WARNING] 
   > RDP ポート 3389 がインターネットに公開されます。 これはテストにのみ推奨されます。 "*運用環境*" では、VPN またはプライベート接続を使用することをお勧めします。

1.  **[設定]** で、 **[サブネット]** を選択します。
2.  **[+ 関連付け]** をクリックします。
3.  **[仮想ネットワーク]** で **[myVirtualNetwork]** を選択します。
4.  **[サブネット]** で **[Private]** を選択し、 **[OK]** を選択します。

## <a name="restrict-network-access-to-a-resource"></a>リソースへのネットワーク アクセスを制限する

サービス エンドポイントを有効にした Azure サービスを介して作成されたリソースへのネットワーク アクセスを制限するために必要な手順は、サービスによって異なります。 各サービスの具体的な手順については、それぞれのサービスのドキュメントをご覧ください。 このチュートリアルの残りの部分では、例として、Azure Storage アカウントのネットワーク アクセスを制限する手順を示します。

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. 検索バーに「ストレージ アカウント」と入力し、それをドロップダウン メニューから選択します。
3. **[+ 追加]** をクリックします。
4. 次の情報を入力します。

    |設定|値|
    |----|----|
    |サブスクリプション| サブスクリプションを選択します。|
    |Resource group| *[myResourceGroup]* を選択します|
    |ストレージ アカウント名| すべての Azure の場所で一意の名前 (3 ～ 24 文字で、数字と小文字のみを使用) を入力します。|
    |場所| **[(米国) 米国東部]** を選択します |
    |パフォーマンス|Standard|
    |アカウントの種類| StorageV2 (汎用 v2)|
    |レプリケーション| ローカル冗長ストレージ (LRS)|

5. **[Create + review]\(作成と確認\)** を選択し、検証チェックに成功したら、 **[作成]** をクリックします。 

>[!NOTE] 
> デプロイが完了するまでに数分かかる場合があります。

6. ストレージ アカウントの作成後、 **[リソースに移動]** をクリックします。

### <a name="create-a-file-share-in-the-storage-account"></a>ストレージ アカウントにファイル共有を作成する

1. ストレージ アカウントの概要ページに移動します。
2. **[ファイル共有]** アプリ アイコンを選択し、 **[+ ファイル共有]** をクリックします。

    |設定|値|
    |----|----|
    |名前| my-file-share|
    |Quota| "最大値に設定する" |

   ![ストレージ アカウント](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 

3. **Create** をクリックしてください。
4. Azure ウィンドウにファイル共有が表示されます。表示されない場合は、 **[Refresh]\(最新の情報に更新\)** をクリックします。

### <a name="restrict-network-access-to-a-subnet"></a>サブネットへのネットワーク アクセスを制限する

既定では、ストレージ アカウントは、インターネットを含む任意のネットワーク上のクライアントからのネットワーク接続を受け入れます。 インターネットおよびすべての仮想ネットワーク内のその他すべてのサブネットからのネットワーク アクセスを制限できます (*myVirtualNetwork* 仮想ネットワーク内の *Private* サブネットを除く)。サブネットへのネットワーク アクセスを制限するには、次の手順に従います。

1. 自分の (一意の名前の) ストレージ アカウントの **[設定]** で **[ネットワーク]** を選択します。
2. **[選択されたネットワーク]** を選択します。
3. **[+ 既存の仮想ネットワークを追加]** を選択します。
4. **[ネットワークの追加]** で次の値を選んで、 **[追加]** を選びます。

    |設定|値|
    |----|----|
    |サブスクリプション| サブスクリプションを選択します|
    |仮想ネットワーク| **myVirtualNetwork**|
    |サブネット| **プライベート**|

    ![[ネットワークの追加] ペインを示すスクリーンショット。ここで、指定された値を入力できます。](./media/tutorial-restrict-network-access-to-resources/virtual-networks.png)

5. **[追加]** をクリックした後、すぐに **[保存]** アイコンをクリックして変更を保存します。
6. 次の画像に示すように、ストレージ アカウントの **[設定]** で **[アクセス キー]** を選択します。

      ![[設定] で選択されている [アクセス キー] を示すスクリーンショット。ここで、キーを取得できます。](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

7. **[キーの表示]** をクリックし、 **[キー]** の値をメモします。後の手順でファイル共有を VM のドライブ文字にマップするときに、キー 1 の値を手入力する必要があります。

## <a name="create-virtual-machines"></a>仮想マシンを作成する

ストレージ アカウントへのネットワーク アクセスをテストするには、各サブネットに VM を展開します。

### <a name="create-the-first-virtual-machine"></a>最初の仮想マシンを作成する

1. [リソースの検索] . ." バーから **[Virtual machines]** を検索します。
2. **[+ 追加] > [仮想マシン]** を選択します。 
3. 次の情報を入力します。

   |設定|値|
   |----|----|
   |サブスクリプション| サブスクリプションを選択します。|
   |Resource group| 先ほど作成した **[myResourceGroup] を選択します。|
   |仮想マシン名| 「*myVmPublic*」と入力します|
   |リージョン | (米国) 米国東部
   |可用性のオプション| 可用性ゾーン|
   |可用性ゾーン | 1 |
   |Image | Windows Server 2019 Datacenter - Gen1 |
   |サイズ | 使用する VM インスタンスのサイズを選択します |
   |ユーザー名|任意のユーザー名を入力します。|
   |Password| 任意のパスワードを入力します。 パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。|
   |パブリック受信ポート | 選択したポートを許可する |
   |受信ポートの選択 | 既定の *[RDP (3389)]* のままにします |

   ![仮想ネットワークを作成する](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)
  
4. **[ネットワーク]** タブを選択し、 **[myVirtualNetwork]** を選択します。 
5. *[パブリック]* サブネットを選択します。
6. **[NIC ネットワーク セキュリティ グループ]** で **[高度]** を選択します。 ポータルで、ポート 3389 を許可するネットワーク セキュリティ グループが自動的に作成されます。ポート 3389 は、後で仮想マシンに接続するために開く必要があります。 

   ![仮想マシンに関する基本情報を入力する](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)

7. **[確認と作成]** を選択し、 **[作成]** を選択して、デプロイの完了を待ちます。
8. **[リソースに移動]** をクリックするか、 **[ホーム] > [Virtual Machines]** ページを開き、先ほど作成した VM (*myVmPublic*) を選択します。これで VM が起動します。

### <a name="create-the-second-virtual-machine"></a>2 番目の仮想マシンを作成する

1. もう一度手順 1. から手順 8. を実行しますが、今回は手順 3. で仮想マシン名を *myVmPrivate* とし、 **[パブリック受信ポート]** を [None]\(なし\) に設定します。 
2. 手順 4. から手順 5. で、 **[プライベート]** サブネットを選択します。

>[!NOTE]
> **[NIC ネットワーク セキュリティ グループ]** 設定と **[パブリック受信ポート]** 設定は、ちょうど次の画像のようになっていると思われます。たとえば、"すべてのパブリック インターネット トラフィックが既定でブロックされる" という内容のメッセージが青色の確認ウィンドウに表示されます。

   ![プライベート仮想マシンを作成する](./media/tutorial-restrict-network-access-to-resources/create-private-virtual-machine.png)

3. **[確認と作成]** を選択し、 **[作成]** を選択して、デプロイの完了を待ちます。 

>[!WARNING]
> デプロイが完了するまでは次の手順に進まないでください。

4. 以下の確認ウィンドウが表示されるまで待ってから、 **[リソースに移動]** をクリックします。

   ![プライベート仮想マシン作成の確認ウィンドウ](./media/tutorial-restrict-network-access-to-resources/create-vm-confirmation-window.png)

## <a name="confirm-access-to-storage-account"></a>ストレージ アカウントへのアクセスを確認する

1. *myVmPrivate* VM が作成されたら、 **[リソースに移動]** をクリックします。 
2. **[接続] > [RDP]** を選択して VM に接続します。
3. **[接続]** ボタンを選択した後、リモート デスクトップ プロトコル (.rdp) ファイルが作成されます。 **[RDP ファイルのダウンロード]** をクリックして、自分のコンピューターにダウンロードします。  
4. ダウンロードされた rdp ファイルを開きます。 メッセージが表示されたら、 **[Connect]** を選択します。 VM の作成時に指定したユーザー名とパスワードを入力します。 場合によっては、 **[その他]** 、 **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。 メール フィールドには、先ほど指定した "管理者アカウント: ユーザー名" の資格情報を入力します。 
5. **[OK]** を選択します。
6. サインイン処理中に証明書の警告が表示される場合があります。 警告を受け取ったら、 **[はい]** または **[続行]** を選択して接続処理を続行します。 次のように VM が起動していることがわかります。

   ![プライベート仮想マシンが実行中](./media/tutorial-restrict-network-access-to-resources/virtual-machine-running.png)

7. VM ウィンドウで PowerShell CLI インスタンスを開きます。
8. PowerShell で以下のスクリプトを使用して、Azure ファイル共有を Z ドライブにマップします。 次のコマンドを実行する前に、`<storage-account-key>` と両方の `<storage-account-name>` フィールドを、指定した値および「[ストレージ アカウントの作成](#create-a-storage-account)」で取得した値に置き換えます。

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

9.   *myVmPrivate* VM へのリモート デスクトップ セッションを閉じます。

## <a name="confirm-access-is-denied-to-storage-account"></a>ストレージ アカウントへのアクセスが拒否されたことを確認する

1. ポータルの上部にある *[リソース、サービス、ドキュメントの検索]* ボックスに「**myVmPublic**」と入力します。
2. 検索結果に **myVmPublic** が表示されたら、それを選びます。
3. 「[ストレージ アカウントへのアクセスを確認する](#confirm-access-to-storage-account)」の手順 1. から手順 8. を、*myVmPublic* VM に対して実行します。

   少し待つと、`New-PSDrive : Access is denied` エラーが発生します。 *myVmPublic* VM が *Public* サブネットに展開されているため、アクセスが拒否されました。 *Public* サブネットには Azure Storage に対して有効になっているサービス エンドポイントがありません。 ストレージ アカウントは *Private* サブネットからのネットワーク アクセスのみを許可し、*Public* サブネットからは許可しません。

4. *myVmPublic* VM へのリモート デスクトップ セッションを閉じます。
5. Azure portal に戻り、先ほど作成した一意の名前のストレージ アカウントに移動します。
6. [File サービス] の **[ファイル共有]** を選択し、先ほど作成した *[my-file-share]* を選択します。
7. 次のエラー メッセージが表示されます。

   ![アクセス拒否エラー](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)
   
>[!NOTE] 
> お使いのコンピューターは *MyVirtualNetwork* 仮想ネットワークの *Private* サブネットに含まれていないため、アクセスが拒否されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループとそれに含まれるすべてのリソースが不要になったら、それらを削除します。

1. ポータル上部の **[検索]** ボックスに「*myResourceGroup*」と入力します。 検索結果に **[myResourceGroup]** が表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[TYPE THE RESOURCE GROUP NAME:]\(リソース グループ名を入力してください:\)** に「*myResourceGroup*」と入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、仮想ネットワーク サブネットのサービス エンドポイントを有効にしました。 複数の Azure サービスからデプロイされているリソースに対して、サービス エンドポイントを有効にできることを学習しました。 Azure Storage アカウントを作成し、そのストレージ アカウントへのネットワーク アクセスを、仮想ネットワーク サブネット内のリソースのみに制限しました。 サービス エンドポイントの詳細については、[サービス エンドポイントの概要](virtual-network-service-endpoints-overview.md)と[サブネットの管理](virtual-network-manage-subnet.md)に関するページをご覧ください。

アカウントに複数の仮想ネットワークがある場合は、各仮想ネットワーク内のリソースが相互に通信できるように、2 つの仮想ネットワークを接続することもできます。 仮想ネットワークを接続する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [仮想ネットワークを接続する](./tutorial-connect-virtual-networks-portal.md)
