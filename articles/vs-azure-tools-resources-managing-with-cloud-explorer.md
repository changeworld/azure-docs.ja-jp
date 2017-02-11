---
title: "クラウド エクスプローラーを使用した Azure リソースの管理 | Microsoft Docs"
description: "クラウド エクスプローラーを使用し、Visual Studio 内で Azure リソースを参照および管理する方法について説明します。"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 6347dc53-f497-49d5-b29b-e8b9f0e939d7
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 9fc8a0fbd49573913b42818bebae505464e8ff78


---
# <a name="managing-azure-resources-with-cloud-explorer"></a>クラウド エクスプローラーを使用した Azure リソースの管理
## <a name="overview"></a>Overview
クラウド エクスプローラーは、Visual Studio IDE 内で Azure リソースをより簡単かつ迅速に参照および管理できるように設計されています。 たとえば、クラウド エクスプローラーを使用して、[Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040)またはブラウザーで Web アプリを開いたり、デバッガーをアタッチしたりできます。また、BLOB コンテナーのプロパティを表示し、BLOB コンテナー エディターで開くことができます。

クラウド エクスプローラーは、 [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)と同様に、Azure Resource Manager スタックを基盤としています。 これは、リソース (Azure リソース グループなど) と Azure サービス (Logic Apps や API Apps など) を認識し、[ロールベースのアクセス制御](active-directory/role-based-access-control-configure.md) (RBAC) をサポートします。 追加または変更された Azure リソースを表示するには、クラウド エクスプローラーのツール バーの **[更新]** を選択します。

クラウド エクスプローラーは、Visual Studio Tools for Azure SDK 2.7 の一部としてインストールされます。

## <a name="prerequisites"></a>前提条件
* Visual Studio 2015 RTM。
* Visual Studio Tools for Azure SDK。
* クラウド エクスプローラーに Azure リソースを表示するには、Azure アカウントを所有していて、そのアカウントにログインしている必要もあります。 お持ちでない場合でも、数分でアカウントを作成できます。 MSDN サブスクリプションをお持ちの場合は、「 [MSDN サブスクライバー向けの Azure の特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)」を参照してください。 それ以外の場合は、 [無料試用版のアカウントの作成](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。
* クラウド エクスプローラーが表示されていない場合は、メニュー バーの **[表示]**、**[その他のウィンドウ]**、**[クラウド エクスプローラー]** の順にクリックすると表示できます。

## <a name="manage-azure-accounts-and-subscriptions"></a>Azure アカウントと Azure サブスクリプションの管理
クラウド エクスプローラーで Azure リソースを表示するには、1 つ以上の有効なサブスクリプションを持つ Azure アカウントにログインする必要があります。 複数の Azure アカウントがある場合は、クラウド エクスプローラーでそれらを追加し、クラウド エクスプローラーのリソース ビューに追加するサブスクリプションを選択します。

これまでに Azure を使用したことがない場合または必要なアカウントを Visual Studio に追加していない場合は、この操作を行うように求められます。

## <a name="to-add-azure-accounts-to-cloud-explorer"></a>Azure アカウントをクラウド エクスプローラーに追加するには
1. クラウド エクスプローラーのツール バーの [設定] アイコンを選択します。
2. **[アカウントの追加]** リンクを選択します。 参照するリソースを所有する Azure アカウントにログインします。 ここで追加したアカウントは、アカウントを選択するドロップダウン リストで選択する必要があります。 選択したアカウントのサブスクリプションが、アカウント項目の下に表示されます。
   
    ![Adding Azure subscriptions](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819514.png)
   
    ![Choosing Azure subscriptions](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819515.png)
3. 参照するアカウントのサブスクリプションのチェック ボックスをオンにし、 **[適用]** をクリックします。
   
    選択したサブスクリプションの Azure リソースがクラウド エクスプローラーに表示されます。

## <a name="to-remove-an-azure-account"></a>Azure アカウントを削除するには
1. メニュー バーの **[ファイル]**、**[アカウントの設定]** の順に選択します。
2. **[アカウントの設定]** ダイアログ ボックスの **[すべてのアカウント]** セクションで、削除するアカウントの横にある **[削除]** コマンドを選択します。 このコマンドは、Visual Studio からアカウントを削除するだけです。Azure アカウント自体には影響しません。

## <a name="view-resource-types-or-groups"></a>リソースの種類またはグループの表示
Azure リソースを表示するには、**[リソースの種類]** ビューまたは **[リソース グループ]** ビューを選択します。

![Resource view dropdown](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819516.png)

* **[リソースの種類]** ビューは、 [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)で使用される一般的なビューでもあり、Web アプリ、ストレージ アカウント、仮想マシンなどの Azure リソースが種類ごとに表示されます。 これは、サーバー エクスプローラーで Azure リソースが表示されるしくみに似ています。
* [リソース グループ] ビューでは、Azure リソースが、関連付けられている Azure リソース グループで分類されます。

    リソース グループとは Azure リソースの集まりで、通常は特定のアプリケーションで使用されます。 Azure リソース グループについて詳しくは、「[Azure リソース マネージャーの概要](./azure-resource-manager/resource-group-overview.md)」をご覧ください。

## <a name="view-and-navigate-resources"></a>リソースの表示および移動
クラウド エクスプローラーで Azure リソースに移動してその情報を表示するには、項目の種類または関連付けられているリソース グループを展開して、リソースを選択します。 リソースを選択すると、クラウド エクスプローラーの下部にある 2 つのタブに情報が表示されます。

![Choose a resource view](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819517.png)

* **[アクション]** タブには、選択したリソースに対してクラウド エクスプローラーで実行できるアクションが表示されます。 リソースのショートカット メニューには、使用可能なアクションも表示されます。
* **[プロパティ]** タブには、リソースのプロパティ (種類、ロケール、関連付けられているリソース グループなど) が表示されます。

すべてのリソースには、 **[ポータルで開く]**というアクションがあります。 このアクションを選択すると、選択したリソースが [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)で表示されます。 この機能は、深く入れ子になったリソースまで移動する場合に特に便利です。

その他のアクションやプロパティ値も、Azure リソースに基づいて表示される場合があります。 たとえば、Web Apps と Logic Apps には、**[ポータルで開く]** に加えて、**[ブラウザーで開く]** と **[デバッガーのアタッチ]** というアクションもあります。 エディターを開くアクションは、ストレージ アカウントの BLOB、キュー、またはテーブルを選択すると表示されます。 Azure アプリには **URL** と**状態**プロパティがあるのに対し、ストレージ リソースにはキーと接続文字列のプロパティがあります。

## <a name="search-resources"></a>リソースの検索
Azure アカウントのサブスクリプションで特定の名前のリソースを検索するには、クラウド エクスプローラーの検索ボックスに名前を入力します。

![Finding resources in Cloud Explorer](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC820394.png)

検索ボックスに文字を入力すると、その文字に一致するリソースのみがリソース ツリーに表示されます。




<!--HONumber=Nov16_HO3-->


