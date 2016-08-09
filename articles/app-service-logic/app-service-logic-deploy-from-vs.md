<properties 
	pageTitle="Visual Studio でのロジック アプリのビルド | Microsoft Azure" 
	description="Visual Studio のプロジェクトを作成することによって、ロジック アプリを作成してデプロイします。" 
	authors="jeffhollan" 
	manager="erikre" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/26/2016"
	ms.author="jehollan"/>
	
# Visual Studio でのロジック アプリのビルドとデプロイ

[Azure ポータル](https://portal.azure.com/)にはロジック アプリを設計して管理するための強力な機能が用意されていますが、ロジック アプリの設計とデプロイを Visual Studio から行うこともできます。Logic Apps には、Visual Studio の豊富なツール群が付属しており、デザイナーを使ってロジック アプリを作成し、デプロイとオートメーション テンプレートを構成して、あらゆる環境にデプロイすることができます。

## インストール手順

以降、Logic Apps 用 Visual Studio ツールをインストールして構成する手順について説明します。

### 前提条件

- [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- [最新の Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 以上)
- Web アクセス (組み込みデザイナー使用時)

### Logic Apps 用 Visual Studio ツールのインストール

前提条件のインストールが済んだら、次の手順を実行します。

1. Visual Studio 2015 を起動して、**[ツール]** メニューの **[拡張機能と更新プログラム]** をクリックします。
1. オンラインで検索するために、**[オンライン]** カテゴリを選択します。
1. **Logic Apps** を検索して **[Azure Logic Apps Tools for Visual Studio]** を表示します。
1. **[ダウンロード]** ボタンをクリックして拡張機能をダウンロードし、インストールします。
1. インストール後、Visual Studio を再起動します。

> [AZURE.NOTE] 拡張機能は、[こちらのリンク](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)から直接ダウンロードすることもできます。

インストール後、ロジック アプリ デザイナーで [Azure リソース グループ] プロジェクトを使用できる状態になります。

## プロジェクトの作成

1. **[ファイル]** メニューの **[新規作成]**、**[プロジェクト]** の順にクリックします (または、**[追加]** の **[新しいプロジェクト]** をクリックして既存のソリューションに追加します)。![[ファイル] メニュー](./media/app-service-logic-deploy-from-vs/filemenu.png)

1. ダイアログ ボックスで、**[クラウド]** を見つけて、**[Azure リソース グループ]** を選択します。**[名前]** に名前を入力し、**[OK]** をクリックします。![新しいプロジェクトの追加](./media/app-service-logic-deploy-from-vs/addnewproject.png)

1. **[ロジック アプリ]** テンプレートを選択します。ロジック アプリのひな形となる空のデプロイ テンプレートが作成されます。 ![Azure テンプレートの選択](./media/app-service-logic-deploy-from-vs/selectazuretemplate.png)

1. **テンプレート**を選択したら、**[OK]** をクリックします。

	これで、ロジック アプリ プロジェクトがソリューションに追加されます。ソリューション エクスプローラーにデプロイ ファイルが表示されます。

	![デプロイ](./media/app-service-logic-deploy-from-vs/deployment.png)

## ロジック アプリ デザイナーの使用

ロジック アプリを含んだ Azure リソース グループ プロジェクトが整ったら、ワークフローの作成を支援する Visual Studio 内のデザイナーを開くことができます。利用可能なプロパティとデータをデザイナーがコネクタに問い合わせるためには、インターネット接続が必要となります (たとえば、Dynamics CRM Online コネクタを使用している場合、デザイナーは、使用できるカスタム プロパティと既定のプロパティをリストするよう CRM のインスタンスに問い合わせを行います)。

1. `<template>.json` ファイルを右クリックし、**[Open with Logic App Designer (ロジック アプリ デザイナーで開く)]** (または `Ctrl+L`) を選択します。
1. サブスクリプション、リソース グループ、デプロイ テンプレートの場所を選択します。
	- ロジック アプリを設計することによって、**API 接続**リソースが作成されることに注意してください。この接続リソースは、設計時にプロパティを問い合わせる目的で使用されます。ここで選択したリソース グループが、設計時にそれらの接続を作成するためのリソース グループになります。Azure ポータルに移動して参照画面で **[API 接続]** を選択すると、任意の API 接続を表示したり変更を加えたりすることができます。 ![Subscription Picker](./media/app-service-logic-deploy-from-vs/designer_picker.png)
1. `<template>.json` ファイル内の定義に基づいてデザイナーが表示されます。
1. これでロジック アプリを作成して設計できる状態になりました。変更はデプロイ テンプレートに反映されます。 ![Designer in Visual Studio](./media/app-service-logic-deploy-from-vs/designer_in_vs.png)

またリソース ファイルには、そのロジック アプリが機能するために必要なすべての接続の `Microsoft.Web/connections` リソースが追加されていることがわかります。これらの接続プロパティはデプロイ時に設定できるほか、デプロイ後は、Azure ポータルの **[API 接続]** で管理することができます。

### JSON コード ビューへの切り替え

デザイナーの一番下にある **[コード ビュー]** タブを選択すると、ロジック アプリの JSON 表示に切り替わります。再度フル リソースの JSON に切り替えるには、`<template>.json` ファイルを右クリックして **[開く]** を選択します。

### ロジック アプリの保存

ロジック アプリは、**[保存]** ボタンまたは `Ctrl+S` を使用して、いつでも保存することができます。保存時にロジック アプリにエラーが発生した場合は、Visual Studio の **[出力]** ウィンドウに表示されます。

## ロジック アプリのデプロイ

最後に、アプリを構成したら、いくつかの手順で Visual Studio から直接デプロイすることができます。

1. ソリューション エクスプローラーでプロジェクトを右クリックし、**[デプロイ]**、**[新しいデプロイ]** の順に移動します。 ![新しいデプロイ](./media/app-service-logic-deploy-from-vs/newdeployment.png)

2. Azure サブスクリプションへのサインインを求められます。

3. ここでは、ロジック アプリをデプロイするリソース グループの詳細を選択する必要があります。![リソース グループへのデプロイ](./media/app-service-logic-deploy-from-vs/deploytoresourcegroup.png)

     > [AZURE.NOTE]    リソース グループに適したテンプレートとパラメーター ファイルを必ず選択してください (たとえば、運用環境にデプロイする場合、運用中のパラメーター ファイルを選択します)。
4. [デプロイ] ボタンをクリックします
 
    
6. デプロイの状態が **[出力]** ウィンドウに表示されます (**[Azure のプロビジョニング]** の選択が必要になる場合があります)。 ![出力](./media/app-service-logic-deploy-from-vs/output.png)

今後は、ソース管理でロジック アプリを修正し、Visual Studio を使用して新しいバージョンをデプロイできます。

> [AZURE.NOTE] Azure ポータルで定義を直接変更した場合、次回 Visual Studio からデプロイすると、変更内容がオーバーライドされます。

## 次のステップ

- Logic Apps の使用を開始するには、[ロジック アプリの作成](app-service-logic-create-a-logic-app.md)に関するページのチュートリアルに従ってください。
- [一般的な例とシナリオを確認する](app-service-logic-examples-and-scenarios.md)
- [Logic Apps を使用してビジネス プロセスを自動化する](http://channel9.msdn.com/Events/Build/2016/T694)
- [Logic Apps を使用してシステムを統合する方法を説明する](http://channel9.msdn.com/Events/Build/2016/P462)

<!---HONumber=AcomDC_0727_2016-->