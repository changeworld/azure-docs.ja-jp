<properties
	pageTitle="Azure Active Directory B2C プレビュー: 制限事項および制約事項 | Microsoft Azure"
	description="Azure Active Directory B2C に関する制限事項と制約事項のリスト"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/27/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C プレビュー: 制限事項および制約事項

Azure Active Directory (Azure AD) B2C の機能の中には、プレビューではサポートされていなかったものがあります。これらの既知の問題や制限事項の多くは、Azure AD B2C が一般公開される前に排除される予定ですが、プレビューの時点で Azure AD B2C を使用して消費者向けアプリケーションを作成する場合は注意が必要です。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Azure AD B2C テナントの作成中の問題

[Azure AD B2C テナントの作成](active-directory-b2c-get-started.md)中に問題が発生した場合は、「[Azure AD テナントまたは Azure AD B2C テナントの作成 - 問題と解決策](active-directory-b2c-support-create-directory.md)」を参照してください。

## 確認メールのブランドの問題

既定の確認メールには、Microsoft ブランドが含まれています。これは将来削除される予定です。現時点では、[会社のブランド化機能](../active-directory/active-directory-add-company-branding.md)を使用して削除できます。

## 実稼働アプリケーションのサポート

Azure AD B2C と統合されたアプリケーションを実稼働レベルのアプリケーションとして一般公開することは避けてください。現時点で Azure AD B2C はプレビュー状態であり、常に重大な変更が行われる可能性があります。サービスのサービス レベル アグリーメントも保証されません。問題が発生してもサポートを受けることはできません。リスクを理解したうえで開発段階のサービスを利用する場合は、アプリケーションまたはサービスのスコープについて検討する必要があります。@AzureAD にツイートしてください。

## アプリケーションに関する制限事項

Azure AD B2C プレビューでは現在、次のタイプのアプリケーションはサポートされていません。サポートされているアプリケーションのタイプについては、「[Azure AD B2C プレビュー: アプリケーションの種類](active-directory-b2c-apps.md)」を参照してください。

### シングル ページ アプリケーション (JavaScript)

最近多く見かけるようになったシングル ページ アプリケーション (SPA) のフロントエンドは、主に JavaScript で作成されています。AngularJS、Ember.js、Durandal などの SPA フレームワークが使われることも少なくありません。このフローは、Azure AD B2C プレビューではまだ使用できません。

### デーモン/サーバー側のアプリケーション

