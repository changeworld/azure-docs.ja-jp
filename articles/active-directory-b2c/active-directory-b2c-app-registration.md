---
title: "Azure Active Directory B2C: アプリケーションの登録 | Microsoft Docs"
description: "Azure Active Directory B2C にアプリケーションを登録する方法"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: bryanla
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/13/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 541849501335fb25d96cffa81b8119adc158cdd7
ms.lasthandoff: 03/14/2017


---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: アプリケーションを登録する

> [!IMPORTANT]
> Azure Portal の Azure AD B2C ブレードから作成したアプリケーションは、同じ場所から管理する必要があります。 PowerShell または別のポータルを使用して B2C アプリケーションを編集すると、そのアプリケーションはサポート対象外となり、Azure AD B2C で動作しなくなる可能性があります。
> 
> 

## <a name="prerequisite"></a>前提条件
コンシューマーのサインアップおよびサインインを受け付けるアプリケーションを作成するには、最初にアプリケーションを Azure Active Directory B2C テナントに登録する必要があります。 「 [Azure AD B2C テナントを作成する](active-directory-b2c-get-started.md)」に書かれている手順に従って独自のテナントを取得してください。 この記事のすべての手順を実行したら、B2C 機能ブレードがスタート画面にピン留めされているはずです。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>B2C 機能ブレードに移動する
B2C 機能ブレードがスタート画面にピン留めされている場合、B2C テナントの全体管理者として [Azure ポータル](https://portal.azure.com/) にサインインするとすぐに、B2C 機能ブレードが表示されます。

また、[Azure Portal](https://portal.azure.com/) の左側のナビゲーション ウィンドウで **[その他のサービス]** をクリックし、"**Azure AD B2C**" を検索して、このブレードにアクセスすることもできます。

> [!IMPORTANT]
> B2C 機能ブレードにアクセスするには、B2Cテナントのグローバル管理者である必要があります。 他のテナントのグローバル管理者やテナントのユーザーはアクセスできません。  Azure Portal の右上隅にあるテナント スイッチャーを使用して、B2C テナントに切り替えることができます。
> 
> 

## <a name="register-a-web-application"></a>Web アプリケーションの登録
1. Azure ポータルの B2C 機能ブレードで、 **[アプリケーション]**をクリックします。
2. ブレードの上部にある **[+追加]** をクリックします。
3. アプリケーションの **[名前]** には、コンシューマーがアプリケーションの機能を把握できるような名前を設定します。 たとえば、「Contoso B2C app」などと入力します。
4. **[Include web app / web API (Web アプリ/Web API を含める)]** スイッチを **[はい]** に切り替えます。 **[応答 URL]** は、アプリケーションが要求したトークンを Azure AD B2C が返すエンドポイントです。 たとえば、「 `https://localhost:44316/`」のように入力します。
5. **[保存]** をクリックして、アプリケーションを登録します。
6. 作成したアプリケーションをクリックし、後でコードの作成時に使用するために、グローバルに一意の **アプリケーション クライアント ID** をメモしておきます。


## <a name="register-a-web-api"></a>Web API の登録
1. Azure ポータルの B2C 機能ブレードで、 **[アプリケーション]**をクリックします。
2. ブレードの上部にある **[+追加]** をクリックします。
3. アプリケーションの **[名前]** には、コンシューマーがアプリケーションの機能を把握できるような名前を設定します。 たとえば、「Contoso B2C api」などと入力します。
4. **[Include web app / web API (Web アプリ/Web API を含める)]** スイッチを **[はい]** に切り替えます。 **[応答 URL]** は、アプリケーションが要求したトークンを Azure AD B2C が返すエンドポイントです。 たとえば、「 `https://localhost:44316/`」のように入力します。
5. **[保存]** をクリックして、アプリケーションを登録します。
6. 作成したアプリケーションをクリックし、後でコードの作成時に使用するために、グローバルに一意の **アプリケーション クライアント ID** をメモしておきます。


## <a name="register-a-mobilenative-application"></a>モバイル/ネイティブ アプリケーションの登録
1. Azure ポータルの B2C 機能ブレードで、 **[アプリケーション]**をクリックします。
2. ブレードの上部にある **[+追加]** をクリックします。
3. アプリケーションの **[名前]** には、コンシューマーがアプリケーションの機能を把握できるような名前を設定します。 たとえば、「Contoso B2C app」などと入力します。
4. **[Include native client (ネイティブ クライアントを含める)]** スイッチを **[はい]** に切り替えます。
5. カスタム スキームを含めた**リダイレクト URI** を入力します。 たとえば、com.onmicrosoft.contoso.appname://redirect/path です。 [適切なリダイレクト URI](#choosing-a-redirect-uri) を選択していることを確認します。
6. **[保存]** をクリックして、アプリケーションを登録します。
7. 作成したアプリケーションをクリックし、後でコードの作成時に使用するために、グローバルに一意の **アプリケーション クライアント ID** をメモしておきます。

### <a name="choosing-a-redirect-uri"></a>リダイレクト URI の選択
モバイル/ネイティブ アプリケーションのリダイレクト URI を選択する際には、2 つの重要な考慮事項があります。 
* **一意**: リダイレクト URI のスキームは、すべてのアプリケーションで一意である必要があります。 この例 (com.onmicrosoft.contoso.appname://redirect/path) では、スキームとして com.onmicrosoft.contoso.appname を使用しています。 このパターンに従うことをお勧めします。 2 つのアプリケーションで同じスキームを共有している場合、ユーザーには [アプリの選択] ダイアログが表示されます。 ユーザーが不適切な選択を行った場合、ログインは失敗します。 
* **完全**: リダイレクト URI には、スキームとパスが必要です。 パスには、ドメインの後に&1; つ以上のスラッシュを含める必要があります (たとえば、//contoso/ は機能しますが、//contoso は失敗します)。 

## <a name="build-a-quick-start-application"></a>クイック スタート アプリケーションを構築する
アプリケーションを Azure AD B2C に登録したので、いずれかのクイック スタート チュートリアルを実行して使い始めることができます。 以下に、推奨事項をいくつか示します。

[!INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]


