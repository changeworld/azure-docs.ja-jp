<properties
	pageTitle="アーティファクトを含む VM の DevTest ラボへの追加 | Microsoft Azure"
	description="アーティファクトを含む VM を DevTest ラボに追加する方法について説明します。"
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
	ms.date="03/13/2016"
	ms.author="tarcher"/>

# アーティファクトを含む VM の DevTest ラボへの追加

> [AZURE.NOTE] この記事の付属のビデオを表示するには、次のリンクをクリックします。[How to create VMs with artifacts in a DevTest Lab](/documentation/videos/how-to-create-vms-with-artifacts-in-a-devtest-lab) (DevTest ラボでアーティファクトを含む VM を作成する方法)

## 概要

[カスタム イメージ](./devtest-lab-create-template.md)または Marketplace イメージのいずれかである基本イメージから、DevTest ラボで VM を作成します。

DevTest ラボ*アーティファクト*を使用すると、VM の作成時に実行される*アクション*を指定できます。

アーティファクトのアクションでは、Windows Powershell スクリプトや Bash コマンドの実行、ソフトウェアのインストールなどの手順を実行することができます。

アーティファクト *パラメーター*を使用すると、特定のシナリオのアーティファクトをカスタマイズできます。

この記事では、アーティファクトを使用する VM をラボに作成する方法を説明します。

## アーティファクトを含む VM の追加

1. [Azure ポータル](https://portal.azure.com)にサインインします。

1. **[参照]** をタップし、一覧の **[DevTest ラボ]** をタップします。

1. ラボの一覧で、新しい VM を作成するラボをタップします。

1. ラボのブレードで、次の図に示す **[+ ラボ VM]** をタップします。![DevTest lab home blade](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. **[ラボ VM]** ブレードで、**[ラボ VM 名]** ボックスに新しい仮想マシンの名前を入力します。

1. **[基本 / 必要な設定の構成]** をタップし、VM の基本イメージを選択します。

    ![Lab VM settings](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-1.png)

1. 基本イメージを選択して **[OK]** をタップすると、**[ラボ VM]** ブレードが展開して、 **[ユーザー名]**、**[認証の種類]** (選択したベースの OS の種類が Linux の場合)、および **[パスワード]** (認証の種類が*パスワード*である場合) を含むユーザー アカウント情報を指定するための UI 要素が含まれます。

    ![Expanded Lab VM blade](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-2.png)

1. **[ユーザー名]** に、仮想マシンの管理者特権を付与するユーザー名を入力します。

1. 選択したベースの OS の種類が Linux の場合は、*[パスワード]* または *[SSH 公開キー]* のいずれかの認証の種類を指定します。

1. 指定した認証の種類に応じて、パスワードまたは SSH 公開キーを入力します。

1. **[VM サイズ]** をタップし、作成する VM のプロセッサ コア、RAM サイズ、ハード ドライブ サイズを指定する定義済みの項目のいずれかを選択します。

1. **[仮想ネットワーク]** をタップして、目的の VNET を選択します。

1. **[サブネット]** をタップして、サブネットを選択します。

1. ラボのポリシーが選択したサブネットでパブリック IP アドレスを許可するように設定されている場合は、**[はい]** または **[いいえ]** を選択して IP アドレスをパブリックにするかどうかを指定します。パブリック IP アドレスを許可するように設定されていない場合、このオプションは無効であり、**[いいえ]** として選択されます。

1. **[アーティファクト]** をタップし、アーティファクトの一覧から基本イメージに追加するアーティファクトを選択して構成します。**注:** DevTest ラボやアーティファクトの構成に関する経験がない場合は、「[既存のアーティファクトの VM への追加](#add-an-existing-artifact-to-a-vm)」セクションに進み、終了してからここに戻ってください。

1. **[作成]** をタップして、指定した VM をラボに追加します。

1. ラボのブレードには VM の作成状況が表示されます。最初は **[作成中]**、VM が起動した後は **[実行中]** と表示されます。

1. 「[次のステップ](#next-steps)」セクションに移動します。

## 既存のアーティファクトの VM への追加

VM を作成するときに、既存のアーティファクトを追加できます。各ラボには、パブリック DevTest ラボ アーティファクト リポジトリのアーティファクトと、独自のアーティファクト リポジトリに作成または追加されたアーティファクトが含まれます。アーティファクトの作成方法については、「[Learn how to author your own artifacts for use with DevTest Labs](devtest-lab-artifact-author.md)」 (DevTest ラボで使用するために独自のアーティファクトを作成する方法) の記事を参照してください。

1. **[ラボ VM]** ブレードで、**[アーティファクト]** をタップします。 

1. **[アーティファクトの追加]** ブレードで、目的のアーティファクトをタップします。

![Add Artifacts blade](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

1. 必須のパラメーター値を入力します。必要に応じて、省略可能なパラメーターも入力します。  

1. **[追加]** をタップしてアーティファクトを追加し、**[アーティファクトの追加]** ブレードに戻ります。

1. VM に必要なアーティファクトを引き続き追加します。

1. アーティファクトを追加すると、[アーティファクトの実行順序の変更](#change-the-order-in-which-artifacts-are-run)を行うことができます。[アーティファクトの表示または変更](#view-or-modify-an-artifact)に戻ることもできます。

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

## 次のステップ

- VM が作成されたら、VM のブレードで **[接続]** をタップして VM に接続できます。
- アーティファクトの作成方法については、「[Learn how to author your own artifacts for use with DevTest Labs](devtest-lab-artifact-author.md)」 (DevTest ラボで使用するために独自のアーティファクトを作成する方法) の記事を参照してください。

<!---HONumber=AcomDC_0316_2016-->