長時間実行されるプロセスを含んだアプリケーションや、ユーザーの介入なしで動作するアプリケーションも、セキュリティで保護されたリソース (Web API など) にアクセスする必要があります。これらのアプリケーションは、[OAuth 2.0 クライアント資格情報フロー](active-directory-b2c-reference-protocols.md#oauth2-client-credentials-grant-flow)を使用することで、(お客様の委任 ID ではなく) アプリケーションの ID を使って認証を行い、トークンを取得することができます。このフローは Azure AD B2C プレビューではまだ使用できないので、現時点では、アプリケーションは対話的なコンシューマー サインイン フローが発生した後にのみ、トークンを取得できます。

### スタンドアロン Web API

Azure AD B2C プレビューでは、[OAuth 2.0 トークンを使用して保護された Web API を作成](active-directory-b2c-apps.md#web-apis)することができます。ただし、その Web API は、同じアプリケーション ID を共有するクライアントからしかトークンを受け取ることができません。複数の異なるクライアントからアクセスされる Web API を構築することはできません。

### Web API チェーン (On-Behalf-Of)

Azure AD B2C によって保護された Web API から、同様に保護されたダウンストリームの別の Web API を呼び出す手法は、多くのアーキテクチャで使用されています。このシナリオは、バックエンドの Web API から Microsoft オンライン サービス (Azure AD Graph API など) を呼び出すネイティブ クライアントでよく見られます。

このように Web API を連鎖的に呼び出すシナリオは、OAuth 2.0 Jwt Bearer Credential Grant (On-Behalf-Of フロー) を使用してサポートできます。ただし、現時点では、Azure AD B2C プレビューに On-Behalf-Of Flow は実装されていません。

## ライブラリと SDK に関する制限事項

Azure AD B2C のプレビュー版をサポートするライブラリが存在しない言語やプラットフォームも一部存在します。認証ライブラリ群は現在、.NET、iOS、Android、および NodeJS に限定されています。それぞれに対応するクイック スタート チュートリアルについては、「[使用の開始](active-directory-b2c-overview.md#getting-started)」をご覧ください。

別の言語またはプラットフォームを使用して、アプリケーションを Azure AD B2C プレビューと統合する場合は、[OAuth 2.0 と OpenID Connect プロトコルのリファレンス](active-directory-b2c-reference-protocols.md)をご覧ください。Azure AD B2C サービスと通信するために必要な HTTP メッセージを構築する方法が説明されています。

## プロトコルに関する制限事項

Azure AD B2C プレビューでは、OpenID Connect と OAuth 2.0 がサポートされています。ただし、各プロトコルの一部の機能はまだ実装されていません。Azure AD B2C プレビューでサポートされているプロトコル機能の範囲について理解を深めるには、[OpenID Connect と OAuth 2.0 のプロトコル リファレンス](active-directory-b2c-reference-protocols.md)をご覧ください。SAML および WS-Fed プロトコルのサポートは提供されていません。

## トークンに関する制限事項

Azure AD B2C プレビューによって発行されるトークンの多くは、JSON Web トークン (JWT) として実装されます。ただし、JWT に含まれているすべての情報 ("要求" と呼ばれる) で十分というわけではなく、欠落しているものもあります。たとえば、"sub" 要求や "preferred\_username" 要求などです。ここに記載されている事柄は、プレビュー段階でかなり変更されることを想定しておく必要があります。Azure AD B2C サービスによって現在発行されているトークンについて理解を深めるには、[トークン リファレンス](active-directory-b2c-reference-tokens.md)をご覧ください。

## グループの入れ子に関する制限

Azure AD B2C テナントでは、グループのメンバーシップを入れ子にすることはできません。この機能を追加する予定はありません。

## Azure クラシック ポータルでのユーザー管理に関する問題

B2C 機能には、Azure ポータルからアクセスできます。ただし、Azure クラシック ポータルを使用して、ユーザー管理などの他のテナント機能にアクセスできます。現在、Azure クラシック ポータルには、ユーザー管理 (**[ユーザー]** タブ) に関する既知の問題がいくつかあります。

- ローカル アカウント ユーザー (電子メール アドレスとパスワード、またはユーザー名とパスワードを使用してサインアップするコンシューマー) の場合、**[ユーザー名]** フィールドはサインアップ中に使用されるサインイン ID (電子メール アドレスまたはユーザー名) に対応していません。これは、Azure クラシック ポータルに表示されるフィールドが、実際には B2C シナリオでは使用されないユーザー プリンシパル名 (UPN) であるためです。ローカル アカウントのサインイン ID を確認するには、[Graph Explorer](https://graphexplorer.cloudapp.net/) でユーザー オブジェクトを見つけます。ソーシャル アカウント ユーザー (Facebook、Google+ などを使用してサインアップするコンシューマー) についても同じ問題がありますが、その場合、特にサインイン ID はありません。

    ![ローカル アカウントの UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- ローカル アカウント ユーザーの場合、**[プロファイル]** タブでフィールドを編集したり、変更を保存したりすることはできません。この点に関しては、まもなく修正される予定です。

## Azure クラシック ポータルでの管理者によるパスワードのリセットに関する問題

管理者が Azure クラシック ポータルでローカル アカウント ベースのコンシューマーのパスワードをリセットすると (**[ユーザー]** タブの **[パスワードのリセット]** コマンド)、サインアップ ポリシーまたはサインイン ポリシーを使用している場合に、そのコンシューマーは次回サインイン時に自分のパスワードを変更できなくなり、アプリケーションからロックアウトされます。Microsoft では、この問題の解決に取り組んでいます。この問題を回避するには、[Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md) を使用してコンシューマーのパスワードを (パスワードの有効期限なしで) リセットするか、または "サインアップまたはサインイン" ポリシーではなく "サインイン" ポリシーを使用してください。

## カスタム属性の作成に関する問題

[Azure ポータルに追加されたカスタム属性](active-directory-b2c-reference-custom-attr.md)は、すぐには B2C テナントに作成されません。カスタム属性を B2C テナントに作成して Graph API から利用できる状態にするためには、あらかじめ少なくとも 1 つのポリシーの中でそのカスタム属性を使用する必要があります。

## Azure クラシック ポータルでのドメインの確認に関する問題

現在、[Azure クラシック ポータル](https://manage.windowsazure.com/)でドメインを正常に確認することはできません。Microsoft では、この問題の解決に取り組んでいます。

## Safari ブラウザーでの MFA ポリシーを使用したサインインに関する問題

Safari ブラウザーで、サインイン ポリシーに対する (MFA をオンにした) 要求が、HTTP 400 (正しくない要求) エラーで断続的に失敗します。これは、Safari の Cookie サイズ制限の値が低いためです。この問題にはいくつかの回避策があります。

- "サインイン ポリシー" ではなく "サインアップまたはサインイン ポリシー" を使用します。
- ポリシーで要求されている**アプリケーション要求**の数を減らします。

<!---HONumber=AcomDC_0629_2016-->