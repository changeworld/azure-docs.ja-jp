<properties 
   pageTitle="クラウド エクスプローラーを使用した Azure リソースの管理"
   description="クラウド エクスプローラーを使用し、Visual Studio 内で Azure リソースを参照および管理する方法について説明します。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/13/2015"
   ms.author="kempb" />

# クラウド エクスプローラーを使用した Azure リソースの管理

##概要

クラウド エクスプローラーは、Visual Studio IDE 内で Azure リソースをより簡単かつ迅速に参照および管理できるように設計されています。たとえば、これを使用すると、Azure ポータルまたはブラウザー内で Web アプリを開いたり、デバッガーをアタッチしたりできます。また、BLOB コンテナーのプロパティを表示して BLOB コンテナー エディターで開くことができます。

クラウド エクスプローラーは、Microsoft Azure プレビュー ポータルと同様に、Azure リソース マネージャー スタックに基づいて構築されています。これは、リソース (Azure リソース グループなど) および Azure サービス (Logic Apps や API Apps など) を認識し、[ロールベースのアクセス制御](../role-based-access-control-configure/) (RBAC) をサポートします。追加または変更された Azure リソースを表示するには、クラウド エクスプローラーのツール バーの **[更新]** を選択します。

クラウド エクスプローラーは、Visual Studio Tools for Azure SDK 2.7 の一部としてインストールされます。

## 前提条件

- Visual Studio 2015 RTM。

- Visual Studio Tools for Azure SDK。
- クラウド エクスプローラーに Azure リソースを表示するには、Azure アカウントを所有していて、そのアカウントにログインしている必要もあります。お持ちでない場合でも、数分でアカウントを作成できます。MSDN サブスクリプションをお持ちの場合は、「[MSDN サブスクライバー向けの Azure の特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)」を参照してください。それ以外の場合は、[無料試用版のアカウントの作成](http://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。

- クラウド エクスプローラーが表示されていない場合は、メニュー バーの **[表示]**、**[その他のウィンドウ]**、**[クラウド エクスプローラー]** の順にクリックすると表示できます。

## Azure アカウントと Azure サブスクリプションの管理

クラウド エクスプローラーで Azure リソースを表示するには、1 つ以上の有効なサブスクリプションを持つ Azure アカウントにログインする必要があります。複数の Azure アカウントがある場合は、クラウド エクスプローラーでそれらを追加し、クラウド エクスプローラーのリソース ビューに追加するサブスクリプションを選択します。

これまでに Azure を使用したことがない場合または必要なアカウントを Visual Studio に追加していない場合は、この操作を行うように求められます。

## Azure アカウントをクラウド エクスプローラーに追加するには

1. クラウド エクスプローラーのツール バーの [設定] アイコンを選択します。

1. **[アカウントの追加]** リンクを選択します。参照するリソースを所有する Azure アカウントにログインします。ここで追加したアカウントは、アカウントを選択するドロップダウン リストで選択する必要があります。選択したアカウントのサブスクリプションが、アカウント項目の下に表示されます。

    ![Adding Azure subscriptions](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819514.png)

    ![Choosing Azure subscriptions](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819515.png)

1. 参照するアカウントのサブスクリプションのチェック ボックスをオンにし、**[適用]** をクリックします。

    選択したサブスクリプションの Azure リソースがクラウド エクスプローラーに表示されます。

## Azure アカウントを削除するには

1. メニュー バーの **[ファイル]**、**[アカウントの設定]** の順に選択します。

1. **[アカウントの設定]** ダイアログ ボックスの **[すべてのアカウント]** セクションで、削除するアカウントの横にある **[削除]** コマンドを選択します。このコマンドは、Visual Studio からアカウントを削除するだけです。Azure アカウント自体には影響しません。

## リソースの種類またはグループの表示

Azure リソースを表示するには、**[リソースの種類]** ビューまたは **[リソース グループ]** ビューを選択します。

![Resource view dropdown](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819516.png)

- **[リソースの種類]** ビューは、Azure ポータルで使用される一般的なビューでもあり、Web アプリ、ストレージ アカウント、Virtual Machines などの Azure リソースが種類ごとに表示されます。これは、サーバー エクスプローラーで Azure リソースが表示されるしくみに似ています。

- [リソース グループ] ビューでは、Azure リソースが、関連付けられている Azure リソース グループで分類されます。

 
	リソース グループとは Azure リソースの集まりで、通常は特定のアプリケーションで使用されます。Azure リソース グループの詳細については、「[Azure リソース マネージャーの概要](https://azure.microsoft.com/documentation/articles/resource-group-overview/)」を参照してください。

## リソースの表示および移動

クラウド エクスプローラーで Azure リソースに移動してその情報を表示するには、項目の種類または関連付けられているリソース グループを展開して、リソースを選択します。リソースを選択すると、クラウド エクスプローラーの下部にある 2 つのタブに情報が表示されます。

![Choose a resource view](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819517.png)

- **[アクション]** タブには、選択したリソースに対してクラウド エクスプローラーで実行できるアクションが表示されます。リソースのショートカット メニューには、使用可能なアクションも表示されます。

- **[プロパティ]** タブには、リソースのプロパティ (種類、ロケール、関連付けられているリソース グループなど) が表示されます。

すべてのリソースには、**[ポータルで開く]** というアクションがあります。このアクションを選択すると、クラウド エクスプローラーでは、Azure ポータルで選択したリソースが表示されます。この機能は、深く入れ子になったリソースまで移動する場合に特に便利です。

その他のアクションやプロパティ値も、Azure リソースに基づいて表示される場合があります。たとえば、Web Apps と Logic Apps には、**[ポータルで開く]** に加えて、**[ブラウザーで開く]** と **[デバッガーのアタッチ]** というアクションもあります。エディターを開くアクションは、ストレージ アカウントの BLOB、キュー、またはテーブルを選択すると表示されます。Azure アプリには **URL** と**状態**プロパティがあるのに対し、ストレージ リソースにはキーと接続文字列のプロパティがあります。

## リソースの検索

Azure アカウントのサブスクリプションで特定の名前のリソースを検索するには、クラウド エクスプローラーの検索ボックスに名前を入力します。

![Finding resources in Cloud Explorer](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC820394.png)

検索ボックスに文字を入力すると、その文字に一致するリソースのみがリソース ツリーに表示されます。

<!---HONumber=Oct15_HO3-->