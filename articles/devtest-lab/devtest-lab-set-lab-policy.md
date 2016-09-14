<properties
	pageTitle="ラボのポリシーの定義 | Microsoft Azure"
	description="VM サイズ、ユーザーごとの VM の最大数、シャットダウンの自動化など、ラボのポリシーを定義する方法について説明します。"
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
	ms.date="08/25/2016"
	ms.author="tarcher"/>

# ラボのポリシーの定義

> [AZURE.VIDEO how-to-set-vm-policies-in-a-devtest-lab]

## Overview

DevTest ラボでは、ラボとその VM を使用する方法を制御する主要ポリシーを指定できます。たとえば、VM を作成するうえで指定できるサイズや、作成できる数のしきい値についてルールを設定できるほか、ラボ VM を自動的に開始または停止するようにジョブをスケジュール設定することもできます。

## ラボのポリシーへのアクセス

ラボのポリシーを表示 (および変更) するには、次の手順に従います。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。

1. **[参照]** を選択し、一覧の **[DevTest Labs]** を選択します。

1. ラボの一覧で目的のラボを選択します。

1. **[設定]** を選択します。

	![Settings](./media/devtest-lab-set-lab-policy/lab-blade-settings.png)

1. **[設定]** ブレードには、**[VM ポリシー]** という設定グループがあります。

	![Settings](./media/devtest-lab-set-lab-policy/policies.png)

	一覧で目的のポリシーを選択すると、そのポリシーの設定の詳細を確認できます。

	- [許可される VM サイズ](#set-allowed-vm-sizes) - ラボで使用できる VM サイズの一覧を選択します。ユーザーは、この一覧からのみ VM を作成できます。

	- [ユーザーごとの VM の最大数](#set-maximum-vms-per-user) - ユーザーが作成できる VM の最大数を指定します。

	- [許可される合計 VM 数](#set-total-vms-allowed) - ラボに作成できる VM の最大数を指定します。

	- [自動シャットダウン](#set-auto-shutdown) - 現在のラボの VM を自動的にシャットダウンする時刻を指定します。

	- [自動開始](#set-auto-start) - 現在のラボの VM を自動的に開始する時刻を指定します。

## 許可される VM サイズの設定

許可される VM サイズを設定するポリシーでは、ラボで使用可能な VM サイズを指定できるので、ラボの無駄を最小限に抑えるのに役立ちます。このポリシーを有効にすると、この一覧の VM サイズだけを使用して VM を作成できます。

1. ラボの **[設定]** ブレードで、**[VM Policies (VM ポリシー)]** の **[Allowed VM Sizes (許可される VM サイズ)]** を選択します。

	![Settings](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)
 
1. このポリシーを有効にするには **[オン]** を選択し、無効にするには **[オフ]** を選択します。

1. このポリシーを有効にする場合は、ラボで作成できる 1 つ以上の VM サイズを選択します。

1. [**保存**] を選択します。

## ユーザーごとの VM の最大数の設定

**ユーザーごとの VM の最大数**のポリシーでは、各ユーザーが作成できる VM の最大数を指定できます。ユーザーの上限に達している場合、ユーザーが新しい VM を作成しようとすると、VM を作成できないことを示すエラー メッセージが表示されます。

1. ラボの **[設定]** ブレードで、**[VM Policies (VM ポリシー)]** の **[Maximum VMs per user (ユーザーごとの VM の最大数)]** を選択します。

	![Settings](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. このポリシーを有効にするには **[オン]** を選択し、無効にするには **[オフ]** を選択します。

1. このポリシーを有効にする場合は、**[Maximum VMs allowed per User (ユーザーごとに許可される VM の最大数)]** ボックスに、ユーザーが作成できる VM の最大数を示す数値を入力します。無効な数値を入力すると、このフィールドに入力できる最大数が UI に表示されます。

1. [**保存**] を選択します。

## 許可される合計 VM 数の設定

**許可される合計 VM 数**のポリシーでは、現在のラボで作成できる VM の最大数を指定することができます。ラボの上限に達している場合、ユーザーが新しい VM を作成しようとすると、VM を作成できないことを示すエラー メッセージが表示されます。

1. ラボの **[設定]** ブレードで、**[VM Policies (VM ポリシー)]**の **[Total VMs allowed (許可される合計 VM 数)]** を選択します。

	![Settings](./media/devtest-lab-set-lab-policy/total-vms-allowed.png)

1. このポリシーを有効にするには **[オン]** を選択し、無効にするには **[オフ]** を選択します。

1. このポリシーを有効にする場合は、**[Total VMs allowed in this lab (このラボで許可される合計 VM 数)]** ボックスに、現在のラボで作成できる VM の最大数を示す数値を入力します。無効な数値を入力すると、このフィールドに入力できる最大数が UI に表示されます。

1. [**保存**] を選択します。

## 自動シャットダウンの設定

自動シャットダウン ポリシーでは、このラボの VM をシャットダウンする時刻を指定できるので、ラボの無駄を最小限に抑えるのに役立ちます。

1. ラボの **[設定]** ブレードで、**[VM Policies (VM ポリシー)]**の **[Auto shutdown (自動シャットダウン)]** を選択します。

	![Settings](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. このポリシーを有効にするには **[オン]** を選択し、無効にするには **[オフ]** を選択します。

1. このポリシーを有効にする場合は、現在のラボのすべての VM をシャットダウンするローカル時刻を指定します。

1. [**保存**] を選択します。

1. このポリシーを有効にすると、既定では現在のラボのすべての VM にこのポリシーが適用されます。この設定を特定の VM から削除するには、その VM のブレードを開いて **[Auto Shutdown (自動シャットダウン)]** 設定を変更します。

## 自動開始の設定

自動開始のポリシーでは、現在のラボにある VM をいつ開始するか指定することができます。

1. ラボの **[設定]** ブレードで、**[VM Policies (VM ポリシー)]**の **[Auto start (自動開始)]** を選択します。

	![Settings](./media/devtest-lab-set-lab-policy/auto-start.png)

1. このポリシーを有効にするには **[オン]** を選択し、無効にするには **[オフ]** を選択します。

1. このポリシーを有効にする場合は、ローカルにスケジュール設定した開始時間と、それを適用する曜日を指定します。

1. [**保存**] を選択します。

1. このポリシーを有効にしても、現在のラボの VM に自動的に適用されることはありません。この設定を特定の VM に適用するには、その VM のブレードを開いて **[Auto Start (自動開始)]** 設定を変更します。

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## 次のステップ

ラボに対して各種の VM ポリシー設定を定義および適用した後は、次に示すいくつかの操作を試してみてください。

- [コスト管理を構成する](./devtest-lab-configure-cost-management.md) - **月間推定コスト傾向**グラフを使用し、現在のカレンダー月の現時点までの推定コストと月末の予測コストを表示する方法を示します。
- [カスタム イメージを作成する](./devtest-lab-create-template.md) - VM を作成する場合は、ベースとしてカスタム イメージまたは Marketplace イメージを指定します。この記事では、VHD ファイルからカスタム イメージを作成する方法について説明します。
- [Marketplace イメージを構成する](./devtest-lab-configure-marketplace-images.md) - DevTest Labs では、Azure Marketplace イメージを基にした新しい VM を作成することができます。この記事では、ラボで新しい VM を作成する際に使用できるようにする Azure Marketplace イメージ (該当するものがある場合) を指定する方法について説明します。
- [ラボで VM を作成する](./devtest-lab-add-vm-with-artifacts.md) - 基本イメージ (カスタムまたは Marketplace) から新しい VM を作成する方法と、VM でアーティファクトを操作する方法について説明します。

<!---HONumber=AcomDC_0831_2016-->