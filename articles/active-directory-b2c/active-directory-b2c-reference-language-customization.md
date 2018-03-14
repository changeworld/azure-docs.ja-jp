---
title: "言語のカスタマイズの使用 - Azure AD B2C | Microsoft Docs"
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 02/26/2018
ms.author: sama
ms.openlocfilehash: 332c6b4ffd841a2c7aef9db5c8ba9e843790f4d6
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2018
---
# <a name="azure-active-directory-b2c-using-language-customization"></a>Azure Active Directory B2C: 言語のカスタマイズの使用

>[!NOTE]
>この機能はパブリック プレビュー段階にあります。
>

言語のカスタマイズを使用すると、顧客のニーズに応じて、ポリシーをさまざまな言語に対応させることができます。  Microsoft では 36 言語の翻訳を提供していますが (「[追加情報](#additional-information)」を参照)、どの言語についても、ご自身で翻訳を提供していただけます。  1 つの言語でのみページが提供されている場合でも、ページ上の任意のテキストをカスタマイズできます。  

## <a name="how-does-language-customization-work"></a>言語のカスタマイズのしくみ
言語のカスタマイズを使用すると、ユーザー体験で利用できる言語を選択できます。  この機能を有効にすると、アプリケーションからクエリ文字列パラメーター ui_locales を指定できます。  Azure AD B2C を呼び出すと、ページが指定したロケールに翻訳されます。  この種類の構成では、ユーザー体験の言語を完全に制御し、顧客のブラウザーの言語設定を無視できます。 また、顧客に表示される言語をそこまで制御する必要がない場合もあります。  ui_locales パラメーターを指定しない場合、顧客のエクスペリエンスはブラウザーの設定によって決まります。  その場合でも、ユーザー体験の翻訳先言語を、サポートされる言語として追加することで制御できます。  サポートしない言語を表示するように顧客のブラウザーが設定されている場合は、サポートされているカルチャで既定として選択した言語が代わりに表示されます。

1. **ui-locales で指定した言語** - [言語のカスタマイズ] を有効にすると、ユーザー体験はここで指定された言語に翻訳されます
2. **ブラウザーが要求した言語** - ui-locales が指定されていない場合は、ブラウザーが要求した言語に翻訳されます (**サポートされている言語に含まれている場合**)
3. **ポリシーの既定の言語** - ブラウザーで言語が指定されていない場合、またはサポートされていない言語が指定されている場合は、ポリシーの既定の言語に翻訳されます

>[!NOTE]
>カスタム ユーザー属性を使用している場合は、独自の翻訳を提供する必要があります。 詳細については、「[文字列のカスタマイズ](#customize-your-strings)」を参照してください。
>

## <a name="support-uilocales-requested-languages"></a>ui_locales で要求した言語のサポート 
言語のカスタマイズの一般公開リリース前に作成されたポリシーについては、最初に、この機能を有効にする必要があります。  後に作成されたポリシーでは、言語のカスタマイズは既定で有効になっています。  ポリシーで [言語のカスタマイズ] を有効にし、ui_locales パラメーターを追加することで、ユーザー体験の言語を制御できるようになりました。
1. [こちらの手順に従って、Azure Portal で B2C 機能ページに移動します。](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings)
2. 翻訳を有効にするポリシーに移動します。
3. **[言語のカスタマイズ]** をクリックします。  
4. 上部にある **[言語のカスタマイズを有効化]** をクリックします。
5. ダイアログを読み、[はい] をクリックします。

## <a name="select-which-languages-in-your-user-journey-are-enabled"></a>ユーザー体験で有効にする言語の選択 
ui_locales パラメーターが指定されていない場合に、ユーザー体験のために翻訳する一連の言語を有効にします。
1. 前の手順で、ポリシーの [言語のカスタマイズ] を有効にしていることを確認します。
2. **[ポリシーの編集]** ページで、**[言語のカスタマイズ]** を選択します。
3. サポートする言語を選択します。
4. プロパティ ウィンドウで、**[有効]** を [はい] に切り替えます。  
5. プロパティ ウィンドウの上部にある **[保存]** をクリックします。

>[!NOTE]
>ui_locales パラメーターが指定されていない場合、ページは顧客のブラウザーの言語に翻訳されます (これが有効になっている場合のみ)
>

## <a name="customize-your-strings"></a>文字列のカスタマイズ
[言語のカスタマイズ] を使用すると、ユーザー体験における文字列をカスタマイズできます。
1. 前の手順で、ポリシーの [言語のカスタマイズ] を有効にしていることを確認します。
2. **[ポリシーの編集]** ページで、**[言語のカスタマイズ]** を選択します。
3. カスタマイズする言語を選択します。
4. 編集するページを選択します。
5. **[既定値のダウンロード]** (この言語を前に編集した場合は **[オーバーライドのダウンロード]**) をクリックします。 

上記の手順により、文字列の編集を開始する際に使用できる JSON ファイルを入手できます。

### <a name="changing-any-string-on-the-page"></a>ページ上の文字列の変更
1. 前の手順でダウンロードした JSON ファイルを JSON エディターで開きます。
2. 変更する要素を探します。  探している文字列の `StringId` を見つけるか、変更する `Value` を見つけます。
3. `Value` 属性を表示する文字列に更新します。
4. 変更するすべての文字列について、忘れずに `Override` を **True** に切り替えてください。
5. ファイルを保存し、変更をアップロードします (アップロード コントロールは、JSON ファイルをダウンロードした場所にあります)。 

>[!IMPORTANT]
>文字列を上書きする必要がある場合は、必ず `Override` の値を `true` に設定してください。  値が変更されていない場合、そのエントリは無視されます。 
>

### <a name="changing-extension-attributes"></a>拡張属性の変更
カスタム ユーザー属性の文字列を変更する場合や、JSON に追加する場合は、次の形式を使用します。
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

`<ExtensionAttribute>` をカスタム ユーザー属性の名前に置き換えます。  

`<ExtensionAttributeValue>` を表示する新しい文字列に置き換えます。

### <a name="using-localizedcollections"></a>LocalizedCollections の使用
応答の値セットの一覧を指定する場合は、`LocalizedCollections` を作成する必要があります。  `LocalizedCollections` は、`Name` と `Value` のペアの配列です。  `Name` は表示される値、`Value` は要求で返される値です。  `LocalizedCollections` を追加するには、次の形式を使用します。

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType", 
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` は、この `LocalizedCollections` が応答になるユーザー属性です
* `Name` は、ユーザーに表示される値です
* `Value` は、このオプションが選択された場合に要求で返される値です

### <a name="upload-your-changes"></a>変更のアップロード
1. JSON ファイルの変更が完了したら、B2C テナントに戻ります。
2. **[ポリシーの編集]** ページで、**[言語のカスタマイズ]** を選択します。
3. 翻訳を提供する言語を選択します。
4. 翻訳を提供するページを選択します。
5. フォルダー アイコンをクリックし、アップロードする JSON ファイルを選択します。
6. この変更はポリシーに自動的に保存されます。

## <a name="using-page-ui-customization-with-language-customization"></a>言語のカスタマイズによる ページ UI のカスタマイズの使用

HTML コンテンツをローカライズする方法は 2 つあります。  1 つは "[言語のカスタマイズ](active-directory-b2c-reference-language-customization.md)" を有効にする方法です。  この機能を有効にすると、Azure AD B2C が Open ID Connect パラメーター `ui-locales` をエンドポイントに転送できるようになります。  コンテンツ サーバーは、このパラメーターを使用して、言語固有のカスタマイズされた HTML ページを提供できます。

使用されているロケールに基づいて、さまざまな場所からコンテンツをプルすることもできます。  ご利用の CORS 対応エンドポイントでは、特定の言語のコンテンツをホストするようフォルダー構造を設定でき、ワイルドカード値 `{Culture:RFC5646}` を入力すると、適切な言語が呼び出されます。  たとえば、カスタム ページの URI として以下を使用しているとします。

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
自分のページは `fr` で読み込むことができます。そのページが html および css コンテンツをプルするときは、以下からプルします。
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="custom-locales"></a>カスタム ロケール

現在 Microsoft が翻訳を提供していない言語を追加することもできます。  ポリシー内のすべての文字列に対して翻訳を提供する必要があります。

1. **[ポリシーの編集]** ページで、**[言語のカスタマイズ]** を選択します。
2. ページの上部から **[カスタム言語の追加]** を選択します。
3. 表示されたコンテキスト ウィンドウで、有効なロケール コードを入力して、翻訳を提供する言語を指定します。
4. ページごとに英語の一連のオーバーライドをダウンロードし、翻訳作業を行うことができます。
5. 完了した JSON ファイルは、ページごとにアップロードできます。
6. **[有効]** を選択すると、ポリシーによりユーザーに対してこの言語が表示されます。
7. 有効にした言語は忘れずに保存してください。

## <a name="additional-information"></a>追加情報

### <a name="page-ui-customization-labels-are-persisted-as-your-first-set-of-overrides-once-language-customization-is-enabled"></a>"言語のカスタマイズ" を有効にすると、ページ UI のカスタマイズ ラベルが、オーバーライドの最初のセットとして保持される
"言語のカスタマイズ" を有効にすると、ページ UI のカスタマイズを使用した、ラベルの以前の編集内容は、英語 (en) の JSON ファイルに保存されます。  引き続き、[言語のカスタマイズ] で言語リソースをアップロードして、ラベルなどの文字列を変更できます。
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>Microsoft は、最新の翻訳を提供するよう努めています。
継続的に翻訳を改善し、コンプライアンスを維持します。  バグやグローバルな用語の変更を特定し、ユーザー体験にシームレスに適用される更新を行います。
### <a name="support-for-right-to-left-languages"></a>右から左へ記述する言語のサポート
右から左へ記述する言語は、現在、サポートされていません。この機能を必要とする場合は、[Azure フィードバック](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag)でこの機能へのご要望をお寄せください。
### <a name="social-identity-provider-translations"></a>ソーシャル ID プロバイダーの翻訳
Microsoft ではソーシャル ログインに ui_locales OIDC パラメーターを渡していますが、Facebook や Google など、一部のソーシャル ID プロバイダーでは適用されません。 
### <a name="browser-behavior"></a>ブラウザーの動作
Chrome と Firefox はどちらもそれぞれの設定言語を要求し、その言語がサポートされている場合は、既定よりも優先して表示されます。  Edge は現在、言語を要求せず、既定の言語が表示されます。

### <a name="what-languages-are-supported"></a>どの言語がサポートされていますか。

| 言語              | 言語コード |
|-----------------------|---------------|
| バングラ語                | bn            |
| チェコ語                 | cs            |
| デンマーク語                | da            |
| ドイツ語                | de            |
| ギリシャ語                 | el            |
| 英語               | en            |
| スペイン語               | es            |
| フィンランド語               | fi            |
| フランス語                | fr            |
| グジャラート語              | gu            |
| ヒンディー語                 | hi            |
| クロアチア語              | hr            |
| ハンガリー語             | hu            |
| イタリア語               | it            |
| 日本語              | ja            |
| カンナダ語               | kn            |
| 韓国語                | ko            |
| マラヤーラム語             | ml            |
| マラーティー語               | mr            |
| マレー語                 | ms            |
| ノルウェー語 - ブークモール      | nb            |
| オランダ語                 | nl            |
| パンジャーブ語               | pa            |
| ポーランド語                | pl            |
| ポルトガル語 - ブラジル   | pt-br         |
| ポルトガル語 - ポルトガル | pt-pt         |
| ルーマニア語              | ro            |
| ロシア語               | ru            |
| スロバキア語                | sk            |
| スウェーデン語               | sv            |
| タミール語                 | ta            |
| テルグ語                | te            |
| タイ語                  | th            |
| トルコ語               | tr            |
| 中国語 - 簡体字  | zh-hans       |
| 中国語 - 繁体字 | zh-hant       |
