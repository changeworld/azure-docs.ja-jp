---
title: "Azure Explorer for IntelliJ を使用してストレージ アカウントを管理する | Microsoft Docs"
description: "Azure Explorer for IntelliJ を使用して Azure ストレージ アカウントを管理する方法について説明します。"
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
ms.openlocfilehash: 22930bf1d37d7b6039bd02792cdd70ec321d6fa7
ms.lasthandoff: 04/22/2017


---

# <a name="managing-storage-accounts-using-the-azure-explorer-for-intellij"></a>Azure Explorer for IntelliJ を使用してストレージ アカウントを管理する

Azure Toolkit for IntelliJ の一部である Azure Explorer は、IntelliJ IDE 内から Azure アカウントのストレージ アカウントを管理するための使いやすいソリューションを Java 開発者に提供します。

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-intellij-show-azure-explorer](../includes/azure-toolkit-for-intellij-show-azure-explorer.md)]

## <a name="creating-a-storage-account-in-intellij"></a>IntelliJ でストレージ アカウントを作成する

次の手順は、Azure Explorer を使用してストレージ アカウントを作成する方法を説明しています。

1. 「[Azure Toolkit for Eclipse のサインイン手順]」の記事の手順を使用して、Azure アカウントにサインインします。

1. **Azure Explorer** ツール ウィンドウで、**[Azure]** ノードを展開し、**[ストレージ アカウント]** を右クリックして **[ストレージ アカウントの作成]** をクリックします。
   ![[ストレージ アカウントの作成] メニュー][CS01]

1. **[ストレージ アカウントの作成]** ダイアログ ボックスが表示されたら、以下のオプションを指定します。![[新しいストレージ アカウントの作成] ダイアログ ボックス][CS02]

   a. **[名前]**: 新しいストレージ アカウントの名前を指定します。

   b. **[アカウントの種類]**: 作成するストレージ アカウントの種類 ("Blob storage" など) を指定します。 (詳しくは、「[Azure ストレージ アカウントについて]」をご覧ください。)

   c. **[パフォーマンス]**: 選択した発行元の、ストレージ アカウントで使用するプランを指定します ("Premium" など)。 (詳しくは、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット]」をご覧ください。)

   d. **[レプリケーション]**: ストレージ アカウントのレプリケーションを指定します ("ゾーン冗長" など)。 (詳しくは、「[Azure Storage のレプリケーション]」をご覧ください。)

   e. **[サブスクリプション]**: 新しいストレージ アカウントに使用する Azure サブスクリプションを指定します。

   f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[場所]**: ストレージ アカウントを作成する場所 ("米国西部" など) を指定します。

   g. **[リソース グループ]**: 仮想マシンのリソース グループを指定します。以下のオプションのいずれかを選ぶ必要があります。
      * **[新規作成]**: 新しいリソース グループを作成することを指定します。
      * **[Use Existing (既存の使用)]**: Azure アカウントに関連付けられているリソース グループの一覧から選ぶことを指定します。

1. 上記のオプションをすべて指定したら、**[OK]** をクリックします。

## <a name="creating-a-storage-container-in-intellij"></a>IntelliJ でストレージ コンテナーを作成する

次の手順は、Azure Explorer を使用してストレージ コンテナーを作成する方法を説明しています。

1. Azure Explorer で、コンテナーを作成するストレージ アカウントを右クリックし、**[BLOB コンテナーの作成]** をクリックします。
   ![ストレージ コンテナーの作成メニュー][CC01]

1. **[BLOB コンテナーの作成]** ダイアログ ボックスが表示されたら、コンテナーの名前を指定し、**[OK]** をクリックします。 (ストレージ コンテナーの名前付けについて詳しくは、「[コンテナー、BLOB、およびメタデータの名前付けおよび参照]」をご覧ください。)![ストレージ コンテナーの作成ダイアログ ボックス][CC02]

## <a name="deleting-a-storage-container-in-intellij"></a>IntelliJ でストレージ コンテナーを削除する

Azure Explorer を使用してストレージ コンテナーを削除するには、次の手順を使用します。

1. Azure Explorer で、ストレージ コンテナーを右クリックし、**[削除]** をクリックします。
   ![ストレージ コンテナーの削除メニュー][DC01]

1. ストレージ コンテナーの削除を求めるメッセージが表示されたら、**[はい]** をクリックします。
   ![ストレージ コンテナーの削除ダイアログ ボックス][DC02]

## <a name="deleting-a-storage-account-in-intellij"></a>IntelliJ でストレージ アカウントを削除する

Azure Explorer を使用してストレージ アカウントを削除するには、次の手順を使用します。

1. **Azure Explorer** ツール ウィンドウで、ストレージ アカウントを右クリックし、**[削除]** を選択します。
   ![ストレージ アカウントの削除メニュー][DS01]

1. ストレージ アカウントの削除を求めるメッセージが表示されたら、**[はい]** をクリックします。
   ![ストレージ アカウントの削除ダイアログ ボックス][DS02]

## <a name="see-also"></a>関連項目
Azure ストレージ アカウント、サイズ、料金について詳しくは、次のリンクをご覧ください。

* [Microsoft Azure Storage の概要]
* [Azure ストレージ アカウントについて]
* Azure ストレージ アカウントのサイズ
   * [Azure の Windows ストレージ アカウントのサイズ]
   * [Azure の Linux ストレージ アカウントのサイズ]
* Azure ストレージ アカウントの料金
   * [Windows ストレージ アカウントの料金]
   * [Linux ストレージ アカウントの料金]

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

[Microsoft Azure Storage の概要]: /azure/storage/storage-introduction
[Azure ストレージ アカウントについて]: /azure/storage/storage-create-storage-account
[Azure Storage のレプリケーション]: /azure/storage/storage-redundancy
[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット]: /azure/storage/storage-scalability-targets
[コンテナー、BLOB、およびメタデータの名前付けおよび参照]: http://go.microsoft.com/fwlink/?LinkId=255555

[Azure の Windows ストレージ アカウントのサイズ]: /azure/virtual-machines/virtual-machines-windows-sizes
[Azure の Linux ストレージ アカウントのサイズ]: /azure/virtual-machines/virtual-machines-linux-sizes
[Windows ストレージ アカウントの料金]: /pricing/details/virtual-machines/windows/
[Linux ストレージ アカウントの料金]: /pricing/details/virtual-machines/linux/

<!-- IMG List -->

[CS01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CS01.png
[CS02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CS02.png
[CC01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CC01.png
[CC02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CC02.png

[DS01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DS01.png
[DS02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DS02.png
[DC01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DC01.png
[DC02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DC02.png

