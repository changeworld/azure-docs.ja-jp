---
title: "Azure Explorer for Eclipse を使用して仮想マシンを管理する | Microsoft Docs"
description: "Azure Explorer for Eclipse を使用して Azure 仮想マシンを管理する方法について説明します。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: f8631c5fe566762862f64b47843e154b68bb8031
ms.lasthandoff: 04/22/2017


---

# <a name="managing-virtual-machines-using-the-azure-explorer-for-eclipse"></a>Azure Explorer for Eclipse を使用して仮想マシンを管理する

Azure Toolkit for Eclipse の一部である Azure Explorer は、Eclipse IDE 内から Azure アカウントの仮想マシンを管理するための使いやすいソリューションを Java 開発者に提供します。

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-eclipse-show-azure-explorer](../includes/azure-toolkit-for-eclipse-show-azure-explorer.md)]

## <a name="creating-a-virtual-machine-in-eclipse"></a>Eclipse で仮想マシンを作成する

次の手順は、Azure Explorer を使用して仮想マシンを作成する方法を説明しています。

1. 「[Azure Toolkit for Eclipse のサインイン手順]」の記事の手順を使用して、Azure アカウントにサインインします。

1. **Azure Explorer** ビューで、**[Azure]** ノードを展開し、**[仮想マシン]** を右クリックして **[VM の作成]** をクリックします。
   ![[VM の作成] メニュー][CR01]

1. **新しい仮想マシンの作成**ウィザードが表示されたら、サブスクリプションを選び、**[次へ]** をクリックします。
   ![新しい仮想マシンの作成ウィザード][CR02]

