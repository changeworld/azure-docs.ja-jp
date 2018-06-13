---
title: Azure MFA クラウドかサーバーかの選択 | Microsoft Docs
description: 保護しようとしている対象とユーザーが位置する場所をたずねることで、ユーザーに最適な多要素認証セキュリティ ソリューションを選択します。
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 10/02/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 8314d72aa2cc6787d3f65dd48cd693a0ac332c0a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2018
ms.locfileid: "33866354"
---
# <a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>ニーズに応じた Azure Multi-Factor Authentication ソリューションを選択する
Azure Multi-Factor Authentication (MFA) にはさまざまな種類があるため、使用に適したバージョンを特定するには、いくつかの項目を検討する必要があります。  その項目とは、以下の 3 つです。

* [セキュリティで保護しようとしている対象](#what-am-i-trying-to-secure)
* [ユーザーが位置する場所](#where-are-the-users-located)
* [必要な機能](#what-features-do-i-need)

以下のセクションでは、ここに挙げた項目の判断に役立つガイダンスを提供します。

## <a name="what-am-i-trying-to-secure"></a>セキュリティで保護しようとしている対象
適切な 2 段階認証ソリューションを選ぶにあたっては、まず 2 番目の認証方法で保護しようとしている対象は何かという点を考える必要があります。  Azure のアプリケーションですか。  それとも、リモート アクセス システムでしょうか。  何をセキュリティで保護しようとしているかがわかれば、Multi-Factor Authentication をどこで有効にする必要があるかがわかります。  

| セキュリティで保護しようとしている対象 | クラウドでの MFA | MFA サーバー |
| --- |:---:|:---:|
| ファーストパーティの Microsoft アプリ |● |● |
| アプリ ギャラリー内の SaaS アプリ |● |  |
| Azure AD アプリケーション プロキシ経由で公開される Web アプリケーション |● |  |
| Azure AD アプリケーション プロキシ経由で公開されない IIS アプリケーション  | |● |
| VPN、RDG などのリモート アクセス | ● | ● |

## <a name="where-are-the-users-located"></a>ユーザーが位置する場所
次に、ユーザーが位置する場所を検討します。これは、使用する適切なソリューション (具体的には、クラウドの Multi-Factor Authentication を採用するか、それともオンプレミスで MFA Server を使用するか) の判断に役立ちます。

| ユーザーの位置 | クラウドでの MFA | MFA サーバー |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| AD FS によるフェデレーションを使用した Azure AD とオンプレミスの AD |● |● |
| DirSync、Azure AD Sync、Azure AD Connect を使用した Azure AD とオンプレミスの AD。パスワード ハッシュ同期またはパススルー認証なし |● |● |
| DirSync、Azure AD Sync、Azure AD Connect を使用した Azure AD とオンプレミスの AD。パスワード ハッシュ同期またはパススルー認証あり |● | |
| オンプレミスの Active Directory | |● |

## <a name="what-features-do-i-need"></a>必要な機能
次の表は、クラウドの Multi-Factor Authentication の機能と、Multi-Factor Authentication Server の機能を比較したものです。

| Feature | クラウドでの MFA | MFA サーバー |
| --- |:---:|:---:|
| 2 番目の要素としてのモバイル アプリ通知 | ● | ● |
| 2 番目の要素としてのモバイル アプリ確認コード | ● | ● |
| 第 2 要素としての音声通話 | ● | ● |
| 第 2 要素としての単方向 SMS | ● | ● |
| 第 2 要素としての双方向 SMS | | ●  (非推奨)| 
| 第 2 要素としてのハードウェア トークン | | ● |
| MFA をサポートしていない Office 365 クライアントのアプリ パスワード | ● | |
| 認証方法の管理制御 | ● | ● |
| PIN モード | | ● |
| 不正アクセスのアラート |● | ● |
| MFA レポート |● | ● |
| ワンタイム バイパス | | ● |
| 音声通話のカスタムあいさつ文 | ● | ● |
| 音声通話のカスタマイズ可能な発信元 ID | ● | ● |
| 信頼できる IP | ● | ● |
| 信頼済みデバイスの MFA の記憶 | ● | |
| 条件付きアクセス | ● | ● |
| キャッシュ |  | ● |

## <a name="next-steps"></a>次の手順

クラウドの Azure Multi-Factor Authentication とオンプレミスの MFA Server の違いがわかったら、Azure Multi-Factor Authentication を実際にセットアップして使用してみましょう。 **シナリオに応じてアイコンをクリックしてください。**

<center>

[![クラウドの MFA](./media/concept-mfa-whichversion/cloud2.png)](howto-mfa-getstarted.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [![MFA サーバー](./media/concept-mfa-whichversion/server2.png)](howto-mfaserver-deploy.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </center>
