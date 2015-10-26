<properties
	pageTitle="Azure Active Directory B2C プレビュー: 制限事項および制約事項 | Microsoft Azure"
	description="Azure Active Directory B2C に関する制限事項と制約事項のリスト"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/28/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C プレビュー: 制限事項および制約事項

Azure Active Directory (AD) B2C の機能の中には、プレビューではサポートされていなかったものがあります。これらの制限事項の多くは、Azure AD B2C が一般公開される前に排除される予定ですが、プレビューの時点で Azure AD B2C を使用して消費者向けアプリケーションを作成する場合は注意が必要です。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Azure AD B2C ディレクトリの作成中の問題

[Azure AD B2C テナントの作成](active-directory-b2c-get-started)中に発生する可能性のある既知の問題があります。詳細については、この[記事](active-directory-b2c-support-create-directory.md)を確認してください。

## 確認電子メール ページおよびセルフサービス パスワード リセット ページでのブランド表示の問題

既定の確認電子メール ページおよびセルフサービス パスワード リセット ページには、"Microsoft" や "Azure" などのブランド表示要素が含まれます。将来的にはこれらは削除されます。これらのページのブランド表示は、[会社ブランド化機能](./active-directory/active-directory-add-company-branding.md)を使用して変更できます。そうすれば、これらのブランド表示要素は表示されなくなります。

## 実稼働アプリケーションのサポート

Azure AD B2C と統合されたアプリケーションを実稼働レベルのアプリケーションとして一般公開することは避けてください。現時点で Azure AD B2C はプレビュー状態であり、いずれ重大な変更が行われる可能性もあります。サービスの SLA も保証されません。問題が発生してもサポートを受けることはできません。リスクを理解したうえで開発段階のサービスを利用する場合は、アプリケーションまたはサービスのスコープについて検討する必要があります。@AzureAD にツイートしてください。

## アプリケーションに関する制限事項

Azure AD B2C プレビューでは現在、次のタイプのアプリケーションはサポートされていません。サポートされているアプリケーションのタイプについては、[こちらの記事](active-directory-b2c-apps)を参照してください。

### シングル ページ アプリケーション (Javascript)

最近多く見かけるようになったシングル ページ アプリケーション (SPA) のフロントエンドは、主に JavaScript で作成されています。AngularJS、Ember.js、Durandal などの SPA フレームワークが使われることも少なくありません。このフローは、Azure AD B2C プレビューではまだ使用できません。

### デーモン/サーバー側のアプリケーション

