---
title: "カスタム コネクタの認定 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps、Microsoft Flow、および Microsoft PowerApps のすべてのユーザーがコネクタを使用できるようにします"
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
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 9bcb138c181df0c12548cc5dc721542fd9c9ba68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="submit-your-connectors-for-microsoft-certification"></a>Microsoft の認定を受けるためにコネクタを提出する

Azure Logic Apps、Microsoft Flow、および Microsoft PowerApps のすべてのユーザーにカスタム コネクタを公開するには、公開に必要なレビュー、検証、および承認処理のためにコネクタを Microsoft に提出します。 

## <a name="certification-criteria"></a>認定基準

| 機能 | 詳細 | 必須または推奨 |
|------------|---------|-------------------------|
| サービスとしてのソフトウェア (SaaS) アプリ | Logic Apps、Flow、PowerApps に十分に適合するユーザー シナリオを満たしていること。 | 必須 |
| 認証の種類 | API は、OAuth2、API キー、または基本認証をサポートすること。 | 必須 | 
| サポート | 顧客がサポートを受けることができるサポート連絡先が用意されていること。 | 必須 | 
| 可用性とアップタイム | アプリのアップタイムは 99.9% 以上です。 | 推奨 | 
|||| 

また、Microsoft パートナーまたは独立系ソフトウェア ベンダー (ISV) ではないメンバーが認定を取得してコネクタを一般公開するには、基になるサービスを所有しているか、API を使用する明示的な権限を示す必要があります。

## <a name="validation-phases"></a>検証フェーズ

Microsoft では、次の検証フェーズを使用してコネクタを評価します。

| 検証フェーズ | Description | 
| ----- | ----------- |
| 機能 | Microsoft では、次のエラーについて、Logic Apps、Flow、および PowerApps でコネクタをテストします。 <p>- カスタム コネクタ ウィザードの定義セクションに表示される無効な OpenAPI (Swagger) エラーまたは JSON エラー <p>- カスタム コネクタ ウィザードのテスト セクションに表示されるランタイムおよびスキーマ検証エラー | 
| コンテンツ | Microsoft では、コネクタの各エンティティに対してフレンドリな名前および説明が使用されているかどうかを確認します。 コネクタの Swagger 内の各操作、入力パラメーター、および応答属性に次の要素が含まれていることを確認してください。 <p>- [summary](../logic-apps/custom-connector-openapi-extensions.md#summary) <br>- [description](../logic-apps/custom-connector-openapi-extensions.md#description) </br>- [可視性情報](../logic-apps/custom-connector-openapi-extensions.md#visibility) | 
||| 

## <a name="nominate-and-submit-your-connector-to-microsoft-for-certification"></a>コネクタの認定申請を Microsoft に提出する

認定を申請するには、次の手順を実行します。

1. **申請**

   1. [申請を提出します](https://go.microsoft.com/fwlink/?linkid=848754)。

      1. ページの下部にある **[Contact us]\(お問い合わせ\)** を選択します。
      2. フォームに記入し、**[Questions on ISV connector program and co-marketing]\(ISV コネクタ プログラムと共同マーケティングに関する質問\)** を選択します。
      3. **[送信]** を選択します。

   2. 受け取った相互の機密保持契約書とパートナー契約書に署名します。 

      Microsoft では、手続きを続行するにあたってこれらの署名済み契約書を必要とします。 
      その後、Microsoft は、コネクタが認定基準を満たしているかどうかを確認します。 
   
   3. コネクタが承認された場合、Microsoft からオンボーディングの手順が通知されます。
    
2. **Review**

   1. 審査を受けるために次の情報を申請担当者に送信します。

      * API が記述された OpenAPI ファイル
      * コネクタを表すアイコン ファイル (.png または .jpg) 
      
        アイコンについては、230 ピクセルの正方形内に最大 160 ピクセルのロゴが示されている必要があります。 
        色付きの背景に白いロゴが推奨されます。
      
      * 16 進数形式で示されたアイコンのブランド カラー。これは、アイコン ファイルの色付きの背景と一致する必要があります

      * 検証用のテスト アカウント
      * サポート連絡先

   2. さらに情報が必要な場合は、Microsoft から連絡があります。

3. **[発行]**

    Microsoft は、コネクタの機能とコンテンツを検証した後、すべての製品とリージョンにわたってデプロイするために、コネクタをステージングします。
    
    既定では、すべてのコネクタは "Premium" コネクタとして発行されます。 
    Azure でアプリを構築した場合は、コネクタを、Office 365 Enterprise プランを利用するすべてのユーザーが使用できる "Standard" コネクタとして登録することを申請できます。 
    詳細については、申請担当者にお問い合わせください。

## <a name="next-steps"></a>次のステップ

* [カスタム コネクタに関する FAQ](../logic-apps/custom-connector-faq.md)
* [カスタム コネクタの概要](../logic-apps/custom-connector-overview.md)