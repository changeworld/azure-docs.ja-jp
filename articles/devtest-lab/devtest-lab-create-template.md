    <properties
	pageTitle="Create VM templates | Microsoft Azure"
	description="Learn how to create VM templates from VHD images"
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
	ms.date="11/01/2015"
	ms.author="tarcher"/>

# VM テンプレートの作成

## 概要

[ラボを作成](devtest-lab-create-lab.md)したら、VM テンプレートの一覧から [VM をそのラボに追加](devtest-lab-add-vm-with-artifacts.md)できます。この記事では、バーチャル ハード ディスク (VHD) イメージ ファイルをアップロードし、VM の作成に使用するテンプレートとして構成する方法について説明します。VHD イメージを使い慣れていない場合、VHD イメージの作成方法については、「[Windows Server VHD の作成と Azure へのアップロード](../virtual-machines-create-upload-vhd-windows-server.md)」をご覧ください。VHD イメージを作成するか、VHD イメージへのアクセス権を取得したら、この記事の手順に従って VHD イメージをアップロードし、イメージからテンプレートを作成します。

## VM テンプレートの作成

1. [Azure プレビュー ポータル](https://portal.azure.com)にサインインします。

1. **[参照]** をタップし、一覧の **[DevTest ラボ]** をタップします。

1. ラボの一覧で目的のラボをタップします。

1. ラボ ブレードで、**[設定]** をタップします。

    ![ラボの設定](./media/devtest-lab-create-template/lab-blade-settings.png)

1. ラボの **[設定]** ブレードで、**[テンプレート]** をタップします。

    ![[テンプレート] オプション](./media/devtest-lab-create-template/lab-blade-settings-templates.png)

1. **[テンプレート]** ブレードで、**[+ テンプレート]** をタップします。

    ![テンプレートの追加](./media/devtest-lab-create-template/add-template.png)

1. **[テンプレートの追加]** ブレードで次の操作を行います。

	1. テンプレートの名前を入力します。この名前は、新しい VM を作成するときにテンプレートの一覧に表示されます。

	1. テンプレートの説明を入力します。この説明は、新しい VM を作成するときにテンプレートの一覧に表示されます。

	1. **[イメージ]** をタップします。

	1. 目的のイメージが表示されておらず、イメージを追加する場合は、「[新しいテンプレート イメージの追加](#add-a-new-template-image)」に進み、イメージを追加してからここに戻ってください。

	1. 目的のイメージを選択します。

	1. **[OK]** をタップして、**[テンプレートの追加]** ブレードを閉じます。

1. **[OS 構成]** をタップします。

1. **[OS 構成]** タブで、**[Windows]** または **[Linux]** を選択します。

1. **[Windows]** を選択した場合は、チェック ボックスを使用して、コンピューターで *Sysprep* が実行されたかどうかを指定します。

1. コンピューターの **[ユーザー名]** を入力します。

1. コンピューターの **[パスワード]** を入力します。**注:** パスワードはクリア テキストで表示されます。

1. **[OK]** をタップして、**[OS 構成]** ブレードを閉じます。

1. **[場所]** を指定します。

1. **[OK]** をタップして、テンプレートを作成します。

##新しいテンプレート イメージの追加

新しいテンプレート イメージを追加するには、VHD イメージ ファイルにアクセスできる必要があります。

1. **[テンプレート イメージの追加]** ブレードで、**[PowerShell を使用してイメージをアップロードする]** をタップします。

    ![イメージのアップロード](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. 次のブレードには、VHD イメージ ファイルを Azure サブスクリプションにアップロードする PowerShell スクリプトの変更と実行の手順が表示されます。**注:** イメージ ファイルのサイズと接続速度によっては、このプロセスに時間がかかる可能性があります。

##次のステップ

VM を作成するときに使用する VM テンプレートを追加したら、次に [VM を DevTest ラボに追加](devtest-lab-add-vm-with-artifacts)します。

<!---HONumber=AcomDC_0128_2016-->