---
title: Azure Active Directory B2C でのカスタム ポリシーの使用に関する開発者向けのメモ | Microsoft Docs
description: カスタム ポリシーで Azure AD B2C を構成および管理する開発者向けのメモ。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 31ca79976ec2ecf2755b975749e9c9150c4a49e8
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441075"
---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Azure Active Directory B2C カスタム ポリシー パブリック プレビューのリリース ノート
カスタム ポリシー機能セットは、すべての Azure Active Directory B2C (Azure AD B2C) のユーザーがパブリック プレビューで評価できるようになりました。 この機能セットは、最も複雑な ID ソリューションを構築する上級 ID 開発者を対象としています。  

現在、この機能セットを使用するために、開発者は XML ファイルを編集して直接 Identity Experience Framework を構成する必要があります。 この構成の方法は、強力で複雑です。 Identity Experience Framework を使用する上級 ID 開発者は、チュートリアルを実行したり参考ドキュメントを確認したりするための時間を取ることを計画する必要があります。 

## <a name="features-included-in-this-public-preview"></a>このパブリック プレビューに含まれている機能
パブリック プレビューで導入された新機能を使用すると、開発者は次のタスクを実行できます。<br>

* カスタム ポリシーを使用して、カスタム認証のユーザー体験を作成およびアップロードする。 
   * 要求プロバイダー間のやり取りとしてユーザー体験を順を追って記述する。 
   * ユーザー体験の条件分岐を定義する。 
* カスタム認証のユーザー体験に REST API 対応サービスを統合する。  
* OpenIDConnect 標準に準拠している ID プロバイダーとのフェデレーションを追加する。 <br>
* SAML 2.0 プロトコルに準拠している ID プロバイダーとのフェデレーションを追加する。 

## <a name="terms-of-the-public-preview"></a>パブリック プレビューの使用条件

* 新機能の使用は、評価目的のみで使用することが推奨されています。<br>
* 新機能は、運用環境での使用を目的としていません。<br>
* サービス レベル アグリーメント (SLA) は新機能には適用されません。 <br>
* サポート要求は、通常のサポート チャネルを通じて提出できます。 <br>
* 一般公開の期日は確定されていません。<br>
* Microsoft の裁量や何らかの理由により、Microsoft は、Azure AD B2C 製品が顧客 ID およびアクセス管理 (CIAM) プラットフォームとしての役割を果たすための取り決めの範囲を超えるシナリオおよびユーザー体験を停止、拒否、および制限することができます。

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>カスタム ポリシー機能セット開発者の責任
手動のポリシー構成で付与される Azure AD B2C の基になるプラットフォームへのアクセス レベルは低くなるため、独自の完全にカスタマイズ可能な信頼フレームワークが作成されます。 カスタム ID プロバイダー、信頼関係、外部サービスとの統合、ステップバイステップのワークフローの起こり得る配列により、それらを使用する上級開発者に対する要求はより厳しくなります。

パブリック プレビューを十分に活用するために、カスタム ポリシー機能セットを利用する開発者には、次の点に従うことをお勧めします。
* Identity Experience Framework とキー/シークレット管理の構成言語について理解を深める。
* シナリオおよびカスタム統合の主導権を得る。
* シナリオの体系的なテストを実行する。
* ソフトウェアの開発とステージングのベスト プラクティスに従い、少なくとも 1 つの開発/テスト環境と 1 つの運用環境を用意する。
* ID プロバイダーによる新しい開発と、統合するサービスに関する情報を常に入手する。 たとえば、シークレットの変更や、サービスの予定された変更と予定外の変更を把握します。
* アクティブな監視を設定し、運用環境の応答性を監視する。
* Azure サブスクリプションの連絡先のメール アドレスを最新に保ち、Microsoft のライブ サイト チームのメールに対応できるようにしておく。
* Microsoft ライブ サイト チームからのアドバイスがあった場合に、すぐに対処する。 

## <a name="features-by-stage-and-known-issues"></a>段階別の機能と既知の問題
カスタム ポリシー/Identity Experience Framework 機能は、継続的かつ高速に開発されています。  次の表は、機能/コンポーネントの可用性のインデックスです。

