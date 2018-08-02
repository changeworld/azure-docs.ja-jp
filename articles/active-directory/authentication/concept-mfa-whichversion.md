---
title: Azure MFA サーバーまたは Azure MFA サービス (オンプレミスまたはクラウド) の選択
description: Azure AD 管理者は、どちらのバージョンの MFA をデプロイすべきか把握する必要があります。
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 0d68c88bdad63bb022babcc4a6ee4ee7c59ce58a
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158456"
---
# <a name="which-version-of-azure-mfa-is-right-for-my-organization"></a>所属する組織に適しているのはどちらのバージョンの Azure MFA であるかを確認しましょう。

Multi-Factor Authentication (MFA) をデプロイする場所と方法を決定する前に、3 つの基本的な質問に答える必要があります。

* [セキュリティで保護しようとしている対象](#what-am-i-trying-to-secure)
* [ユーザーが位置する場所](#where-are-the-users-located)
* [必要な機能](#what-features-do-i-need)

以下の各セクションでは、上記の質問に答えられるよう、詳しい情報を提供します。

## <a name="what-am-i-trying-to-secure"></a>セキュリティで保護しようとしている対象

適切な 2 段階認証ソリューションを選ぶにあたっては、まず追加の認証要素で保護しようとしている対象は何かという点を考える必要があります。 Azure のアプリケーションですか。 それとも、リモート アクセス システムでしょうか。 何をセキュリティで保護しようとしているかがわかれば、Multi-Factor Authentication をどこで有効にする必要があるかがわかります。

| セキュリティで保護しようとしている対象 | クラウドでの MFA | MFA サーバー |
| --- |:---:|:---:|
| ファーストパーティの Microsoft アプリ |● |● |
| アプリ ギャラリー内の SaaS アプリ |● |  |
| Azure AD アプリケーション プロキシ経由で公開される Web アプリケーション |● |  |
| Azure AD アプリケーション プロキシ経由で公開されない IIS アプリケーション  | |● |
| VPN、RDG などのリモート アクセス | ● | ● |

## <a name="where-are-the-users-located"></a>ユーザーが位置する場所

次に、組織のユーザーが位置する場所を確認します。これは、使用に適したソリューション (オンプレミスの MFA サーバーとクラウドのいずれか) の判断に役立ちます。

| ユーザーの位置 | クラウドでの MFA | MFA サーバー |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| AD FS によるフェデレーションを使用した Azure AD とオンプレミスの AD |● |● |
| Azure AD Connect を使用した Azure AD とオンプレミス AD。パスワード ハッシュ同期またはパススルー認証なし |● |● |
| Azure AD Connect を使用した Azure AD とオンプレミス AD。パスワード ハッシュ同期またはパススルー認証あり |● | |
| オンプレミスの Active Directory | |● |

## <a name="what-features-do-i-need"></a>必要な機能

次の表は、クラウドの Multi-Factor Authentication の機能と、Multi-Factor Authentication Server の機能を比較したものです。

| Feature | クラウドでの MFA | MFA サーバー |
| --- |:---:|:---:|
| 2 番目の要素としてのモバイル アプリ通知 | ● | ● |
| 2 番目の要素としてのモバイル アプリ確認コード | ● | ● |
| 第 2 要素としての音声通話 | ● | ● |
| 第 2 要素としての単方向 SMS | ● | ● |
| 第 2 要素としてのハードウェア トークン | | ● |
| MFA をサポートしていない Office 365 クライアントのアプリ パスワード | ● | |
| 認証方法の管理制御 | ● | ● |
| PIN モード | | ● |
| 不正アクセスのアラート | ● | ● |
| MFA レポート | ● | ● |
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
