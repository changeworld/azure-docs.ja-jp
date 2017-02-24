---
title: "Visual Studio Cloud Explorer から Azure Logic Apps を管理する |Microsoft Docs"
description: "Visual Studio Cloud Explorer から Azure Logic Apps を管理します。"
author: klam
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: klam
translationtype: Human Translation
ms.sourcegitcommit: a9f786259676a1bc9aa616f2db2935dff45475e2
ms.openlocfilehash: dcb0512b4f9e6c94262a3f39ef59f82fcfbf313e


---
# <a name="manage-logic-apps-with-the-visual-studio-cloud-explorer"></a>Visual Studio Cloud Explorer を使用して Azure Logic Apps を管理する
[Azure Portal](https://portal.azure.com) には Logic Apps を設計して管理するための強力な機能が用意されていますが、Visual Studio Cloud Explorer を使用して Logic Apps をはじめとする多くの Azure 資産を Visual Studio から管理できます。  Cloud Explorer では、発行済みの Logic Apps を参照して、実行履歴の有効化、無効化、編集、表示などのアクションを実行できます。 

## <a name="installation-steps"></a>インストール手順
以降、Logic Apps 用 Visual Studio ツールをインストールして構成する手順について説明します。

### <a name="prerequisites"></a>前提条件
* [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [最新の Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 以上)
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
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

## <a name="browsing-logic-apps"></a>Logic Apps の参照
Cloud Explorer で Logic Apps を参照するには、[表示] > [Cloud Explorer] で Cloud Explorer を開き、リソース グループまたはリソース タイプで参照します。  リソース タイプで参照する場合は、サブスクリプションを選択し、[Logic Apps] セクションを展開してロジック アプリを&1; つ選択します。  いずれかの Logic Apps を右クリックするか、Cloud Explorer の下部にある [アクション] メニューを使用して目的のアクションを実行します。

![参照](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-logic-app-with-the-designer"></a>デザイナーを使用したロジック アプリの編集
Azure Portal のデザイナーと同じデザイナーで現在デプロイされているロジック アプリを開くには、ロジック アプリを右クリックし、[Logic App Editor で開く] を選択します。  デザイナーでは、ロジック アプリを編集してクラウドに再度保存し、[トリガーの実行] ボタンで新しい実行を開始することができます。

![デザイナー](./media/logic-apps-manage-from-vs/designer.png)

## <a name="browse-logic-app-run-history"></a>ロジック アプリの実行履歴の参照
ロジック アプリの実行履歴を一覧表示するには、ロジック アプリを右クリックし、[実行履歴を開く] を選択します。  このビューで列ヘッダーを選択して、表示されている任意のプロパティで並べ替えることができます。  

![実行](media/logic-apps-manage-from-vs/runs.png)

いずれかの実行インスタンスをダブルクリックすると、そのインスタンスの実行履歴が表示され、各ステップの入力と出力を含めた実行結果を確認することができます。

![履歴](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>次のステップ
* Logic Apps の使用を開始するには、 [ロジック アプリの作成](logic-apps-create-a-logic-app.md) に関するページのチュートリアルに従ってください。  
* [一般的な例とシナリオを確認する](logic-apps-examples-and-scenarios.md)
* [Logic Apps を使用してビジネス プロセスを自動化する](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Logic Apps を使用してシステムを統合する方法を説明する](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Jan17_HO3-->


