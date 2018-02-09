---
title: "SOAP コネクタを作成および登録する - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps で使用する SOAP コネクタをセットアップする"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 031762e5639fc52e0b0a6a5bf8d12db25da25e12
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2018
---
# <a name="create-and-register-soap-connectors-in-azure-logic-apps"></a>Azure Logic Apps で SOAP コネクタを作成および登録する

お使いのロジック アプリのワークフローに SOAP サービスを統合するために、お使いの SOAP サービスを記述している Web サービス記述言語 (WSDL) を使用して、カスタムの簡易オブジェクト アクセス プロトコル (SOAP) コネクタを作成して登録できます。 SOAP コネクタは、ロジック アプリ内の他のコネクターと同じように使用できるので、事前に構築されたコネクタと同様に機能します。


## <a name="prerequisites"></a>前提条件

お使いの SOAP コネクタを登録するには、次の項目が必要です。

* Azure サブスクリプション。 サブスクリプションがない場合は、[無料の Azure アカウント](https://azure.microsoft.com/free/)をご利用いただけます。 また、[従量課金のサブスクリプション](https://azure.microsoft.com/pricing/purchase-options/)にサインアップすることもできます。

* 次のいずれかが必要です。
  * お使いの SOAP サービスおよび API を定義する WSDL への URL
  * お使いの SOAP サービスおよび API を定義する WSDL ファイル

  このチュートリアルでは、サンプルの[SOAP サービスの注文](http://fazioapisoap.azurewebsites.net/FazioService.svc?singleWsdl)を使用できます。

* 任意: カスタム コネクタのアイコンとして使用する画像


## <a name="1-create-your-connector"></a>1.コネクタを作成する

1. Azure Portal の Azure メイン メニューで、**[新規]** を選びます。 検索ボックスにフィルターとして「Logic Apps コネクタ」と入力し、Enter キーを押します。

   ![[新規]、"Logic Apps コネクタ" の検索](./media/logic-apps-soap-connector-create-register/create-logic-apps-connector.png)

2. 結果の一覧から、**[Logic Apps コネクタ]** > **[作成]** を選びます。

   ![Logic Apps コネクタを作成する](./media/logic-apps-soap-connector-create-register/choose-logic-apps-connector.png)

3. 表に示すように、コネクタ登録の詳細を指定します。 終了したら、**[ダッシュボードにピン留めする]** > **[作成]** を選びます。

   ![Logic App カスタム コネクタの詳細](./media/logic-apps-soap-connector-create-register/logic-apps-soap-connector-details.png)

   | プロパティ | 推奨値 | Description | 
   | -------- | --------------- | ----------- | 
   | **名前** | *soap-connector-name* | コネクタの名前を指定します。 | 
   | **サブスクリプション** | <*Azure サブスクリプション名*> | Azure サブスクリプションを選択します。 | 
   | **[リソース グループ]** | <*Azure リソース グループ名*> | Azure リソースを整理するための Azure グループを作成または選択します。 | 
   | **場所** | <*デプロイ リージョン*> | コネクタをデプロイするリージョンを選びます。 | 
   |||| 

   Azure がコネクタをデプロイした後、ロジック アプリ コネクタ メニューが自動的に開きます。 
   開かない場合は、Azure ダッシュボードから SOAP コネクタを選びます。

## <a name="2-define-your-connector"></a>2.コネクタを定義する

次に、コネクタを作成するための WSDL ファイルまたは URL、コネクタが使用する認証、SOAP コネクタが提供するアクションおよびトリガーを指定します。


### <a name="2a-specify-the-wsdl-file-or-url-for-your-connector"></a>2a. コネクタの WSDL ファイルまたは URL を指定します。

1. コネクタのメニューで、まだ選ばれていない場合は、**[Logic Apps コネクタ]** を選びます。 ツール バーで、**[編集]** を選びます。

   ![カスタム コネクタを編集する](./media/logic-apps-soap-connector-create-register/edit-soap-connector.png)

2. **[全般]** を選び、以下の表に従って、SOAP コネクタのアクションとトリガーを作成、保護、定義するための詳細を指定します。

   1. **[カスタム コネクタ]** では、API が記述されている WSDL ファイルを提供できるように、お使いの **API エンドポイント** の **[SOAP]** を選択します。

      ![API の WSDL ファイルを提供する](./media/logic-apps-soap-connector-create-register/provide-wsdl-file.png)

      | オプション | 形式 |[説明] | 
      | ------ | ------ | ----------- | 
      | **ファイルからの WSDL のアップロード** | *WSDL ファイル* | WSDL ファイルの場所を参照して、ファイルを選びます。 | 
      | **URL からの WSDL のアップロード** | http://*path-to-wsdl-file* | サービスの WSDL ファイルの URL を提供します。 | 
      | **SOAP を REST へ** |   | SOAP サービスの API を REST API に変換します。 | 
      |||| 

   2. **[全般情報]** では、コネクタ用のアイコンをアップロードします。 
   通常、**[説明]**、**[ホスト]**、**[ベース URL]** フィールドは、WSDL ファイルから自動的に設定されます。 
   設定されない場合は、表に示すようにこの情報を追加して、**[続行]** を選びます。 

      ![コネクタの詳細](./media/logic-apps-soap-connector-create-register/add-general-details.png)

      | オプションまたは設定 | 形式 | [説明] | 
      | ----------------- | ------ | ----------- | 
      | **アップロード アイコン** | <*1 MB 以下の PNG または JPG ファイル*> | コネクタを表すアイコン <p>色: できれば、白いロゴとカラーの背景。 <p>寸法: 230 ピクセルの四角形の中に 160 ピクセル以下のロゴ | 
      | **アイコンの背景色** | <*アイコンのブランドの色の 16 進数コード*> | <p>アイコン ファイルの背景色に一致するアイコンの背景色。 <p>形式: 16 進数。 例: #007ee5 は青を表します。 | 
      | **説明** | <*コネクタの説明*> | コネクタの簡単な説明を指定します。 | 
      | **Host** | <*コネクタのホスト*> | お使いの SOAP サービスのホスト ドメインを指定します。 | 
      | **ベース URL** | <*コネクタのベース URL*> | お使いの SOAP サービスのベース URL を指定します。 | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. コネクタで使う認証を指定する

1. **[セキュリティ]** を選び、コネクタで使う認証を確認または指定します。 認証は、ユーザーの ID がサービスとクライアントの間を適切に流れるようにします。

   既定では、コネクタの **[認証の種類]** は **[認証なし]** に設定されています。
   
   ![認証の種類](./media/logic-apps-soap-connector-create-register/security-authentication-options.png)

   認証の種類を変更するには、**[編集]** を選択します。 **[基本認証]** を選択できます。 既定値以外のパラメーター ラベルを使用するには、**[パラメーター ラベル]** で設定します。

   ![基本認証](./media/logic-apps-soap-connector-create-register/security.png)

   
2. セキュリティ情報を入力した後でコネクタを保存するには、ページの上部にある **[コネクタの更新]** を選んでから **[続行]** を選びます。 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. コネクタのアクションとトリガーを確認、更新、定義する

1. 次に、**[定義]** を選んで、ユーザーがワークフローに追加できる新しいアクションとトリガーを確認、編集、定義します。

   アクションとトリガーは、WSDL ファイルで定義されている操作に基づき、**[定義]** ページが自動的に設定され、要求と応答の値が含まれます。 したがって、必要な操作が既にここに表示されている場合は、このページを変更せずに、登録プロセスの次の手順に進むことができます。

   ![コネクタの定義](./media/logic-apps-soap-connector-create-register/definition.png)

2. 既存のアクションとトリガーを編集するか、新しいアクションとトリガーを追加する必要がある場合は、[以下の手順に進みます](logic-apps-custom-connector-register.md#add-action-or-trigger)。


## <a name="3-finish-creating-your-connector"></a>手順 3.コネクタの作成を完了する

準備ができたら、**[コネクタの更新]** を選んでコネクタをデプロイできるようにします。 

お疲れさまでした。 ロジック アプリを作成すると、Logic Apps デザイナーでコネクタを検索し、ロジック アプリにそのコネクタを追加できるようになります。

![Logic Apps デザイナーでコネクタを見つける](./media/logic-apps-soap-connector-create-register/soap-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>他の Logic Apps ユーザーとコネクタを共有する

登録済みのカスタム コネクタは、認定を受けていなくても、Microsoft が管理するコネクタと同様に機能します。ただし、これらのコネクタを表示および使用できるのは、コネクタの作成者と、ロジック アプリがデプロイされているリージョンで同じ Azure Active Directory テナントとロジック アプリの Azure サブスクリプションを持つユーザーに "*限られます*"。 共有は任意ですが、コネクタを他のユーザーと共有することが望ましい場合があります。 

> [!IMPORTANT]
> コネクタを共有すると、他のユーザーがそのコネクタに依存し始める可能性があります。 
> ***コネクタを削除すると、そのコネクタへのすべての接続が削除されます。***
 
これらの境界の外側の外部ユーザー (たとえば、すべての Logic Apps ユーザー) とコネクタを共有するには、[Microsoft の認定を受けるためにコネクタを提出](../logic-apps/custom-connector-submit-certification.md)します。

## <a name="faq"></a>FAQ

**Q:** SOAP コネクタは一般公開 (GA) されていますか。 </br>
**A:** SOAP コネクタは**プレビュー**段階であり、まだ GA サービスではありません。

**Q:** SOAP コネクタに制限や既知の問題は何かありますか。 </br>
**A:** はい。[SOAP コネクタの制限と既知の問題](../api-management/api-management-api-import-restrictions.md#wsdl)に関するページを参照してください。

**Q:** カスタム コネクタには制限が何かありますか。 </br>
**A:** はい。カスタム コネクタの制限については[こちら](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits)をご覧ください。

## <a name="get-support"></a>サポートを受ける

* 開発とオンボーディングに関するサポートについて、または登録ウィザードにない機能の要求については、[condevhelp@microsoft.com](mailto:condevhelp@microsoft.com) にお問い合わせください。Microsoft では、開発者の質問や問題に対応するために、このアカウントを監視し、質問や問題を適切なチームに転送しています。

* [Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)では、質問の投稿や質問への回答を行うことができるほか、他の Azure Logic Apps ユーザーがどのようなことを行っているかがわかります。

* [Logic Apps ユーザー フィードバック サイト](http://aka.ms/logicapps-wish)でアイデアへの投票やアイデアの投稿を行って、Logic Apps の改善にご協力ください。 

## <a name="next-steps"></a>次の手順

* [省略可能: コネクタの認定を受ける](../logic-apps/custom-connector-submit-certification.md)
* [カスタム コネクタに関する FAQ](../logic-apps/custom-connector-faq.md)