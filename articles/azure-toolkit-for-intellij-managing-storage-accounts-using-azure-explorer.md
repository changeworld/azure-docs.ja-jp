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
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: a1b56cc2751fc43a1ad6917eca77eec460f26694
ms.contentlocale: ja-jp
ms.lasthandoff: 05/15/2017


---

# <a name="manage-storage-accounts-by-using-the-azure-explorer-for-intellij"></a>Azure Explorer for IntelliJ を使用してストレージ アカウントを管理する

Azure Toolkit for IntelliJ の一部である Azure Explorer は、IntelliJ 統合開発環境 (IDE) 内から Azure アカウントのストレージ アカウントを管理するための使いやすいソリューションを Java 開発者に提供します。

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-intellij-show-azure-explorer](../includes/azure-toolkit-for-intellij-show-azure-explorer.md)]

## <a name="create-a-storage-account-in-intellij"></a>IntelliJ でストレージ アカウントを作成する

Azure Explorer を使用してストレージ アカウントを作成するには、以下の手順を実行します。

1. 「[Azure Toolkit for Eclipse のサインイン手順]」に従って Azure アカウントにサインインします。 

2. **Azure Explorer** ビューで、**[Azure]** ノードを展開し、**[ストレージ アカウント]** を右クリックし、**[ストレージ アカウントの作成]** をクリックします。

   ![[ストレージ アカウントの作成] コマンド][CS01]

3. **[ストレージ アカウントの作成]** ダイアログ ボックスで、次のオプションを指定します。

   ![[新しいストレージ アカウントの作成] ダイアログ ボックス][CS02]

   * **[名前]**: 新しいストレージ アカウントの名前を指定します。

   * **[アカウントの種類]**: 作成するストレージ アカウントの種類 ("Blob Storage" など) を指定します。 詳細については、「[Azure ストレージ アカウントについて]」を参照してください。 

   * **[パフォーマンス]**: 選択した発行元の、ストレージ アカウントで使用するプランを指定します ("Premium" など)。 詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット]」を参照してください。 

   * **[レプリケーション]**: ストレージ アカウントのレプリケーションを指定します ("ゾーン冗長" など)。 詳細については、「[Azure Storage のレプリケーション]」を参照してください。 

   * **[サブスクリプション]**: 新しいストレージ アカウントに使用する Azure サブスクリプションを指定します。

   * **[場所]**: ストレージ アカウントを作成する場所を指定します ("米国西部" など)。

   * **[リソース グループ]**: 仮想マシン用のリソース グループを指定します。 次のいずれかのオプションを選択します。
      * **[新規作成]**: 新しいリソース グループを作成することを指定します。
      * **[Use Existing\(既存の使用\)]**: Azure アカウントに関連付けられているリソース グループの一覧から選択することを指定します。

4. 上記のオプションをすべて指定したら、**[OK]** をクリックします。

## <a name="create-a-storage-container-in-intellij"></a>IntelliJ にストレージ コンテナーを作成する

Azure Explorer を使用してストレージ コンテナーを作成するには、以下の手順を実行します。

1. Azure Explorer ビューで、コンテナーを作成するストレージ アカウントを右クリックし、**[BLOB コンテナーの作成]** をクリックします。

   ![[BLOB コンテナーの作成] コマンド][CC01]

2. **[BLOB コンテナーの作成]** ダイアログ ボックスで、コンテナーの名前を指定し、**[OK]** をクリックします。 ストレージ コンテナーの名前付けについて詳しくは、「[コンテナー、BLOB、メタデータの名前付けと参照]」をご覧ください。

   ![[BLOB コンテナーの作成] ダイアログ ボックス][CC02]

## <a name="delete-a-storage-container-in-intellij"></a>IntelliJ でストレージ コンテナーを削除する

Azure Explorer を使用してストレージ コンテナーを削除するには、以下の手順を実行します。

1. Azure Explorer ビューで、ストレージ コンテナーを右クリックし、**[削除]** をクリックします。

   ![[ストレージ コンテナーの削除] コマンド][DC01]

2. 確認ウィンドウで **[はい]** をクリックします。

   ![ストレージ コンテナー削除確認ウィンドウ][DC02]

## <a name="delete-a-storage-account-in-intellij"></a>IntelliJ でストレージ アカウントを削除する

Azure Explorer を使用してストレージ アカウントを削除するには、以下の手順を実行します。

1. **Azure Explorer** ビューで、ストレージ アカウントを右クリックし、**[削除]** を選択します。

   ![ストレージ アカウントの [削除] メニュー][DS01]

2. 確認ウィンドウで **[はい]** をクリックします。

   ![ストレージ アカウント削除確認ウィンドウ][DS02]

## <a name="next-steps"></a>次のステップ
Azure ストレージ アカウント、サイズ、および料金について詳しくは、次のリソースをご覧ください。

* [Microsoft Azure Storage の概要]
* [Azure ストレージ アカウントについて]
* Azure ストレージ アカウントのサイズ
  * [Azure の Windows ストレージ アカウントのサイズ]
  * [Azure の Linux ストレージ アカウントのサイズ]
* Azure ストレージ アカウントの料金
  * [Windows ストレージ アカウントの料金]
  * [Linux ストレージ アカウントの料金]

Azure Toolkits for Java IDE の詳細については、以下のリソースを参照してください。

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

Java での Azure の使用の詳細については、[Azure Java デベロッパー センター]と [Java Tools for Visual Studio Team Services] を参照してください。

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
[コンテナー、BLOB、メタデータの名前付けと参照]: http://go.microsoft.com/fwlink/?LinkId=255555

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

