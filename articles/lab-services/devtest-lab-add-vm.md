---
title: Azure DevTest Labs でのラボへの VM の追加 | Microsoft Docs
description: Azure DevTest Labs でラボに仮想マシンを追加する方法を説明します。
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: spelluru
ms.openlocfilehash: ce95a2177260e97113fd5e639671075eb6ad40cd
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215018"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs でラボに VM を追加する
[最初の VM 作成](devtest-lab-create-first-vm.md)の作業を行った方は、おそらくプレインストールされている[マーケットプレイス イメージ](devtest-lab-configure-marketplace-images.md)から VM を追加したことと思います。 その後さらに VM をラボに追加する必要が生じた場合は、"*ベース*" ([カスタム イメージ](devtest-lab-create-template.md)または[数式](devtest-lab-manage-formulas.md)) を選択することもできます。 このチュートリアルでは、Azure Portal を使用して、DevTest Labs でラボに VM を追加します。

この記事では、ラボで VM のアーティファクトを管理する方法についても説明します。

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs でのラボへの VM の追加手順
1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
1. ラボの一覧で、VM を作成するラボを選択します。  
1. ラボの **[概要]** ウィンドウで、**[+ 追加]** を選択します。  

    ![VM ボタンを追加する](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. **[ベースの選択]** ウィンドウで、VM のベースを選択します。
1. **[仮想マシン]** ウィンドウでは、自動生成された一意の名前で**仮想マシン名**が事前入力されます。 この名前は、電子メール アドレス内のユーザー名に対応しており、その後に一意の 3 桁の数字が続きます。 この機能により、マシンを作成するたびにマシンの名前を考えて入力する時間を節約できます。 必要に応じて、任意の名前でこの自動入力されるフィールドをオーバーライドすることができます。 VM の自動入力される名前をオーバーライドするには、**[仮想マシン名]** テキスト ボックスに名前を入力します。 

    ![ラボの VM ウィンドウ](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. マシンの**ユーザー名**は、自動生成された一意の名前で事前入力されます。 この名前は、電子メール アドレス内のユーザー名に対応しています。 この機能により、新しいマシンを作成するたびにユーザー名を決める時間を節約できます。 ここでも、必要に応じて、任意のユーザー名でこの自動入力されるフィールドをオーバーライドすることができます。 ユーザー名の自動入力される値をオーバーライドするには、**[ユーザー名]** テキスト ボックスに値を入力します。 このユーザーには、仮想マシンの**管理者**権限が付与されます。     
1. **パスワード**:
    
    ラボで最初の VM を作成する場合、**[値を入力します]** テキスト ボックスにパスワードを入力します。 このパスワードをラボに関連付けられている Azure Key Vault での既定のパスワードとして保存するには、**[既定のパスワードとして保存する]** を選択します。 既定のパスワードは、**VmPassword** の名前でキー コンテナーに保存されます。 ラボで次の VM を作成しようとすると、**VmPassword** が**パスワード**に自動的に選択されます。 値をオーバーライドするには、**[保存されているシークレットを使用する]** チェック ボックスをオフにして、パスワードを入力します。 

    最初にシークレットをキー コンテナーに保存してから、ラボで VM を作成するときにそれを使用することもできます。 詳細については、「[Azure DevTest Labs でキー コンテナーにシークレットを格納する](devtest-lab-store-secrets-in-key-vault.md)」を参照してください。 キー コンテナーに格納されているパスワードを使用するには、**[保存されているシークレットを使用する]** を選択し、シークレット (パスワード) に対応するキー値を指定します。 
3. **仮想マシンのディスクの種類**によって、ラボの仮想マシンで許可されるストレージ ディスクの種類が決まります。
4. **[仮想マシン サイズ]** を選択し、定義済みの項目の中から、作成する VM のプロセッサ コア、RAM サイズ、ハード ドライブ サイズを指定するものを選びます。
5. **[アーティファクト]** を選択し、アーティファクトの一覧から基本イメージに追加するアーティファクトを選択して構成します。
    **注:** DevTest Labs やアーティファクトの構成に関する経験がない場合は、「[既存のアーティファクトの VM への追加](#add-an-existing-artifact-to-a-vm)」セクションを参照し、終了してからここに戻ってください。
6. **[詳細設定]** を選択して VM のネットワーク オプションと有効期限のオプションを構成します。 

   有効期限オプションを設定するには、予定表アイコンを選択して、VM が自動的に削除される日付を指定します。  既定では、VM は無期限に設定されます。 
1. Azure Resource Manager テンプレートを表示またはコピーする場合は、「[Azure Resource Manager テンプレートの保存](#save-azure-resource-manager-template)」セクションを参照し、終了してからここに戻ってください。
1. **[作成]** を選択して、指定した VM をラボに追加します。
1. ラボのウィンドウには VM の作成状況が表示されます。最初は **[作成中]**、VM が起動した後は **[実行中]** と表示されます。

> [!NOTE]
> [要求可能 VM の追加](devtest-lab-add-claimable-vm.md)に関する記事では、ラボのあらゆるユーザーが使用できるように VM を要求可能にする方法について説明しています。
>
>

## <a name="add-an-existing-artifact-to-a-vm"></a>既存のアーティファクトの VM への追加
VM を作成するときに、既存のアーティファクトを追加できます。 各ラボには、パブリック DevTest ラボ アーティファクト リポジトリのアーティファクトと、独自のアーティファクト リポジトリに作成または追加されたアーティファクトが含まれます。

* Azure DevTest Labs "*アーティファクト*" を使用すると、VM のプロビジョニング時に実行される "*アクション*" (Windows PowerShell スクリプトの実行、Bash コマンドの実行、ソフトウェアのインストールなど) を指定できます。
* アーティファクト "*パラメーター*" を使用すると、特定のシナリオのアーティファクトをカスタマイズできます。

アーティファクトの作成方法については、 [DevTest Labs VM で使用するための独自のアーティファクトを作成する方法](devtest-lab-artifact-author.md)に関する記事を参照してください。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
1. ラボの一覧で、使用する VM が含まれるラボを選択します。  
1. **[仮想マシン]** を選択します。
1. 目的の VM を選択します。
1. **[アーティファクトの管理]** を選択します。 
1. **[アーティファクトの適用]** を選択します。
1. **[アーティファクトの適用]** ウィンドウで、VM に追加するアーティファクトを選択します。
1. **[アーティファクトの追加]** ウィンドウで、必須のパラメーター値を入力します。必要に応じて、省略可能なパラメーターも入力します。  
1. **[追加]** を選択してアーティファクトを追加し、**[アーティファクトの適用]** ウィンドウに戻ります。
1. VM に必要なアーティファクトを引き続き追加します。
1. アーティファクトを追加したら、 [アーティファクトの実行順を変更する](#change-the-order-in-which-artifacts-are-run)ことができます。 [アーティファクトの表示または変更](#view-or-modify-an-artifact)に戻ることもできます。
1. アーティファクトの追加が完了したら、**[適用]** を選択します。

## <a name="change-the-order-in-which-artifacts-are-run"></a>アーティファクトの実行順序の変更
既定では、アーティファクトのアクションは VM に追加された順序で実行されます。 次の手順では、アーティファクトの実行順序を変更する方法を示しています。

1. **[アーティファクトの適用]** ウィンドウの上部で、VM に追加されたアーティファクトの数を示すリンクを選択します。
   
    ![Number of artifacts added to VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. **[選択されたアーティファクト]** ウィンドウに、目的の順序になるようにアーティファクトをドラッグ アンド ドロップします。 **注:** アーティファクトをドラッグできない場合は、アーティファクトの左側からドラッグしていることを確認してください。 
1. 完了したら、 **[OK]** を選択します。  

## <a name="view-or-modify-an-artifact"></a>アーティファクトの表示または変更
次の手順では、アーティファクトのパラメーターを表示または変更する方法を示しています。

1. **[アーティファクトの適用]** ウィンドウの上部で、VM に追加されたアーティファクトの数を示すリンクを選択します。
   
    ![Number of artifacts added to VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. **[選択されたアーティファクト]** ウィンドウで、表示または編集するアーティファクトを選択します。  
1. **[アーティファクトの追加]** ウィンドウで必要な変更を行ったら、**[OK]** を選択して **[アーティファクトの追加]** ウィンドウを閉じます。
1. **[OK]** を選択して、**[選択されたアーティファクト]** ウィドウを閉じます。

## <a name="save-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの保存
Azure Resource Manager テンプレートでは、反復可能なデプロイを定義する宣言的な方法が提供されます。 次の手順では、作成される VM の Azure Resource Manager テンプレートを保存する方法について説明します。
保存した Azure Resource Manager テンプレートで、[Azure PowerShell を使用して新しい VM をデプロイ](../azure-resource-manager/resource-group-overview.md#template-deployment)できます。

1. **[仮想マシン]** ウィンドウで、**[Azure Resource Manager テンプレートの表示]** を選択します。
2. **[Azure Resource Manager テンプレートの表示]** ウィンドウで、テンプレート テキストを選択します。
3. 選択したテキストをクリップボードにコピーします。
4. **[OK]** を選択して、**[Azure Resource Manager テンプレートの表示]** ウィンドウを閉じます。
5. テキスト エディターを開きます。
6. クリップボードからテンプレート テキストを貼り付けます。
7. 後で使用するためにファイルを保存します。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>次の手順
* VM が作成されたら、VM のウィンドウで **[接続]** を選択して VM に接続できます。
* [DevTest Labs VM のカスタム アーティファクトの作成](devtest-lab-artifact-author.md)方法を学習します。
* [DevTest Labs Azure Resource Manager のクイックスタート テンプレート ギャラリー](https://github.com/Azure/azure-devtestlab/tree/master/Samples)を検索します。
