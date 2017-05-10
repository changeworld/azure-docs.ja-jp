---
title: "Azure Active Directory B2C: カスタム ポリシーを使用した UI カスタマイズ | Microsoft Docs"
description: "Azure AD B2C でカスタム ポリシーを使用する場合のユーザー インターフェイス (UI) のカスタマイズに関するトピック"
services: active-directory-b2c
documentationcenter: 
author: saeeda
manager: krassk
editor: gsacavdm
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: saeeda
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 215f9baeda7cd0bcf3fd66893919575647849e7d
ms.contentlocale: ja-jp
ms.lasthandoff: 05/02/2017

---
# <a name="azure-active-directory-b2c-ui-customization-in-a-custom-policy"></a>Azure Active Directory B2C: カスタム ポリシーでの UI カスタマイズ

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事を完了すると、独自のブランドと外観を持つカスタム ポリシーでサインアップとサインインが行えます。  Azure AD B2C では、エンド ユーザーに表示される HTML と CSS をほぼ完全に制御できます。  カスタム ポリシーを使用する場合、UI のカスタマイズは Azure ポータルでコントロールを使用するのではなく XML で構成されます。

## <a name="prerequisites"></a>前提条件

処理を実行する前に、[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)を完了する必要があります。  ローカル アカウントでのサインアップとサインインのために作業用カスタム ポリシーを持つ必要があります。

### <a name="confirming-your-b2c-tenant"></a>B2C テナントの確認

カスタム ポリシーはまだプライベート プレビュー段階のため、カスタム ポリシーのアップロードのために Azure AD B2C テナントが有効になっていることを確認します。

1. [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキストに切り替え](active-directory-b2c-navigate-to-b2c-context.md)、[Azure AD B2C] ブレードを開きます。
1. **[All Policies]**(すべてのポリシー) をクリックします。
1. **[Upload Policy]**(アップロード ポリシー) が利用可能なことを確認します。  ボタンが無効になっている場合、AADB2CPreview@microsoft.com までメールでお知らせください。

## <a name="the-page-ui-customization-feature"></a>ページ UI カスタマイズ機能

ページの UI のカスタマイズ機能を使用して、任意のカスタム ポリシーのルック アンド フィールをカスタマイズできます。  アプリケーションと Azure AD B2C 間のブランドと視覚的な一貫性を維持します。

