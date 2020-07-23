---
title: Microsoft Authentication Library (MSAL) への移行
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library (MSAL) と Azure AD Authentication Library (ADAL) の違いと、MSAL への移行方法について学習します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/16/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: f84e479c4780437b09ab463781edbb52d8dfdfaa
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86255627"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL) へのアプリケーションの移行

多くの開発者は、Azure Active Directory Authentication Library (ADAL) を使用してアプリケーションをビルドおよびデプロイしてきました。 今後は、Azure AD エンティティの認証と承認には、Microsoft Authentication Library (MSAL) を使用することをお勧めします。

ADAL ではなく MSAL を使用することで、以下が可能になります。

- より広範な、次のような一連の ID を認証できます。
  - Azure AD ID
  - Microsoft アカウント
  - ソーシャルおよびローカル アカウント (Azure AD B2C を使用)
- ユーザーは、最高のシングル サインオン エクスペリエンスを得られます。
- アプリケーションで、増分同意を有効にすることができます。
- 条件付きアクセスのサポートが簡単になります。
- イノベーションを活用できます。 現在、Microsoft のすべての開発の取り組みは MSAL に重点を置いているため、ADAL に新しい機能は実装されません。

**MSAL は、Microsoft ID プラットフォームと併せて使用する際にお勧めの認証ライブラリです**。

## <a name="migration-guidance"></a>移行ガイダンス

次の記事は、MSAL への移行に役立ちます。

- [MSAL.Android への移行](migrate-android-adal-msal.md)
- [MSAL.iOS / macOS への移行](migrate-objc-adal-msal.md)
- [MSAL Java への移行](migrate-adal-msal-java.md)
- [MSAL.js への移行](msal-compare-msal-js-and-adal-js.md)
- [MSAL.NET への移行](msal-net-migration.md)
- [MSAL Python への移行](migrate-python-adal-msal.md)
- [ブローカーを使用する Xamarin アプリの MSAL.NET への移行](msal-net-migration-ios-broker.md)

## <a name="frequently-asked-questions-faq"></a>よく寄せられる質問 (FAQ)

__Q:ADAL は非推奨となる予定ですか?__  
A:はい。 2020 年 6 月 30 日以降、ADAL に新機能は追加されなくなります。 2022 年 6 月 30 日までは、引き続き ADAL の重要なセキュリティ修正プログラムを追加します。

__Q:どのアプリで ADAL を使用しているかを知る方法はありますか?__  
A:アプリケーションのソース コードがある場合は、上記の移行ガイドを参照して、アプリで使用しているライブラリを判別し、それを MSAL に移行する方法を確認できます。 アプリケーションのソース コードにアクセスできない場合は、[サポート リクエストを開いて](developer-support-help-options.md#open-a-support-request)、登録されているアプリケーションと各アプリケーションで使用されているライブラリの一覧を取得することができます。

__Q:既存の ADAL アプリは引き続き動作しますか。__  
A:既存のアプリは、変更せずとも引き続き動作します。 2022 年 6 月 30 日以降も運用する予定がある場合は、その安全性を確保するために MSAL に更新することを検討してください。ただし、既存の機能を維持する上で、MSAL への移行は必須ではありません。

__Q:MSAL への移行に投資すべき理由を教えてください。__  
A:MSAL には、増分同意、シングル サインオン、トークン キャッシュ管理など、ADAL には含まれない新機能が含まれています。 また、ADAL とは異なり、MSAL は 2022 年 6 月 30 日以降のセキュリティ更新プログラムを引き続き受けることができます。 [詳細については、こちらを参照してください](msal-overview.md)。

__Q:ADAL から MSAL にアプリを移行するのに役立つツールはリリースされますか?__  
A:いいえ。 ライブラリ間の違いにより、そのようなツールの開発やメンテナンスには、MSAL の改良に当てられるリソースをつぎ込む必要があります。 ただし、アプリケーションで必要な変更を行うための、前述の一連の移行ガイドを提供致します。

__Q:MSAL と AD FS の連携方法について教えてください。__  
A:MSAL.NET では、AD FS 2019 に対して認証する特定のシナリオがサポートされています。 アプリで以前のバージョンの AD FS から直接トークンを取得する必要がある場合は、ADAL 上に残す必要があります。 [詳細については、こちらを参照してください](msal-net-adfs-support.md)。

__Q:アプリケーションの移行に関するヘルプを受けるにはどうすればよいですか?__  
A:この記事の「[移行ガイダンス](#migration-guidance)」を参照してください。 アプリのプラットフォームに関するこのガイドを読んだ後も、さらに質問がある場合は、タグ `[adal-deprecation]` を使用して Stack Overflow に投稿するか、ライブラリの GitHub リポジトリで問題を開いてください。 各ライブラリのリポジトリへのリンクについては、MSAL の概要に関する記事の「[言語とフレームワーク](msal-overview.md#languages-and-frameworks)」セクションを参照してください。

## <a name="next-steps"></a>次のステップ

- [Microsoft 認証ライブラリと Microsoft Graph API を使用するようにアプリケーションを更新する](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
- [Microsoft ID プラットフォームの概要](v2-overview.md)
- [MSAL コード サンプルを確認する](sample-v2-code.md)
