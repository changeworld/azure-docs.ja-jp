---
title: Microsoft Azure Active Directory シングル サインオン プラグイン管理者ガイド | Microsoft Docs
description: Azure Active Directory と Microsoft Azure Active Directory single sign-on for JIRA の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: af949d1db8af37a534a16364f9f0763479c436e4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-admin-guide"></a>Microsoft Azure Active Directory シングル サインオン プラグイン管理者ガイド

## <a name="table-of-contents"></a>目次

1. **[概要](#overview)**
2. **[動作のしくみ](#how-it-works)**
3. **[対象ユーザー](#audience)**
4. **[想定環境](#assumptions)**
5. **[前提条件](#prerequisites)**
6. **[JIRA と Confluence のサポートされているバージョン](#supported-versions-of-jira-and-confluence)**
7. **[インストール](#installation)**
8. **[プラグインの構成](#plugin-configuration)**
9. **[アドオン構成画面のフィールドの説明](#field-explanation-for-add---on-configuration-screen:)**
10. **[トラブルシューティング](#troubleshooting)**

## <a name="overview"></a>概要

ここで説明するアドオンを使うことで、Microsoft Azure AD のユーザーは、Atlassian Jira および Confluence Server に基づく製品に、組織のユーザー名とパスワードを使ってログインできます。 SAML 2.0 ベースの SSO が実装されています。

## <a name="how-it-works"></a>動作のしくみ

ユーザーが Atlassian Jira または Confluence アプリケーションにログインしようとすると、ログイン ページに **[Login with Azure AD]\(Azure AD でログイン\)** ボタンが表示されます。 このボタンをクリックした場合、Azure AD 組織のログイン ページでログインする必要があります。

ユーザーは、認証された後、アプリケーションにログインできます。 組織 ID とパスワードで既に認証されているユーザーは、アプリケーションに直接ログインします。 また、ログインは JIRA と Confluence の間でも機能することに注意してください。 JIRA アプリケーションと Confluence の両方が同じブラウザー ウィンドウで開かれている場合、ユーザーは 1 回だけログインすればよく、他のアプリで資格情報を入力し直す必要はありません。 また、ユーザーは Azure アカウントの myapps を使って Atlassian 製品も利用でき、ログインするときに資格情報を求められることはありません。

> [!NOTE]
> このアドオンを使ってユーザーのプロビジョニングが行われることはありません。

## <a name="audience"></a>対象ユーザー

このプラグインを使って Azure AD による SSO を有効にすることを計画している JIRA および Confluence の管理者です。

## <a name="assumptions"></a>前提条件

* JIRA/Confluence のインスタンスで HTTPS が有効になっている
* JIRA/Confluence でユーザーが既に作成されている
* JIRA/Confluence で割り当てられたロールをユーザーが持っている
* プラグインを構成するために必要な情報に管理者がアクセスできる
* 会社のネットワークの外部でも JIRA/Confluence を利用できる
* アドオンが JIRA および Confluence のオンプレミス バージョンのみで動作する

## <a name="prerequisites"></a>前提条件

アドオンのインストールに進む前に、以下の前提条件に注意してください。

* JIRA/Confluence が、64 ビット バージョンのWindows にインストールされていること
* JIRA/Confluence のバージョンで HTTPS が有効であること
* 後の「サポートされるバージョン」セクションで、プラグインのサポートされているバージョンを確認すること。
* JIRA/Confluence をインターネットで使用できること。
* JIRA/Confluence の管理者の資格情報
* Azure AD の管理者の資格情報
* JIRA/Confluence で WebSudo が無効になっていること

## <a name="supported-versions-of-jira-and-confluence"></a>JIRA と Confluence のサポートされているバージョン

現時点では、次のバージョンの JIRA と Confluence がサポートされています。

* JIRA Core と JIRA Software: 6.0 から 7.2.0
* JIRA Service Desk: 3.0 から 3.2
* Confluence: 5.0 ～ 5.10

## <a name="installation"></a>インストール

管理者は、以下の手順に従って、プラグインをインストールする必要があります。

1. 管理者として、JIRA/Confluence のインスタンスにログインします
    
2. JIRA/Confluence の管理ページに移動し、[Add-ons]\(アドオン\) をクリックします。
    
3. Atlassian Market place で **Microsoft SAML SSO Plugin** を検索します
 
4. 適切なバージョンのアドオンが検索で表示されます
 
5. プラグインを選択すると、UPM が同じものをインストールします。
 
6. プラグインがインストールされると、[Manage Add-on]\(アドオンの管理\) セクションの [User Installed add-ons]\(ユーザーがインストールしたアドオン\) セクションに表示されます
 
7. 使用を開始する前に、プラグインを構成する必要があります。
 
8. プラグインをクリックすると構成ボタンが表示されます。
 
9. ボタンをクリックすると構成を入力できます
    
## <a name="plugin-configuration"></a>プラグインの構成

次の図は、JIRA と Confluence 両方のアドオン構成画面です
    
![アドオンの構成](./media/ms-confluence-jira-plugin-adminguide/jira.png)

### <a name="field-explanation-for-add-on-configuration-screen"></a>アドオン構成画面のフィールドの説明:

1.   [Metadata URL]\(メタデータ URL\): Azure AD からフェデレーション メタデータを取得するための URL です
 
2.   [Identifier]\(識別子\): Azure AD が要求のソースを検証するために使います。 これは、Azure AD の ID 要素にマップされます。 この値は、プラグインによって https://<domain:port>/ として自動的に取得されます
 
3.   [Reply URL]\(応答 URL\): IdP で応答 URL を使って SAML ログインを開始します。 これは、Azure AD の応答 URL 要素にマップされます。 この値は、プラグインによって https://<domain:port>/plugins/servlet/saml/auth として自動的に取得されます
 
4.   [Sign On URL]\(サインオン URL\): IdP でサインオン URL を使って SAML ログインを開始します。 これは、Azure AD のサインオン要素にマップされます。 この値は、プラグインによって https://<domain:port>/plugins/servlet/saml/auth として自動的に取得されます
 
5.   [IdP Entity ID]\(IdP エンティティ ID\): IdP が使うエンティティ ID です。 これは、メタデータ URL が解決されると設定されます。
 
6.   [Login URL]\(ログイン URL\): IdP からのログイン URL です。 これは、メタデータ URL が解決されると Azure AD から設定されます。
 
7.   [Log out URL]\(ログアウト URL\): IdP からのログアウト URL です。 これは、メタデータ URL が解決されると Azure AD から設定されます。
 
8.   [X.509 Certificate]\(X.509 証明書\): IdP の X.509 証明書です。 これは、メタデータ URL が解決されると Azure AD から設定されます。
 
9.   [Login Button Name]\(ログイン ボタン名\): 組織で表示するログイン ボタンの名前を指定します。 このテキストは、ログイン画面のログイン ボタンでユーザーに表示されます。
 
10.   [SAML User ID Locations]\(SAML ユーザー ID の場所\): SAML の応答でユーザー ID が格納されている場所です。 NameID またはカスタム属性名を使用できます。 この ID は JIRA/Confluence のユーザー ID である必要があります。
 
11.   [Attribute name]\(属性名\): ユーザー ID が格納されている属性の名前です。
 
12.   [Enable Home Realm Discovery]\(ホーム領域の検出を有効にする\): ADFS ベースのログインを使う場合、このフラグをオンにします。
 
13.   [Domain Name]\(ドメイン名\): ADFS ベースのログインを使う場合、ドメイン名をここに入力します
 
14.   [Enable Single Sign out]\(シングル サインアウトを有効にする\): ユーザーが JIRA/Confluence からログアウトしたら Azure AD からもログアウトさせる場合は、これをオンにします。

### <a name="troubleshooting"></a>トラブルシューティング

* 複数証明書のエラーが発生する場合
    
    * Azure AD にログインし、アプリに対して使用可能な複数の証明書を削除します。 証明書が 1 つだけであることを確認します。

* Azure AD で証明書の有効期限がまもなく切れる。
    
    * アドオンは、証明書の自動ロールオーバーを処理します。 証明書の有効期限が近づいたら、新しい証明書はアクティブとしてマークし、使われていない証明書を削除する必要があります。 この状況でユーザーが JIRA にログインしようとすると、アドオンは新しい証明書を取得して、プラグインに保存します。

* WebSudo を無効にする方法 (セキュリティで保護された管理者セッションを無効にする)
    
    * JIRA: セキュリティで保護された管理者セッション (つまり、管理機能にアクセスする前のパスワードの確認) は既定で有効になります。 JIRA インスタンスでこれを無効にする必要がある場合は、jira-config.properties ファイルで "ira.websudo.is.disabled = true" を指定することによって無効にできます
    
    * Confluence: 次の URL で示されている手順に従ってください https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html

* メタデータ URL で設定されることになっているフィールドが設定されない
    
    * URL が正しいことを確認します。 正しいテナントとアプリ ID をマップしていることを確認します。
    
    * ブラウザーから URL にアクセスし、フェデレーション メタデータ XML を受け取ることを確認します。

* 内部サーバー エラー:
    
    * インストールのログ ディレクトリにあるログを調べます。 ユーザーが Azure AD の SSO を使ってログインしようとするとエラーが発生する場合は、後で示すサポート連絡先にログを提供します。

* ユーザーがログインしようとすると、ユーザー ID が見つからないというエラーが発生する
    
    * JIRA/Confluence でユーザーが作成されていないので、同じユーザーを作成します。

* Azure AD でアプリが見つからないというエラーが発生する
    
    * 適切な URL が Azure AD のアプリにマップされていることを確認します。

* サポートの詳細: 米国の連絡先: [Azure AD SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。 24 ～ 48 業務時間内に応答します。
    
    * Azure Portal チャネルを通じて Microsoft にサポート チケットを提出することもできます。