しくみは次のとおりです。Azure AD B2C はコンシューマーのブラウザーでコードを実行すると共に、[クロス オリジン リソース共有 (CORS)](http://www.w3.org/TR/cors/) と呼ばれる最新の手法を使用します。  最初に、カスタマイズされた HTML コンテンツを含むカスタム ポリシーで URL を指定します。  Azure AD B2C は UI 要素と URL からロードした HTML コンテンツをマージし、コンシューマーにページを表示します。

## <a name="creating-your-html5-content"></a>HTML5 のコンテンツの作成

タイトルに製品のブランド名を使用して HTML コンテンツを作成しましょう。

1. この html スニペットで **[コピー]** をクリックします。  これは、`<body>` 内のどこかにある `<div id="api"></div>` と呼ばれる空の要素を持つ整形式の HTML5 です。 この要素は、Azure AD B2C コンテンツを挿入する位置をマークします。

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. テキスト エディターに貼り付け、ファイルを `customize-ui.html` として保存します。

## <a name="create-a-blob-storage-account"></a>BLOB ストレージ アカウントの作成

>[!NOTE]
> このガイドでは、Azure BLOB ストレージを使用して、コンテンツをホストします。  Web サーバーでコンテンツをホストすることもできますが、この場合は [Web サーバーでクロス オリジン リソース共有 (CORS) を有効にする](https://enable-cors.org/server.html)必要があります。

Azure BLOB ストレージ上で、この HTML をホストしてみましょう。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
1. ハブ メニューで、**[新規]** -> **[ストレージ]** -> **[ストレージ アカウント]** の順にクリックします。
1. ストレージ アカウント用に一意の**名前**を入力します。
1. **[デプロイ モデル]** は **[リソース マネージャー]** のまま残してかまいません。
1. **[Account Kind]** (アカウントの種類) を **[Blob Storage]** に変更します。
1. **[パフォーマンス]** は **[標準]** のまま残してかまいません。
1. **[レプリケーション]** は **[RA-GRS]** のまま残してかまいません。
1. **[アクセス層]** は **[ホット]** のまま残してかまいません。
1. **[ストレージ サービスの暗号化]** は **[無効]** のまま残してかまいません。
1. ストレージ アカウントに **[サブスクリプション]** を選択します。
1. **リソース グループ**を作成するか、または既存のリソース グループを選択します。
1. ストレージ アカウントの**地理的な場所**を選択します。
1. **[作成]** をクリックしてストレージ アカウントを作成します。
1. デプロイが完了するまでお待ちください。ストレージ アカウントのブレードが自動的に開きます。

## <a name="create-a-container"></a>コンテナーを作成する

Azure Blob Storage 上にパブリック コンテナーを作成しましょう。

1. **[概要]** と呼ばれる左側のタブに切り替えます。
1. **[+ Container]** (+ コンテナー) をクリックします。
1. **[名前]** は「`$root`」と入力します。
1. **[Access type]** (アクセスの種類) を **[Blob]** に設定します。
1. '$root' をクリックして、新しいコンテナーを開きます。
1. **[アップロード]**をクリックします。
1. `Select a file` の横にあるフォルダー アイコンをクリックします。
1. [前のセクション](#the-page-ui-customization-feature)で作成した `customize-ui.html` を参照します。
1. **[アップロード]**をクリックします。
1. アップロードした `customize-ui.html` と呼ばれる Blob を選択します。
1. **URL** の横にある [コピー] ボタンをクリックします。
1. ブラウザーを開き、この URL に移動します。  アクセスできない場合は、コンテナーのアクセスの種類が Blob に設定されていることを確認してください。

## <a name="configure-cors"></a>CORS を構成する

次に、クロス オリジン リソース共有 (CORS) の Azure Blob Storage を構成します。

>[!NOTE]
>サンプル HTML と CSS コンテンツを使用して UI のカスタマイズ機能を試すことができます。  Azure Blob Storage アカウントにサンプル コンテンツをアップロードし、構成する[簡単なヘルパー ツール](active-directory-b2c-reference-ui-customization-helper-tool.md)が提供されています。  このツールを使用する場合は、スキップして[サインアップまたはサインイン カスタム ポリシーの変更](#modify-your-sign-up-or-sign-in-custom-policy)に進んでください。

1. [設定] の下にある [ストレージ ブレード] で、**[CORS]** を開きます。
1. **[+ 追加]** をクリックします。
1. `Allowed origins` を `*` に設定します。
1. `Allowed verbs` と呼ばれるドロップダウン リストで、`GET` と `OPTIONS` の両方を選択します。
1. `Allowed headers` を `*` に設定します。
1. `Exposed headers` を `*` に設定します。
1. `Maximum age (seconds)` を `200` に設定します。
1. **[追加]**をクリックします。

## <a name="testing-cors"></a>CORS のテスト

準備が完了したかどうかを確認してみましょう。

1. http://test-cors.org/ に移動し、`Remote URL` フィールドに URL を貼り付けます。
1. **[Send Request]** (要求を送信する) をクリックします。
1. エラーが発生した場合、[CORS の設定](#configure-cors)が正しいかどうか確認します。  ブラウザー キャッシュをクリアするか、またはプライベート ブラウズ セッション `CTRL-SHIFT-P` を試行する必要がある場合もあります。

## <a name="modify-your-sign-up-or-sign-in-custom-policy"></a>サインアップまたはサインイン カスタム ポリシーを変更する

1. 最上位の `<TrustFrameworkPolicy>` タグの下で、`<BuildingBlocks>` タグを見つけます。  `<BuildingBlocks>` タグの内部に、次の例をコピーして `ContentDefinitions` タグを追加します。  `{your_storage_account}` をストレージ アカウントの名前に置き換えます。

  ```xml
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.idpselections">
        <LoadUri>https://{your_storage_account}.blob.core.windows.net/customize-ui.html</LoadUri>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>
  ```

## <a name="upload-your-updated-custom-policy"></a>更新したカスタム ポリシーをアップロードします。

1. [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキストに切り替え](active-directory-b2c-navigate-to-b2c-context.md)、[Azure AD B2C] ブレードを開きます。
1. **[All Policies]**(すべてのポリシー) をクリックします。
1. **[Upload Policy]** (ポリシーのアップロード) を選択します。
1. 追加した `ContentDefinitions` タグを含む `SignUpOrSignin.xml` をアップロードします。

## <a name="test-the-custom-policy-using-run-now"></a>"Run Now" を使用してカスタム ポリシーをテストする

1. **Azure AD B2C ブレード**を開き、**[All polices]**(すべてのポリシー) に移動します。
1. アップロードしたカスタム ポリシーを選択し、**[Run now]**(今すぐ実行) ボタンをクリックします。
1. メール アドレスを使用してサインアップできることを確認します。

## <a name="next-steps"></a>次のステップ

この[組み込みポリシーにおける UI カスタマイズのリファレンス ガイド](active-directory-b2c-reference-ui-customization.md)には、カスタマイズできる UI 要素に関する追加情報が含まれています。  組み込みポリシーとカスタム ポリシーの間で、UI のカスタマイズに違いはありません。

