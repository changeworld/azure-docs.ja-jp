---
title: Azure Lab Services でマルチ VM 環境を有効にする | Microsoft Docs
description: Azure Lab Services のクラスルーム ラボでテンプレート仮想マシン内に複数の仮想マシンを含む環境を作成する方法について説明します。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 9a86ba803f899e78b2ba9640e6cc317966969e64
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332375"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>クラスルーム ラボのテンプレート VM 内に複数の VM を備えた環境を作成する
現在、Azure Lab Services では、ラボ内に 1 つのテンプレート仮想マシンを設定し、各ユーザーが 1 つのコピーを使用できるようにすることができます。 ただし、ファイアウォールやサーバーの設定方法について IT クラスに講義する教授であれば、必要に応じて、複数の仮想マシンがネットワークを介して相互に通信できる環境を各学生に用意します。

入れ子になった仮想化を使用すると、ラボのテンプレート仮想マシン内にマルチ VM 環境を作成できます。 テンプレートを公開すると、ラボ内の各ユーザーに、内部に複数の VM が設定された仮想マシンが提供されます。

## <a name="what-is-nested-virtualization"></a>入れ子になった仮想化の概要
入れ子になった仮想化を使用すると、1 つの仮想マシン内に複数の仮想マシンを作成できます。 入れ子になった仮想化は Hyper-V を介して行われ、Windows VM でのみ使用できます。

入れ子になった仮想化の詳細については、以下の記事を参照してください。

- [Azure の入れ子になった仮想化](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Azure VM で入れ子になった仮想化を有効にする方法](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Azure Lab Services で入れ子になった仮想化を使用する
重要な手順は次のとおりです。

1. ラボ用に **L** サイズの **Windows** テンプレート マシンを作成します。 
2. それに接続して、[入れ子になった仮想化を有効にします](../../virtual-machines/windows/nested-virtualization.md)。


次に、その手順を詳しく説明します。 

1. まだお持ちでない場合は、ラボ アカウントを作成します。 手順については、「[チュートリアル:Azure Lab Services でラボ アカウントを設定する](tutorial-setup-lab-account.md)」を参照してください。
1. [Azure Lab Services Web サイト](https://labs.azure.com)に移動します。 Internet Explorer 11 はまだサポートされていないことに注意してください。 
2. **[サインイン]** を選択して、資格情報を入力します。 Azure Lab Services では、組織アカウントと Microsoft アカウントがサポートされています。 
3. **[New lab]\(新しいラボ\)** を選択します。 
    
    ![クラスルーム ラボを作成する](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. **[New Lab]\(新しいラボ\)** ウィンドウで、次のようにします。 
    1. ラボの**名前**を指定します。 
    2. **仮想マシン サイズ**として **[Large (Nested Virtualization)]\(大 (入れ子になった仮想化)\)** または **[Medium (Nested Virtualization)]\(中 (入れ子になった仮想化)\)** のサイズを選択します。
    6. 使用する Windows **イメージ**を選択します。 入れ子になった仮想化は、Windows マシンでのみ使用できます。 
    4. 次に、 **[次へ]** を選択します。 

        ![クラスルーム ラボを作成する](../media/how-to-enable-multi-vm-environment/new-lab-window.png)
    1. **[Virtual machine credentials]\(仮想マシンの資格情報\)** ページで、ラボ内のすべての VM 用の既定の資格情報を指定します。 ユーザーの**名前**と**パスワード**を指定し、 **[次へ]** を選択します。  

        ![新しいラボのウィンドウ](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > ユーザー名とパスワードはメモしておいてください。 これらは再表示されません。
    3. **[Lab policies]\(ラボのポリシー\)** ページで、ラボに対してスケジュールされた時間外で各ユーザーに割り当てられる時間数を入力し ( **[quota for each user]\(各ユーザーのクォータ\)** )、 **[完了]** を選択します。 

        ![[Quota for each user]\(各ユーザーのクォータ\)](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. テンプレート VM の作成の状態を示す次の画面が表示されます。 ラボ内のテンプレートの作成には、最大 20 分がかかります。 

    ![テンプレート VM の作成の状態](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
1. **[テンプレート]** ページで、ツール バーの **[Customize template]\(テンプレートのカスタマイズ\)** を選択します。 

    ![[Customize template]\(テンプレートのカスタマイズ\) ボタン](../media/how-to-create-manage-template/customize-template-button.png)
2. **[Customize template]\(テンプレートのカスタマイズ\)** ダイアログで **[続行]** を選択します。 テンプレートを開始し、変更を加えると、ユーザーに最後に公開された仮想マシンと同じ設定ではなくなります。 再び公開するまで、ユーザーの既存の仮想マシンにはテンプレートの変更が反映されません。

    ![[カスタマイズ] ダイアログ ボックス](../media/how-to-create-manage-template/customize-template-dialog.png)
1. ツール バーで **[Connect to template]\(テンプレートに接続する\)** を選択してテンプレート VM に接続し、入れ子になった仮想化を構成し、指示に従います。 Windows コンピューターの場合、RDP ファイルをダウンロードするオプションが表示されます。 

    ![テンプレート VM に接続する](../media/how-to-create-manage-template/connect-template-vm.png) 
9. テンプレート仮想マシン内で、入れ子になった仮想化を設定し、複数の仮想マシンがある仮想ネットワークを構成します。 詳細な手順については、「[Azure VM で入れ子になった仮想化を有効にする方法](../../virtual-machines/windows/nested-virtualization.md)」を参照してください。 手順の概要を次に示します。 
    1. テンプレート仮想マシンで Hyper-V 機能を有効にします。
    2. 入れ子になった仮想マシンにインターネット接続を使用して内部仮想ネットワークを設定します
    3. Hyper-V マネージャーで仮想マシンを作成します
    4. IP アドレスを仮想マシンに割り当てます
10. **[テンプレート]** ページで、ツール バーの **[発行]** を選択します。 

    ![テンプレートを発行するボタン](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 一度発行すると、再発行することはできません。 
8. **[Publish template]\(テンプレートの発行\)** ページで、ラボに作成する仮想マシンの数を入力し、 **[発行]** を選択します。 

    ![テンプレートの発行 - VM の数](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. ページには、テンプレートの**発行の状態**が表示されます。 このプロセスには、最大で 1 時間かかることがあります。 

    ![テンプレートの発行 - 進行状況](../media/tutorial-setup-classroom-lab/publish-template-progress.png)


## <a name="next-steps"></a>次の手順

これで、各ユーザーは、内部にマルチ VM 環境を含む 1 つの仮想マシンを取得するようになりました。 ラボにユーザーを追加して登録リンクを送信する方法については、次の記事を参照してください。[ラボへのユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)。