---
title: JavaScript とページ レイアウトのバージョン
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C で JavaScript を有効にし、ページ レイアウトのバージョンを使用する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: project-no-code, devx-track-js
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d234e57f7c11b0d9f2cd212bde93a8b8e478ef41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781366"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C における JavaScript とページ レイアウトのバージョン

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

Azure AD B2C では、ユーザー フローとカスタム ポリシーのユーザー インターフェイス要素に対する HTML、CSS、JavaScript を含むパッケージ コンテンツのセットが提供されています。 アプリケーションで JavaScript を有効にするには、次の手順を実行します。

::: zone pivot="b2c-user-flow"

* [ページ レイアウト](page-layout.md)を選択する
* Azure portal を使用して、ユーザー フローで有効にする
* 要求で [b2clogin.com](b2clogin.md) を使用する

::: zone-end

::: zone pivot="b2c-custom-policy"

* [ページ レイアウト](page-layout.md)を選択する
* [カスタム ポリシー](custom-policy-overview.md)に要素を追加する
* 要求で [b2clogin.com](b2clogin.md) を使用する

::: zone-end

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="select-a-page-layout-version"></a>ページ レイアウトのバージョンを選択する

JavaScript クライアント側コードを有効にする場合は、JavaScript の基になる要素を変更不可にする必要があります。 変更不可になっていないと、何らかの変更によってユーザー フローのページで予期しない動作が発生する可能性があります。 これらの問題を回避するには、ページ レイアウトの使用を強制し、ページ レイアウト バージョンを指定して、JavaScript の基になっているコンテンツ定義を確実に不変にします。 JavaScript を有効にする予定がない場合でも、ページに対してページ レイアウトのバージョンを指定できます。

::: zone pivot="b2c-user-flow"

ユーザー フロー ページのページ レイアウト バージョンを指定するには、次のようにします。 

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
1. ポリシー ("B2C_1_SignupSignin" など) を選択して開きます。
1. **[Page layouts]\(ページ レイアウト\)** を選択します。 **[レイアウト名]** を選択し、 **[ページ レイアウト バージョン (プレビュー)]** を選択します。

さまざまなページ レイアウトのバージョンの詳細については、[ページ レイアウトのバージョン変更ログ](page-layout.md)に関する記事を参照してください。

![ポータルでのページ レイアウト設定でページ レイアウトのバージョンのドロップダウン リストを表示した状態](media/javascript-and-page-layout/page-layout-version.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

アプリケーションのユーザー インターフェイスの要素に[ページ レイアウト](contentdefinitions.md#select-a-page-layout)を選択します。

カスタム ポリシーのコンテンツ定義の "*すべて*" に対して、ページ `contract` バージョンを使用して [ページ レイアウト バージョン](contentdefinitions.md#migrating-to-page-layout)を定義します。 値の形式には、次のように `contract` という語が含まれている必要があります: _urn:com:microsoft:aad:b2c:elements:**contract**:page-name:version_。 ページ バージョンで[ページ レイアウトに移行する](contentdefinitions.md#migrating-to-page-layout)方法について説明します。

次の例は、コンテンツ定義識別子と、対応する **DataUri** をページ コントラクトと共に示しています。 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end

## <a name="enable-javascript"></a>JavaScript を有効にする

::: zone pivot="b2c-user-flow"

ユーザー フローの **[プロパティ]** で、JavaScript を有効にすることができます。 JavaScript を有効にすると、ページ レイアウトの使用も強制されます。 その後、次のセクションで説明するように、ユーザー フローのページ レイアウトのバージョンを設定できます。

![JavaScript の有効化設定を強調したユーザー フローのプロパティ ページ](media/javascript-and-page-layout/javascript-settings.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

**ScriptExecution** 要素を [RelyingParty](relyingparty.md) 要素に追加することで、スクリプトの実行を有効にします。

1. カスタム ポリシー ファイルを開きます。 たとえば、*SignUpOrSignin.xml* などです。
1. **RelyingParty** 要素に **ScriptExecution** 要素を追加します。

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
1. このファイルを保存してアップロードします。

::: zone-end

## <a name="guidelines-for-using-javascript"></a>JavaScript の使用に関するガイドライン

JavaScript を使用して、アプリケーションのインターフェイスをカスタマイズするときに、次のガイドラインに従います。

- `<a>` HTML 要素ではクリック イベントをバインドしないでください。
- Azure AD B2C コードやコメントへの依存関係は使用しないでください。
- Azure AD B2C の HTML 要素の順序や階層は変更しないでください。 UI 要素の順序を制御するには、Azure AD B2C のポリシーを使用します。
- 任意の RESTful サービスを呼び出すことができますが、次の考慮事項があります。
    - クライアント側の HTTP 呼び出しを許可するために、RESTful サービス CORS を設定することが必要な場合があります。
    - RESTful サービスは必ずセキュリティ保護し、HTTPS プロトコルのみを使用してください。
    - JavaScript を直接使用して Azure AD B2C のエンドポイントを呼び出すことはしないでください。
- JavaScript を埋め込んだり、外部の JavaScript ファイルにリンクしたりできます。 外部の JavaScript ファイルを使用する場合は、相対 URL ではなく必ず絶対 URL を使用してください。
- JavaScript フレームワーク: 
    - Azure AD B2C では、[特定のバージョンの jQuery](page-layout.md#jquery-version) を使用します。 別のバージョンの jQuery を含めないでください。 同じページに複数のバージョンを使用すると、問題が発生します。
    - RequireJS の使用はサポートされていません。
    - ほとんどの JavaScript フレームワークは、Azure AD B2C ではサポートされていません。
- `window.SETTINGS`、`window.CONTENT` オブジェクト (現在の UI 言語など) を呼び出すことによって、Azure AD B2C の設定を読み取ることができます。 これらのオブジェクトの値は変更しないでください。
- Azure AD B2C のエラー メッセージをカスタマイズするには、ポリシーでローカライズを使用します。
- ポリシーを使用して実現できるのであれば、それが一般に推奨される方法です。

## <a name="javascript-samples"></a>JavaScript のサンプル

### <a name="show-or-hide-a-password"></a>パスワードの表示または非表示

サインアップに成功するように顧客を支援する一般的な方法は、パスワードの入力内容を表示できるようにすることです。 このオプションを使用して、パスワードを簡単に表示し、必要であれば訂正できるようにすることで、ユーザーのサインアップをサポートできます。 パスワードの入力のすべてのフィールドには、 **[パスワードの表示]** ラベルのあるチェックボックスがあります。  これにより、ユーザーはプレーン テキストでパスワードを表示できます。 セルフ アサート ページのサインアップまたはサインイン テンプレートには、次のコード スニペットを含めます。

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>利用規約の追加

次のコードを、 **[利用規約]** チェックボックスを組み込むページに含めます。 通常、このチェック ボックスは、ローカル アカウントのサインアップとソーシャル アカウント サインアップ ページで必要になります。

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

コードでは、`termsOfUseUrl` を利用規約の同意のリンクで置き換えます。 ディレクトリでは、**termsOfUse** と呼ばれる新しいユーザー属性を作成し、**termsOfUse** をユーザー属性として組み込みます。

## <a name="next-steps"></a>次のステップ

[Azure Active Directory B2C でのアプリケーションのユーザー インターフェイスのカスタマイズ](customize-ui-with-html.md)に関する記事を参照して、アプリケーションのユーザー インターフェイスをカスタマイズする方法の詳細を確認します。
