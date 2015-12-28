<properties
	pageTitle="アプリ モデル 2.0 の制約事項 | Microsoft Azure"
	description="Azure AD v2.0 アプリ モデルの一連の制限事項について記載しています。"
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/09/2015"
	ms.author="dastrock"/>

# アプリ モデル v2.0 プレビュー: 制限事項

v2.0 アプリ モデルのいくつかの機能は、パブリック プレビューの時点ではまだサポートされていません。いずれの制限も v2.0 アプリ モデルが一般公開リリースとなる前に排除される予定ですが、パブリック プレビューの時点でアプリ開発を行う場合は注意が必要です。

> [AZURE.NOTE]この情報は、v2.0 アプリ モデルのパブリック プレビューに関するものです。一般公開されている Azure AD サービスとの統合手順については、「[Azure Active Directory 開発者ガイド](active-directory-developers-guide.md)」を参照してください。

## 実稼働アプリのサポート
v2.0 アプリ モデルに統合されたアプリを実稼働レベルのアプリとして一般リリースすることは避けてください。現時点で v2.0 アプリ モデルはパブリック プレビューとなっています。いずれ重大な変更が行われる可能性もあります。サービスの SLA も保証されません。問題が発生してもサポートを受けることはできません。リスクを承知のうえで開発段階のサービスを利用する場合は、アプリまたはサービスのスコープについて検討する必要があります。@AzureAD にご連絡ください。

## アプリに関する制限事項
v2.0 アプリ モデルのパブリック プレビューでは現在、次のタイプのアプリはサポートされていません。サポートされているアプリのタイプについては、[こちらの記事](active-directory-v2-flows.md)を参照してください。