長時間実行されるプロセスを含んだアプリケーションや、ユーザーの介入なしで動作するアプリケーションも、セキュリティで保護されたリソース (Web API など) にアクセスする必要があります。これらのアプリケーションは、[OAuth 2.0 クライアント資格情報フロー](active-directory-b2c-protocols.md#oauth2-client-credentials-grant-flow)を使用することで、(お客様の委任 ID ではなく) アプリケーションの ID を使って認証を行い、トークンを取得することができます。このフローは Azure AD B2C プレビューではまだ使用できません。つまり、アプリケーションは対話的なコンシューマー サインイン フローが発生した後にのみ、トークンを取得できます。

### スタンドアロン Web API

Azure AD B2C プレビューでは、[OAuth 2.0 トークンを使用して保護された Web API を作成](active-directory-b2c-apps.md#web-apis)することができます。ただし、その Web API は、同じアプリケーション ID を共有するクライアントからしかトークンを受け取ることができません。複数の異なるクライアントからアクセスされる Web API を構築することはできません。

### Web API チェーン (On-Behalf-Of)

Azure AD B2C によって保護された Web API から、同様に保護されたダウンストリームの別の Web API を呼び出す手法は、多くのアーキテクチャで使用されています。このシナリオは、バックエンドの Web API から Microsoft Online サービス (Azure AD Graph API など) を呼び出すネイティブ クライアントでよく見られます。

このように Web API を連鎖的に呼び出すシナリオは、OAuth 2.0 Jwt Bearer Credential Grant (On-Behalf-Of Flow) を使用してサポートできます。ただし、現時点では、Azure AD B2C プレビューに On-Behalf-Of Flow は実装されていません。

## ライブラリと SDK に関する制限事項

Azure AD B2C のプレビュー版をサポートするライブラリが存在しない言語やプラットフォームも一部存在します。認証ライブラリ群は現在、.NET、iOS、Android、および NodeJS に限定されています。それぞれに対応するクイック スタート チュートリアルについては、「[使用の開始](active-directory-b2c-overview.md#getting-started)」をご覧ください。

別の言語またはプラットフォームを使用して、アプリケーションを Azure AD B2C プレビューと統合する場合は、[OAuth 2.0 と OpenID Connect プロトコルのリファレンス](active-directory-b2c-protocols.md)をご覧ください。Azure AD B2C サービスと通信するために必要な HTTP メッセージを構築する方法が説明されています。

## プロトコルに関する制限事項

Azure AD B2C プレビューでは、OpenID Connect と OAuth 2.0 がサポートされています。ただし、各プロトコルの一部の機能はまだ実装されていません。Azure AD B2C プレビューでサポートされているプロトコル機能の範囲について理解を深めるには、[OpenID Connect と OAuth 2.0 のプロトコル リファレンス](active-directory-b2c-protocols.md)をご覧ください。

## トークンに関する制限事項

Azure AD B2C プレビューによって発行されるトークンの多くは、JSON Web トークン (JWT) として実装されます。ただし、JWT に含まれているすべての情報 ("クレーム" と呼ばれる) で十分というわけではなく、欠落しているものもあります。たとえば、"sub" クレームや "preferred\_username" クレームなどです。ここに記載されている事柄は、プレビュー段階でかなり変更されることを想定しておく必要があります。Azure AD B2C サービスによって現在発行されているトークンについて理解を深めるには、[トークン リファレンス](active-directory-b2c-tokens.md)をご覧ください。

## Azure ポータルでのユーザー管理に関する問題

B2C 機能には、Azure プレビュー ポータルからアクセスできます。ただし、Azure ポータルを使用して、ユーザー管理などのその他のテナント機能にアクセスできます。現在、Azure ポータルには、ユーザー管理 (**[ユーザー]** タブ) に関する既知の問題がいくつかあります。

- ローカル アカウント ユーザー (電子メール アドレスとパスワード、またはユーザー名とパスワードを使用してサインアップするコンシューマー) の場合、**[ユーザー名]** フィールドはサインアップ中に使用されるサインイン ID (電子メール アドレスまたはユーザー名) に対応していません。これは、Azure ポータルに表示されるフィールドが、実際には B2C シナリオでは使用されないユーザー プリンシパル名 (UPN) であるためです。ローカル アカウントのサインイン ID を確認するには、[Graph Explorer](https://graphexplorer.cloudapp.net/) でユーザー オブジェクトを見つけます。ソーシャル アカウント ユーザー (Facebook、Google+ などを使用してサインアップするコンシューマー) についても同じ問題がありますが、その場合、特にサインイン ID はありません。

    ![ローカル アカウントの UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- ローカル アカウント ユーザーの場合、**[プロファイル]** タブでフィールドを編集したり、変更を保存したりすることはできません。この点に関しては、まもなく修正される予定です。

## Azure ポータルでの管理者によるパスワードのリセットに関する問題

管理者が Azure ポータルでローカル アカウント ベースのコンシューマーのパスワードをリセットすると (**[ユーザー]** タブの **[パスワードのリセット]** コマンド)、そのコンシューマーは次回サインイン時に自分のパスワードを変更できなくなり、アプリケーションからロックアウトされます。Microsoft では、この問題の解決に取り組んでいます。回避策として、[Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md) を使用してコンシューマーのパスワードをリセットします。

## Azure AD B2C ディレクトリの削除に関する制限事項

Azure ポータルで Azure AD B2C テナントを削除することはできません。

<!---HONumber=Oct15_HO3-->