質問については、Stack Overflow ([https://aka.ms/aadb2cso](https://aka.ms/aadb2cso)) に投稿してください。


### <a name="identity-providers-tokens-protocols"></a>ID プロバイダー、トークン、プロトコル
外部コンポーネントとアプリケーションとのインターフェイス

| Feature | 開発 | プレビュー | 一般公開 | メモ |
|---------------------------------------------|-------------|---------|----|-------|
| IDP-OpenIDConnect |  | ○ |  | 例: Google+ |
| IDP-OAUTH2 |  | ○ |  | 例: Facebook  |
| IDP-OAUTH1 |  | ○ |  | 例: Twitter |
| IDP-SAML |  | ○ |  | 例: Salesforce、ADFS |
| IDP-WSFED | ○ |  |  |  |
| 証明書利用者 OAUTH |  | ○ |  |  |
| 証明書利用者 OIDC |  | ○ |  |  |
| 証明書利用者 SAML | ○ |  |  |  |
| 証明書利用者 WSFED | ○ |  |  |  |
| 基本認証と証明機関を使用した REST API |  | ○ |  | 例: Azure Functions |


### <a name="component-support"></a>コンポーネントのサポート


| Feature | 開発 | プレビュー | 一般公開 | メモ |
|-------------------------------------------|-------------|---------|----|-------|
| Azure Multi Factor Authentication |  | ○ |  |  |
| ローカル ディレクトリとしての Azure Active Directory |  | ○ |  |  |
| 2FA の Azure Email サブシステム |  | ○ |  |  |
| 複数言語のサポート|  | ○ |  |  |
| パスワードの複雑さ | ○ |  |  |  |


### <a name="content-definition"></a>コンテンツ定義

| Feature | 開発 | プレビュー | 一般公開 | メモ |
|-----------------------------------------------------------------------------|-------------|---------|----|-------|
|   エラー ページ、api.error |  | ○ |  |  |
|   IDP の選択ページ、api.idpselections |  | ○ |  |  |
|   サインアップのための IDP の選択、api.idpselections.signup |  | ○ |  |  |
|   パスワードを忘れた場合、api.localaccountpasswordreset |  | ○ |  |  |
|   ローカル アカウントのサインイン、api.localaccountsignin |  | ○ |  |  |
|   ローカル アカウントのサインアップ、api.localaccountsignup |  | ○ |  |  |
|   MFA ページ、api.phonefactor |  | ○ |  |  |
|   セルフアサート - たとえば、ソーシャル アカウントのサインアップ、api.selfasserted |  | ○ |  |  |
|   セルフアサートされたプロファイルの更新、api.selfasserted.profileupdate |  | ○ |  |  |
|   統合されたサインアップまたはサインイン ページ、api.signuporsignin |  | ○ |  |  |


### <a name="app-ief-integration"></a>アプリケーションと IEF の統合
| Feature | 開発 | プレビュー | 一般公開 | メモ |
|--------------------------------------------------|-------------|---------|----|-------------------------------------------------|
| クエリ文字列パラメーター id_token_hint | ○ |  |  |  |
| クエリ文字列パラメーター domain_hint |  | ○ |  | 要求として使用可能、IDP に渡すことができる |
| クエリ文字列パラメーター login_hint |  | ○ |  | 要求として使用可能、IDP に渡すことができる |
| client_assertion で JSON を UserJourney に挿入する | ○ |  |  | 非推奨となる予定です |
| id_token_hint として JSON を UserJourney に挿入する | ○ |  |  | JSON を渡す順方向の転送方法 |


### <a name="session-management"></a>セッションの管理

| Feature | 開発 | プレビュー | 一般公開 | メモ |
|---------------------------------|-------------|---------|----|-------|
| SSO セッション プロバイダー |  | ○ |  |  |
| 外部ログイン セッション プロバイダー |  | ○ |  |  |
| SAML SSO セッション プロバイダー |  | ○ |  |  |


### <a name="security"></a>セキュリティ
| Feature | 開発 | プレビュー | 一般公開 | メモ |
|---------------------------------------------|-------------|---------|----|-------|
| ポリシー キー - 生成、手動、アップロード |  | ○ |  |  |
| ポリシー キー - RSA/証明書、シークレット |  | ○ |  |  |


### <a name="developer-interface"></a>開発者向けインターフェイス
| Feature | 開発 | プレビュー | 一般公開 | メモ |
|---------------------------------------------|-------------|---------|----|-------|
| Azure Portal - IEF UX |  | ○ |  |  |
| Application Insights の UserJourney ログ  |  | ○ |  |  |
| Application Insights のイベント ログ |○|  |  |  |



## <a name="next-steps"></a>次の手順
[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)