1. 次のウィザード画面で、以下のオプションを指定し、**[次へ]** をクリックします。 ![新しい仮想マシンの作成ウィザード][CR03]

   a. **[場所]**: 仮想マシンを作成する場所 ("米国西部" など) を指定します。

   b. **[発行者]**: 仮想マシンの作成に使用するイメージを作成した発行元を指定します ("Microsoft" など)。

   c. **[プラン]**: 選択した発行元の、仮想マシンで使用するプランを指定します ("JDK" など)。

   d. **[SKU]**: 選択したプランから、使用する*在庫保管単位 (SKU)* を指定します ("JDK_8" など)。

   e. **[Version # (バージョン #)]**: 選択した SKU から、使用するバージョンを指定します。

1. 次のウィザード画面で、以下のオプションを指定し、**[次へ]** をクリックします。![新しい仮想マシンの作成ウィザード][CR04]

   a. **[仮想マシン名]**: 新しい仮想マシンの名前を指定します。英字、数字、ハイフンのみ使用でき、先頭には英字を使用する必要があります。

   b. **[サイズ]**: 仮想マシンに割り当てるコアとメモリの数を指定します。

   c. **[ユーザー名]**: 仮想マシンを管理するために作成する管理者アカウントを指定します。

   d. **[パスワード]** と **[確認]**: 管理者アカウントのパスワードを指定します。

1. ウィザードの最後の画面で、以下のオプションを指定します。

   a. **[リソース グループ]**: 仮想マシンのリソース グループを指定します。以下のオプションのいずれかを選ぶ必要があります。
      * **[新規作成]**: 新しいリソース グループを作成することを指定します。
      * **[Use Existing (既存の使用)]**: Azure アカウントに関連付けられているリソース グループの一覧から選ぶことを指定します。

   b. **[ストレージ アカウント]**: 仮想マシンの格納に使用するストレージ アカウントを指定します。既存のストレージ アカウントを選ぶことも、新しいアカウントを作成することもできます。 **[&lt;&lt;新規作成&gt;&gt;]** を選択した場合は、次のダイアログ ボックスが表示されます。

      ![[新しいストレージ アカウントの作成] ダイアログ ボックス][CR05]

   c. **[仮想ネットワーク]** と **[サブネット]**: 仮想マシンが接続する仮想ネットワークとサブネットを指定します。仮想マシンに使用する既存のネットワークとサブネットを選ぶことも、新しいネットワークとサブネットを作成することもできます。 **[&lt;&lt;新規作成&gt;&gt;]** を選択した場合は、次のダイアログ ボックスが表示されます。<br/>

      ![[新しい仮想ネットワークの作成] ダイアログ ボックス][CR06]

   d. **[パブリック IP アドレス]**: 外部に接続する仮想マシンの IP アドレスを指定します。新しい IP アドレスの作成を選ぶことも、仮想マシンでパブリック IP アドレスを使用しない場合は **[(なし)]** を選択することもできます。

   e. **[ネットワーク セキュリティ グループ]**: 仮想マシンで使用される省略可能なネットワーク ファイアウォールを指定します。既存のファイアウォールを選ぶことも、仮想マシンでネットワーク ファイアウォールを使用しない場合は **[(なし)]** を選択することもできます。

   f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[可用性セット]**: 仮想マシンが属する可能性がある省略可能な可用性セットを指定します。既存の可用性セットを選ぶことも、仮想マシンが可用性セットに属さない場合は **[(なし)]** を選択することもできます。

1. 上記のオプションをすべて入力したら、**[完了]** をクリックします。![新しい仮想マシンの作成ウィザード][CR07]

1. 上記の手順を完了すると、新しい仮想マシンが Azure Explorer のツール ウィンドウに表示されます。
   ![新しい仮想マシン][CR08]

## <a name="restarting-a-virtual-machine-in-eclipse"></a>Eclipse で仮想マシンを再起動する

Eclipse で Azure Explorer を使用して仮想マシンを再起動するには、次の手順を使用します。

1. **Azure Explorer** ビューで、仮想マシンを右クリックし、**[再起動]** を選択します。
   ![仮想マシンを再起動する][RE01]

1. メッセージが表示されたら、**[はい]** をクリックして、仮想マシンを再起動します。
   ![仮想マシンを再起動する][RE02]

## <a name="shutting-down-a-virtual-machine-in-eclipse"></a>Eclipse で仮想マシンをシャットダウンする

Eclipse でAzure Explorer を使用して実行中の仮想マシンをシャットダウンするには、次の手順を使用します。

1. **Azure Explorer** ビューで、仮想マシンを右クリックし、**[シャットダウン]** を選択します。
   ![仮想マシンをシャットダウンする][SH01]

1. メッセージが表示されたら、**[はい]** をクリックして、仮想マシンをシャットダウンします。
   ![仮想マシンをシャットダウンする][SH02]

## <a name="deleting-a-virtual-machine-in-eclipse"></a>Eclipse で仮想マシンを削除する

Eclipse で Azure Explorer を使用して仮想マシンを削除するには、次の手順を使用します。

1. **Azure Explorer** ビューで、仮想マシンを右クリックし、**[削除]** を選択します。
   ![仮想マシンを削除する][DE01]

1. メッセージが表示されたら、**[はい]** をクリックして、仮想マシンを削除します。
   ![仮想マシンを削除する][DE02]

## <a name="see-also"></a>関連項目
Azure 仮想マシンのサイズと料金について詳しくは、次のリンクをご覧ください。

* Azure 仮想マシンのサイズ
  * [Azure の Windows 仮想マシンのサイズ]
  * [Azure の Linux 仮想マシンのサイズ]
* Azure 仮想マシンの料金
  * [Windows 仮想マシンの料金]
  * [Linux 仮想マシンの料金]

Azure Toolkits for Java IDE の詳細については、次のリンクをご覧ください。

* [Azure Toolkit for Eclipse]
  * [Azure Toolkit for Eclipse の新機能]
  * [Azure Toolkit for Eclipse のインストール]
  * [Azure Toolkit for Eclipse のサインイン手順]
  * [Eclipse で Azure 用の Hello World Web アプリを作成する]
* [Azure Toolkit for IntelliJ]
  * [Azure Toolkit for IntelliJ の新機能]
  * [Azure Toolkit for IntelliJ のインストール]
  * [Azure Toolkit for IntelliJ のサインイン手順]
  * [IntelliJ で Azure 用の Hello World Web アプリを作成する]

Java での Azure の使用の詳細については、 [Azure Java デベロッパー センター] と[Java Tools for Visual Studio Team Services] を参照してください。

<!-- URL List -->

[Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij.md
[Eclipse で Azure 用の Hello World Web アプリを作成する]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[IntelliJ で Azure 用の Hello World Web アプリを作成する]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Azure Toolkit for Eclipse のインストール]: ./azure-toolkit-for-eclipse-installation.md
[Azure Toolkit for IntelliJ のインストール]: ./azure-toolkit-for-intellij-installation.md
[Azure Toolkit for Eclipse のサインイン手順]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Azure Toolkit for IntelliJ のサインイン手順]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Azure Toolkit for Eclipse の新機能]: ./azure-toolkit-for-eclipse-whats-new.md
[Azure Toolkit for IntelliJ の新機能]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java デベロッパー センター]: https://azure.microsoft.com/develop/java/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/

[Azure の Windows 仮想マシンのサイズ]: /azure/virtual-machines/virtual-machines-windows-sizes
[Azure の Linux 仮想マシンのサイズ]: /azure/virtual-machines/virtual-machines-linux-sizes
[Windows 仮想マシンの料金]: /pricing/details/virtual-machines/windows/
[Linux 仮想マシンの料金]: /pricing/details/virtual-machines/linux/

<!-- IMG List -->

[RE01]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/RE01.png
[RE02]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/RE02.png

[SH01]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/SH01.png
[SH02]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/SH02.png

[DE01]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/DE01.png
[DE02]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/DE02.png

[CR01]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR01.png
[CR02]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR02.png
[CR03]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR03.png
[CR04]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR04.png
[CR05]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR05.png
[CR06]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR06.png
[CR07]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR07.png
[CR08]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR08.png

