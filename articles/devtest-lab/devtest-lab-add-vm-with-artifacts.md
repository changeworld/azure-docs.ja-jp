<properties
	pageTitle="アーティファクトを含む VM をラボに追加する | Microsoft Azure"
	description="アーティファクトを含む VM を DevTest ラボに追加する方法について説明します"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/08/2016"
	ms.author="tarcher"/>

# アーティファクトを含む VM をラボに追加する

> [AZURE.NOTE] この記事の付属のビデオを表示するには、次のリンクをクリックします。[How to create VMs with artifacts in a lab](/documentation/videos/how-to-create-vms-with-artifacts-in-a-devtest-lab) (ラボでアーティファクトを含む VM を作成する方法)

## 概要

[カスタム イメージ](./devtest-lab-create-template.md)、[数式](./devtest-lab-manage-formulas.md)、または [Marketplace イメージ](./devtest-lab-configure-marketplace-images.md)のいずれかである*ベース*から、ラボで VM を作成します。

DevTest ラボ *アーティファクト*を使用すると、VM の作成時に実行される*アクション*を指定できます。

アーティファクトのアクションでは、Windows PowerShell スクリプトや Bash コマンドの実行、ソフトウェアのインストールなどの手順を実行することができます。

アーティファクト *パラメーター*を使用すると、特定のシナリオのアーティファクトをカスタマイズできます。

この記事では、アーティファクトを使用する VM をラボに作成する方法を説明します。

## アーティファクトを含む VM の追加

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。

1. **[参照]** をタップし、一覧の **[DevTest ラボ]** をタップします。

1. ラボの一覧で、新しい VM を作成するラボをタップします。

1. ラボのブレードで、次の図に示す **[+ ラボ VM]** をタップします。![[+ ラボ VM] ボタン](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. **[Choose a base]** (ベースの選択) ブレードで、VM のベースを選択します。

1. **[ラボ VM]** ブレードで、**[ラボ VM 名]** ボックスに新しい仮想マシンの名前を入力します。

	![[ラボ VM] ブレード](./media/devtest-lab-add-vm-with-artifacts/devtestlab-lab-vm-blade.png)

1. **[ユーザー名]** に、仮想マシンの管理者特権を付与するユーザー名を入力します。

1. 選択したベースの OS の種類が Linux の場合は、*[パスワード]* または *[SSH 公開キー]* のいずれかの認証の種類を指定します。

1. 指定した認証の種類に応じて、パスワードまたは SSH 公開キーを入力します。

1. **[VM サイズ]** をタップし、作成する VM のプロセッサ コア、RAM サイズ、ハード ドライブ サイズを指定する定義済みの項目のいずれかを選択します。

1. **[仮想ネットワーク]** をタップして、目的の仮想ネットワークを選択します。

1. **[サブネット]** をタップして、サブネットを選択します。

1. ラボのポリシーが選択したサブネットでパブリック IP アドレスを許可するように設定されている場合は、**[はい]** または **[いいえ]** を選択して IP アドレスをパブリックにするかどうかを指定します。それ以外の場合、このオプションは無効になっており、**[いいえ]** が選択されています。

1. **[アーティファクト]** をタップし、アーティファクトの一覧から基本イメージに追加するアーティファクトを選択して構成します。**注:** DevTest ラボやアーティファクトの構成に関する経験がない場合は、「[既存のアーティファクトの VM への追加](#add-an-existing-artifact-to-a-vm)」セクションに進み、終了してからここに戻ってください。

1. ARM テンプレートを表示またはコピーする場合は、「[ARM テンプレートの保存](#save-arm-template)」セクションに進み、終了してからここに戻ってください。

1. **[作成]** をタップして、指定した VM をラボに追加します。

1. ラボのブレードには VM の作成状況が表示されます。最初は **[作成中]**、VM が起動した後は **[実行中]** と表示されます。

1. 「[次のステップ](#next-steps)」セクションに移動します。

## 既存のアーティファクトの VM への追加

VM を作成するときに、既存のアーティファクトを追加できます。各ラボには、パブリック DevTest ラボ アーティファクト リポジトリのアーティファクトと、独自のアーティファクト リポジトリに作成または追加されたアーティファクトが含まれます。アーティファクトの作成方法については、「[DevTest ラボ VM のカスタム アーティファクトの作成](devtest-lab-artifact-author.md)」の記事を参照してください。

1. **[ラボ VM]** ブレードで、**[アーティファクト]** をタップします。 

1. **[アーティファクトの追加]** ブレードで、目的のアーティファクトをタップします。

![Add Artifacts blade](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

1. 必須のパラメーター値を入力します。必要に応じて、省略可能なパラメーターも入力します。  

1. **[追加]** をタップしてアーティファクトを追加し、**[アーティファクトの追加]** ブレードに戻ります。

1. VM に必要なアーティファクトを引き続き追加します。

1. アーティファクトを追加したら、[アーティファクトの実行順を変更する](#change-the-order-in-which-artifacts-are-run)ことができます。[アーティファクトの表示または変更](#view-or-modify-an-artifact)に戻ることもできます。

## アーティファクトの実行順序の変更

既定では、アーティファクトのアクションは VM に追加された順序で実行されます。次の手順では、アーティファクトの実行順序を変更する方法を示しています。

1. **[アーティファクトの追加]** ブレードの上部で、VM に追加されたアーティファクトの数を示すリンクをタップします。

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. 一覧のアーティファクトをドラッグ アンド ドロップして、順序を変更します。**注:** アーティファクトをドラッグできない場合は、アーティファクトの左側からドラッグしていることを確認してください。

1. 完了したら、**[OK]** をタップします。

## アーティファクトの表示または変更

次の手順では、アーティファクトのパラメーターを表示または変更する方法を示しています。

1. **[アーティファクトの追加]** ブレードの上部で、VM に追加されたアーティファクトの数を示すリンクをタップします。

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. **[選択されたアーティファクト]** ブレードで、表示または編集するアーティファクトをタップします。

1. **[アーティファクトの追加]** ブレードで、必要な変更を行ったら **[OK]** をタップして **[アーティファクトの追加]** ブレードを閉じます。

1. **[OK]** をタップして、**[選択されたアーティファクト]** ブレードを閉じます。

## ARM テンプレートの保存

ARM テンプレートでは、反復可能なデプロイを定義する宣言的な方法が提供されます。次の手順では、作成される VM の ARM テンプレートを保存する方法について説明します。保存した ARM テンプレートで、[Azure PowerShell を使用して新しい VM をデプロイする](../resource-group-overview/#template-deployment)ことができます。

1. **[ラボ VM]** ブレードで、**[ARM テンプレートの表示]** をタップします。

1. **[Azure Resource Manager テンプレートの表示]** ブレードで、すべてのテンプレート テキストを選択します。

1. 選択したテキストをクリップボードにコピーします。

1. **[OK]** をタップして、**[Azure Resource Manager テンプレートの表示]** ブレードを閉じます。

1. テキスト エディターを開きます。

1. クリップボードからテンプレート テキストを貼り付けます。

1. 後で使用するためにファイルを保存します。

## 次のステップ

- VM が作成されたら、VM のブレードで **[接続]** をタップして VM に接続できます。
- [DevTest ラボ VM のカスタム アーティファクトの作成](devtest-lab-artifact-author.md)方法を学習します。
- [DevTest ラボ ARM のクイックスタート テンプレート ギャラリー](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)を調べます。

<!---HONumber=AcomDC_0518_2016-->