##### シングル ページ アプリ (JavaScript)
最近多く見かけるようになったシングル ページ アプリのフロントエンドは、主に JavaScript で作成されています。AngularJS、Ember.js、Durandal などの SPA フレームワークが使われることも少なくありません。一般公開される Azure AD サービスでは、これらのアプリが [OAuth 2.0 Implicit Flow](active-directory-v2-protocols.md#oauth2-implicit-flow) を使ってサポートされます。しかし v2.0 アプリ モデルでは、このフローがまだ利用できません。サポートされるまでには、あと少し時間がかかります。

今すぐに v2.0 アプリ モデルで SPA を動作させる必要がある場合は、[Web アプリ フロー](active-directory-v2-flows.md#web-apps)を使用して認証を実装してください。ただしこの方法は推奨されません。そのような使い方に関するドキュメントも限られています。SPA の使い方について感触をつかむことが目的であれば、[一般提供版 Azure AD の SPA コード サンプル](active-directory-devquickstarts-angular.md)を参照してください。

##### デーモン/サーバー サイド アプリ
長時間実行されるプロセスを含んだアプリや、ユーザーの介入なしで動作するアプリも、セキュリティで保護されたリソース (Web API など) にアクセスする必要があります。これらのアプリは、[OAuth 2.0 クライアント資格情報フロー](active-directory-v2-protocols.md#oauth2-client-credentials-grant-flow)を使用することで、(ユーザーの委任 ID ではなく) アプリの ID を使って認証を行い、トークンを取得することができます。

v2.0 アプリ モデルでは、このフローが現在サポートされていません。つまりアプリがトークンを取得できるのは、対話的なユーザー サインイン フローの後に限られます。近い将来、クライアント資格情報フローが追加されます。一般提供版 Azure AD アプリ モデルでのクライアント資格情報フローについては、[GitHub のデーモン サンプル](https://github.com/AzureADSamples/Daemon-DotNet)を参照してください。

##### Web API の連鎖 (On-Behalf-Of)
v2.0 アプリ モデルによって保護された Web API から、同じように保護された別の下流の Web API を呼び出す手法は、多くのアーキテクチャで使われています。このシナリオは、バックエンドの Web API から Microsoft Online サービス (Office 365、Graph API など) を呼び出すネイティブ クライアントでよく見られます。

このように Web API を連鎖的に呼び出すシナリオは、OAuth 2.0 Jwt Bearer Credential Grant ([On-Behalf-Of フロー](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow)) を使用してサポートできます。しかし現時点では v2.0 アプリ モデル プレビューに On-Behalf-Of フローが実装されていません。一般提供版 Azure AD サービスにおけるこのフローの動作については、[GitHub の On-Behalf-Of コード サンプル](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet)を参照してください。

##### スタンドアロン Web API
v2.0 アプリ モデルのプレビュー版では、v2.0 エンドポイントからの [OAuth トークンをセキュリティに使用する Web API を構築](active-directory-v2-flows.md#web-apis)することができます。しかし、その Web API は、同じアプリケーション ID を共有するクライアントからしかトークンを受け取ることができません。複数の異なるクライアントからのアクセスを受け付けるサード パーティ Web サービスを構築することはできません。

同じアプリ ID を持った既知のクライアントからトークンを受け付ける Web API の構築方法については、[こちら](active-directory-appmodel-v2-overview.md#getting-started)のセクションに掲載されている v2.0 アプリ モデルの Web API サンプルを参照してください。

## ユーザーに関する制限事項
現在、v2.0 アプリ モデルで構築されたすべてのアプリは、Microsoft アカウントまたは Azure AD アカウントを持つすべてのユーザーに公開されます。どちらかのタイプのアカウントを持つユーザーであれば、対象のアプリにアクセス (またはアプリをインストール) して v2.0 アプリ モデルで資格情報を入力し、アプリのアクセス許可要求に同意することができます。特定のユーザーからのサインインを拒否するようにアプリのコードを記述することはできますが、それらのユーザーがアプリに同意することまでは防止できません。

実質的には、アプリにサインインできるユーザーの種類を制限することができません。(1 つの組織内のユーザーに限定される) 基幹業務アプリ、(Azure AD アカウントを持った) 社内ユーザーのみが利用できるアプリ、(Microsoft アカウントを持つ) コンシューマーのみが利用できるアプリを構築することはできません。

## アプリの登録に関する制限事項
現時点では、v2.0 アプリ モデルに統合するアプリはすべて、[apps.dev.microsoft.com](https://apps.dev.microsoft.com) で新しいアプリ登録を作成する必要があります。既存の Azure AD アプリまたは Microsoft アカウント アプリは、v2.0 アプリ モデルとの互換性がありません。新しいアプリ登録ポータル以外のポータルで登録されたアプリも同様です。一般提供版の Azure AD サービスから v2.0 アプリ モデルへのアプリの移行パスはありません。

同様に、新しいアプリ登録ポータルで登録されたアプリは、v2.0 アプリ モデル専用となります。アプリ登録ポータルで、Azure AD または Microsoft アカウント サービスと適切に連携するアプリを作成することはできません。

現在、新しいアプリケーション登録ポータルで登録されるアプリは、ごく限られた redirect\_uri 値に限定されています。Web アプリと Web サービスの redirect\_uri はスキーム (`https`) で始める必要があります。それ以外のすべてのプラットフォームの redirect\_uri は、`urn:ietf:oauth:2.0:oob` という値をハードコーディングする必要があります。

新しいアプリケーション登録ポータルでアプリを登録する方法については、[こちらの記事](active-directory-v2-app-registration.md)を参照してください。

## サービスと API に関する制限事項
v2.0 アプリ モデルは現在、[サポートされる認証フロー](active-directory-v2-flows.md)に該当することを条件として、新しいアプリケーション登録ポータルで登録されたアプリのサインインをサポートしています。ただしこれらのアプリで取得できるのは、ごく限られたリソースの OAuth 2.0 アクセス トークンだけです。v2.0 エンドポイントから発行される access\_tokens の対象は次のいずれかに限定されます。

- トークンを要求したアプリ。論理的に複数の異なるコンポーネントまたは階層で構成されている場合にアプリが取得できるのはそれ自身の access\_token となります。その実際のシナリオについては、[こちら](active-directory-appmodel-v2-overview.md#getting-started)のチュートリアルを参照してください。
- Outlook メール、カレンダー、連絡先の REST API (いずれも https://outlook.office.com に存在)。これらの API にアクセスするアプリの作成方法については、[Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) に関するチュートリアルを参照してください。

その他の Microsoft Online Services や独自の Web API およびサービスについても近い将来、サポートが追加される予定です。

## ライブラリと SDK に関する制限事項
v2.0 アプリ モデルのプレビュー版をサポートするライブラリが存在しない言語やプラットフォームも一部存在します。認証ライブラリ群は現在、.NET、iOS、Android、NodeJS、JavaScript に限られます。それぞれに対応するコード サンプルとチュートリアルについては、[こちら](active-directory-appmodel-v2-overview.md#getting-started)のセクションを参照してください。

別の言語またはプラットフォームを使用して v2.0 アプリ モデルにアプリを統合することをご希望の場合は、[OAuth 2.0 と OpenID Connect プロトコルのリファレンス](active-directory-v2-protocols.md)を参照してください。v2.0 エンドポイントと通信するために必要な HTTP メッセージを構築する方法が説明されています。

## プロトコルに関する制限事項
v2.0 アプリ モデルは、Open ID Connect および OAuth 2.0 をサポートしています。ただし、各プロトコルの一部の機能がまだ v2.0 アプリ モデルには組み込まれていません。次に例をいくつか示します。

- OpenID Connect `prompt` パラメーターの完全サポート
- OpenID Connect `login_hint` パラメーター
- OpenID Connect `domain_hint` パラメーター
- OpenID Connect `end_sesssion_endpoint`

v2.0 アプリ モデルでサポートされるプロトコル機能の範囲について、より詳細な情報については、[OpenID Connect と OAuth 2.0 のプロトコル リファレンス](active-directory-v2-protocols.md)を参照してください。

<!---HONumber=AcomDC_1217_2015-->