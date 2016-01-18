<properties
   pageTitle="Azure Active Directory とアプリケーションの統合のファースト ステップ ガイド | Microsoft Azure"
   description="この記事は、オンプレミスのアプリケーションおよびクラウド アプリケーションと Azure Active Directory (AD) を統合するための概要ガイドです。"
   services="active-directory"
   documentationCenter=""
   authors="ihenkel"
   manager="stevenpo"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="10/16/2015"
      ms.author="inhenk"/>

# Azure Active Directory とアプリケーションの統合のファースト ステップ ガイド
## 概要
このトピックは、アプリケーションを Azure Active Directory (AD) と統合するためのロードマップを示すことを目的としています。以下の各セクションには、詳細なトピックの要点が記載されており、このファースト ステップ ガイドのどの部分が自分に必要かを特定できるようになっています。リンク先のページで各トピックの詳細を参照できます。

## 開始する前にインベントリを取得する
アプリケーションと Azure AD の統合に急いで着手する前に、現在地と目的地を知ることが重要です。次の質問は、Azure AD のアプリケーション統合プロジェクトについて考える際に役立つように設計されています。

### アプリケーション インベントリ
- すべてのアプリケーションがどこに存在するか。 どのユーザーがそのアプリケーションを所有しているか。
- どの種類の認証がアプリケーションで必要とされるか。
- どのユーザーがどのアプリケーションにアクセスする必要があるか。
- 新しいアプリケーションをデプロイする必要があるか。
  - 社内で開発して Azure コンピューティング インスタンスにデプロイするか。
  - Azure アプリケーション ギャラリーで利用できるものを使用するか。

### ユーザーとグループのインベントリ
- どこにユーザー アカウントが存在するか。
 - オンプレミスの Active Directory
 - Azure AD
 - 自社で所有する独立したアプリケーション データベース内
 - 承認されていないアプリケーション内
 - 上記のすべて
- 個々のユーザーは現在どのようなアクセス許可とロールの割り当てを所有しているか。 アクセス権を見直す必要があるか、それともユーザーのアクセス権とロールの割り当てが適切であることを確認済みか。
- オンプレミスの Active Directory でグループが既に確立されているか。
 - グループをどのように編成するか。
 - だれがグループのメンバーか。
 - グループは現在どのようなアクセス許可やロールの割り当てを所有しているか。
- 統合する前にユーザーやグループのデータベースをクリーンアップする必要があるか。 (これは非常に重要な質問です。ゴミを入れるとゴミが出てきます。)

### アクセス管理インベントリ
- アプリケーションへのユーザーのアクセスを現在どのように管理しているか。 変更する必要があるか。 [RBAC](role-based-access-control-configure.md) など、他の方法でアクセスを管理することを検討したか。
- どのユーザーが何にアクセスする必要があるか。

一部の質問にはあらかじめ回答できないこともありますが、それでもかまいません。このガイドにより、そのような質問の一部に回答し、一部に情報に基づいて判断できるようになります。

## 前提条件
- Azure サブスクリプションと Azure Active Directory のディレクトリ。Azure サブスクリプションをまだ取得していない場合は、30 日間の無料試用版を試すことができます。[実際に使ってみてください。](https://azure.microsoft.com/trial/get-started-active-directory/)

## Azure AD とのアプリケーションの統合
### 承認されていないクラウド アプリケーションを Cloud App Discovery で検出する
前に説明したように、これまで組織で管理されていなかったアプリケーションが存在する可能性があります。インベントリのプロセスの一環で、承認されていないクラウド アプリケーションを検索することができます。詳細については、[承認されていないクラウド アプリケーションを Cloud App Discovery で検出する方法](active-directory-cloudappdiscovery-whatis.md)に関するページを参照してください。

### 認証の種類
各アプリケーションにさまざまな認証の要件が存在する可能性があります。Azure AD では、証明書の署名に、パスワードによるシングル サインオンだけでなく、SAML 2.0、WS-Federation、OpenID Connect プロトコルを使用するアプリケーションを使用することができます。Azure AD で使用するアプリケーション認証の種類の詳細については、「[Azure Active Directory でのフェデレーション シングル サインオンの証明書の管理](active-directory-sso-certs.md)」と[パスワード ベースのシングル サインオン](active-directory-appssoaccess-whatis.md)に関するページを参照してください。

### Azure AD アプリケーション プロキシを使用した SSO の有効化
Microsoft Azure AD アプリケーション プロキシを使用すると、プライベート ネットワーク内に置かれたアプリケーションへの、任意の場所および任意のデバイスからのアクセスを安全に許可することができます。アプリケーション プロキシ コネクタは、環境内にインストールすると、Azure AD で簡単に構成することができます。詳細については、[Azure AD アプリケーション プロキシを使用した SSO の有効化](active-directory-appssoaccess-enable-hybrid-access.md)に関するページと [Azure AD アプリケーション プロキシ経由の新しいアプリケーションの発行](active-directory-application-proxy-configure.md)に関するページを参照してください。

### Azure AD とアプリケーションの統合
次の記事では、アプリケーションを Azure AD と統合するさまざまな方法について説明し、ガイダンスをいくつか示します。

- [使用する Active Directory の決定](active-directory-administer.md)
- [既存のアプリケーションとの統合](active-directory-sso-integrate-existing-apps.md)
- [Azure AD アプリケーション プロキシ経由の新しいアプリケーションの発行](active-directory-application-proxy-configure.md)
- [Azure アプリケーション ギャラリーのアプリケーションの使用](active-directory-appssoaccess-whatis.md/#get-started-with-the-azure-ad-application-gallery.md)
- [SaaS アプリケーションのチュートリアルの一覧の統合](active-directory-saas-tutorial-list.md)

## アプリケーションへのアクセスの管理
次の記事では、Azure AD コネクタと Azure AD を使用して Azure AD と統合した後でアプリケーションへのアクセスを管理する方法について説明します。

- [Azure AD を使用したアプリへのアクセスの管理](active-directory-managing-access-to-apps.md)
- [Azure AD コネクタを使用した自動化](active-directory-saas-app-provisioning.md)
- [アプリケーションへのユーザーの割り当て](active-directory-applications-guiding-developers-assigning-users.md)
- [アプリケーションへのグループの割り当て](active-directory-applications-guiding-developers-assigning-groups.md)
- [アカウントの共有](active-directory-sharing-accounts.md)

## カスタム アプリケーションの統合
新しいアプリケーションの作成中に、開発者が Azure AD の機能を活用できるようにサポートする必要がある場合は、[開発者ガイド](active-directory-applications-guiding-developers-for-lob-applications.md)を参照してください。

カスタム アプリケーションを Azure アプリケーション ギャラリーに追加する場合は、[Azure AD のセルフ サービス SAML 構成を使用した "ユーザー独自のアプリケーションの持ち込み"](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx) に関するページを参照してください。

<!---HONumber=AcomDC_0107_2016-->