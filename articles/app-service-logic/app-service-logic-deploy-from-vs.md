---
title: "Visual Studio でのロジック アプリのビルド | Microsoft Docs"
description: "Visual Studio のプロジェクトを作成することによって、ロジック アプリを作成してデプロイします。"
author: jeffhollan
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e484e5ce-77e9-4fa9-bcbe-f851b4eb42a6
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 73a57310c07ed946480f60f6564d8619e54155a5


---
# <a name="build-and-deploy-logic-apps-in-visual-studio"></a>Visual Studio でのロジック アプリのビルドとデプロイ
[Azure ポータル](https://portal.azure.com/) にはロジック アプリを設計して管理するための強力な機能が用意されていますが、ロジック アプリの設計とデプロイを Visual Studio から行うこともできます。  Logic Apps には、Visual Studio の豊富なツール群が付属しており、デザイナーを使ってロジック アプリを作成し、デプロイとオートメーション テンプレートを構成して、あらゆる環境にデプロイすることができます。  

## <a name="installation-steps"></a>インストール手順
以降、Logic Apps 用 Visual Studio ツールをインストールして構成する手順について説明します。

### <a name="prerequisites"></a>前提条件
* [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [最新の Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 以上)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* Web アクセス (組み込みデザイナー使用時)

### <a name="install-visual-studio-tools-for-logic-apps"></a>Logic Apps 用 Visual Studio ツールのインストール
前提条件のインストールが済んだら、次の手順を実行します。 

1. Visual Studio 2015 を起動して、**[ツール]** メニューの **[拡張機能と更新プログラム]** をクリックします。
2. オンラインで検索するために、 **[オンライン]** カテゴリを選択します。
3. 「**Logic Apps**」を検索して **[Azure Logic Apps Tools for Visual Studio]** を表示します。
4. **[ダウンロード]** ボタンをクリックして拡張機能をダウンロードし、インストールします。
5. インストール後、Visual Studio を再起動します。

> [!NOTE]
> 拡張機能は、 [こちらのリンク](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)
> 
> 

インストール後、ロジック アプリ デザイナーで [Azure リソース グループ] プロジェクトを使用できる状態になります。

## <a name="create-a-project"></a>プロジェクトの作成
1. **[ファイル]** メニューの **[新規作成]** >  **[プロジェクト]**をクリックします (または、**[追加]** の **[新しいプロジェクト]** をクリックして既存のソリューションに追加します)。  ![[ファイル] メニュー](./media/app-service-logic-deploy-from-vs/filemenu.png)
2. ダイアログ ボックスで、**[クラウド]** を見つけて、**[Azure リソース グループ]** を選択します。 **[名前]** に名前を入力し、**[OK]** をクリックします。
    ![新しいプロジェクトの追加](./media/app-service-logic-deploy-from-vs/addnewproject.png)
3. **[ロジック アプリ]** テンプレートを選択します。 ロジック アプリのひな形となる空のデプロイ テンプレートが作成されます。
    ![Azure テンプレートの選択](./media/app-service-logic-deploy-from-vs/selectazuretemplate.png)
4. **テンプレート**を選択したら、**[OK]** をクリックします。
   
    これで、ロジック アプリ プロジェクトがソリューションに追加されます。 ソリューション エクスプローラーにデプロイ ファイルが表示されます。  
   
    ![デプロイ](./media/app-service-logic-deploy-from-vs/deployment.png)

## <a name="using-the-logic-app-designer"></a>ロジック アプリ デザイナーの使用
ロジック アプリを含んだ Azure リソース グループ プロジェクトが整ったら、ワークフローの作成を支援する Visual Studio 内のデザイナーを開くことができます。  利用可能なプロパティとデータをデザイナーがコネクタに問い合わせるためには、インターネット接続が必要となります (たとえば、Dynamics CRM Online コネクタを使用している場合、デザイナーは、使用できるカスタム プロパティと既定のプロパティをリストするよう CRM のインスタンスに問い合わせを行います)。

1. `<template>.json` ファイルを右クリックし、**[Open with Logic App Designer (ロジック アプリ デザイナーで開く)]** を選択します (または `Ctrl+L` を押します)。
2. サブスクリプション、リソース グループ、デプロイ テンプレートの場所を選択します。
   * ロジック アプリを設計することによって、**API 接続**リソースが作成されることに注意してください。この接続リソースは、設計時にプロパティを問い合わせる目的で使用されます。  ここで選択したリソース グループが、設計時にそれらの接続を作成するためのリソース グループになります。  Azure Portal に移動して参照画面で **[API 接続]** を選択すると、任意の API 接続を表示したり変更を加えたりすることができます。
     ![Subscription Picker](./media/app-service-logic-deploy-from-vs/designer_picker.png)
3. `<template>.json` ファイル内の定義に基づいてデザイナーが表示されます。
4. これでロジック アプリを作成して設計できる状態になりました。変更はデプロイ テンプレートに反映されます。
    ![Visual Studio のデザイナー](./media/app-service-logic-deploy-from-vs/designer_in_vs.png)

またリソース ファイルには、そのロジック アプリが機能するために必要なすべての接続の `Microsoft.Web/connections` リソースが追加されていることがわかります。  これらの接続プロパティはデプロイ時に設定できるほか、デプロイ後は、Azure Portal の **[API 接続]** で管理することができます。

### <a name="switching-to-the-json-code-view"></a>JSON コード ビューへの切り替え
デザイナーの一番下にある **[コード ビュー]** タブを選択すると、ロジック アプリの JSON 表示に切り替わります。  再度フル リソースの JSON に切り替えるには、 `<template>.json` ファイルを右クリックして **[開く]**を選択します。

### <a name="saving-the-logic-app"></a>ロジック アプリの保存
ロジック アプリは、**[保存]** ボタンまたは `Ctrl+S` を使用して、いつでも保存することができます。  保存時にロジック アプリにエラーが発生した場合は、Visual Studio の **[出力]** ウィンドウに表示されます。

## <a name="deploying-your-logic-app"></a>ロジック アプリのデプロイ
最後に、アプリを構成したら、いくつかの手順で Visual Studio から直接デプロイすることができます。 

1. ソリューション エクスプローラーでプロジェクトを右クリックし、**[デプロイ]** > **[新しいデプロイ]**
    ![New deployment](./media/app-service-logic-deploy-from-vs/newdeployment.png) の順に移動します。
2. Azure サブスクリプションへのサインインを求められます。 
3. ここでは、ロジック アプリをデプロイするリソース グループの詳細を選択する必要があります。 
    ![リソース グループへのデプロイ](./media/app-service-logic-deploy-from-vs/deploytoresourcegroup.png)
   
   > [!NOTE]
   > リソース グループに適したテンプレートとパラメーター ファイルを必ず選択してください (たとえば、運用環境にデプロイする場合、運用中のパラメーター ファイルを選択します)。 
   > 
   > 
4. [デプロイ] ボタンをクリックします
5. デプロイの状態が **[出力]** ウィンドウに表示されます **([Azure のプロビジョニング]** の選択が必要になる場合があります)。 
    ![Output](./media/app-service-logic-deploy-from-vs/output.png)

今後は、ソース管理でロジック アプリを修正し、Visual Studio を使用して新しいバージョンをデプロイできます。 

> [!NOTE]
> Azure ポータルで定義を直接変更した場合、次回 Visual Studio からデプロイすると、変更内容がオーバーライドされます。
> 
> 

## <a name="next-steps"></a>次のステップ
* Logic Apps の使用を開始するには、 [ロジック アプリの作成](app-service-logic-create-a-logic-app.md) に関するページのチュートリアルに従ってください。  
* [一般的な例とシナリオを確認する](app-service-logic-examples-and-scenarios.md)
* [Logic Apps を使用してビジネス プロセスを自動化する](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Logic Apps を使用してシステムを統合する方法を説明する](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Nov16_HO3-->


