---
title: Azure Multi-Factor Authentication のデータの保存場所
description: Azure Multi-Factor Authentication でご自身とご自身のユーザーに関してどのような個人情報と組織データが保存されているか、および発生国/地域にどのようなデータが残っているかを説明します。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25322ad9a5d57094f44ccbad312091214ae8dcac
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965286"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication のデータの保存場所と顧客データ

Microsoft 365 や Azure などの Microsoft Online サービスをサブスクライブしている場合、顧客データは Azure AD により、組織によって提供されたアドレスに基づく地理的な場所に格納されます。 ご自身の顧客のデータがどこに保存されるかについては、Microsoft セキュリティ センターの「[お客様のデータの場所](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)」を参照してください。

クラウドベースの Azure Multi-Factor Authentication と Azure Multi-Factor Authentication Server では、一定量の個人データと組織データが処理され、保存されます。 この記事では、どのようなデータがどこに保存されるのかについて、概要を説明します。

Azure Multi-Factor Authentication サービスには、米国、ヨーロッパ、アジア太平洋にデータセンターがあります。 次のアクティビティは、特に明記されている場合を除き、リージョンのデータセンターから生じたものです。

* 電話を使用する多要素認証は、米国のデータセンターから発信され、グローバル プロバイダーによってルーティングされます。
* ヨーロッパやオーストラリアなどの他のリージョンからの汎用ユーザー認証要求は、現在ユーザーの場所に基づいて処理されています。
* Microsoft Authenticator アプリを使用したプッシュ通知は、現在、ユーザーの場所に基づいて、リージョンのデータセンターで処理されています。
    * Apple プッシュ通知などのデバイス ベンダー固有のサービスは、ヨーロッパの外部にある可能性があります。

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication によって保存される個人データ

個人データは、特定のユーザーに関連付けられているユーザー レベルの情報です。 次のデータ ストアには、個人情報が含まれています。

* ブロックされたユーザー
* バイパスされたユーザー
* Microsoft Authenticator デバイス トークン変更要求
* Multi-Factor Authentication アクティビティ レポート
* Microsoft Authenticator のアクティブ化

この情報は 90 日間保持されます。

Azure Multi-Factor Authentication では、ユーザー名、電話番号、IP アドレスなどの個人データは記録されませんが、Multi-Factor Authentication での試行をユーザーに対して識別する *UserObjectId* があります。 ログ データは 30 日間保存されます。

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Azure のパブリック クラウドでは、Azure B2C 認証、NPS 拡張機能、Windows Server 2016 または 2019 AD FS アダプターを除き、次の個人データが保存されます。

| イベントの種類                           | データ ストアの種類 |
|--------------------------------------|-----------------|
| OATH トークン                           | Multi-Factor Authentication のログ内     |
| 単方向 SMS                          | Multi-Factor Authentication のログ内     |
| 音声通話                           | Multi-Factor Authentication のログ内<br />Multi-Factor Authentication アクティビティ レポート データ ストア<br />不正アクセスが報告された場合にブロックされたユーザー |
| Microsoft Authenticator 通知 | Multi-Factor Authentication のログ内<br />Multi-Factor Authentication アクティビティ レポート データ ストア<br />不正アクセスが報告された場合にブロックされたユーザー<br />Microsoft Authenticator デバイス トークン変更時の変更要求 |

> [!NOTE]
> Multi-Factor Authentication アクティビティ レポート データ ストアは、認証要求を処理するリージョンに関係なく、すべてのクラウドについて米国内に保存されます。 Microsoft Azure Germany、21Vianet が運営する Microsoft Azure、Microsoft Government Cloud には、パブリック クラウド リージョンのデータ ストアとは別の独立したデータ ストアがありますが、このデータは常に米国内に保存されます。

Microsoft Azure Government、Microsoft Azure Germany、21Vianet が運営する Microsoft Azure、Azure B2C 認証、NPS 拡張機能、Windows Server 2016 または 2019 AD FS アダプターの場合、次の個人データが保存されます。

| イベントの種類                           | データ ストアの種類 |
|--------------------------------------|-----------------|
| OATH トークン                           | Multi-Factor Authentication のログ内<br />Multi-Factor Authentication アクティビティ レポート データ ストア |
| 単方向 SMS                          | Multi-Factor Authentication のログ内<br />Multi-Factor Authentication アクティビティ レポート データ ストア |
| 音声通話                           | Multi-Factor Authentication のログ内<br />Multi-Factor Authentication アクティビティ レポート データ ストア<br />不正アクセスが報告された場合にブロックされたユーザー |
| Microsoft Authenticator 通知 | Multi-Factor Authentication のログ内<br />Multi-Factor Authentication アクティビティ レポート データ ストア<br />不正アクセスが報告された場合にブロックされたユーザー<br />Microsoft Authenticator デバイス トークン変更時の変更要求 |

### <a name="multi-factor-authentication-server"></a>Multi-Factor Authentication Server

Azure Multi-Factor Authentication Server をデプロイして実行すると、次の個人データが保存されます。

> [!IMPORTANT]
> 2019 年 7 月 1 日より、Microsoft から新しいデプロイに対して Multi-Factor Authentication Server はもう提供されません。 ユーザーからの多要素認証が必要な新しいお客様は、クラウドベースの Azure Multi-Factor Authentication を使用していただく必要があります。 7 月 1 日より前に Multi-Factor Authentication Server をアクティブ化した既存のお客様は、最新バージョンの今後の更新プログラムをダウンロードし、アクティブ化資格情報を通常どおり生成することができます。

| イベントの種類                           | データ ストアの種類 |
|--------------------------------------|-----------------|
| OATH トークン                           | Multi-Factor Authentication のログ内<br />Multi-Factor Authentication アクティビティ レポート データ ストア |
| 単方向 SMS                          | Multi-Factor Authentication のログ内<br />Multi-Factor Authentication アクティビティ レポート データ ストア |
| 音声通話                           | Multi-Factor Authentication のログ内<br />Multi-Factor Authentication アクティビティ レポート データ ストア<br />不正アクセスが報告された場合にブロックされたユーザー |
| Microsoft Authenticator 通知 | Multi-Factor Authentication のログ内<br />Multi-Factor Authentication アクティビティ レポート データ ストア<br />不正アクセスが報告された場合にブロックされたユーザー<br />Microsoft Authenticator デバイス トークン変更時の変更要求 |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication によって保存される組織のデータ

組織のデータは、構成または環境のセットアップが公開される可能性がある、テナントレベルの情報です。 次に示す Azure portal の Multi-Factor Authentication ページにあるテナント設定には、着信電話認証要求のロックアウトしきい値や発信者 ID 情報などの組織のデータが保存される場合があります。

* アカウントのロックアウト
* 不正アクセスのアラート
* 通知
* 電話の設定

また、Azure Multi-Factor Authentication Server の場合、次の Azure portal ページに組織のデータが含まれている可能性があります。

* サーバーの設定
* ワンタイム バイパス
* キャッシュ規則
* Multi-Factor Authentication Server の状態

## <a name="log-data-location"></a>ログ データの場所

ログ情報が保存される場所は、ログ情報がどのリージョンで処理されるかによって異なります。 ほとんどの地域にはネイティブの Azure Multi-Factor Authentication 機能があるため、ログ データは、Multi-Factor Authentication 要求を処理するのと同じリージョンに保存されます。 ネイティブの Azure Multi-Factor Authentication がサポートされていない地域では、米国またはヨーロッパのどちらかの地域によってサービスが提供され、ログ データは Multi-Factor Authentication 要求を処理するのと同じリージョンに保存されます。

一部の主要な認証ログ データは、米国内にのみ保存されます。 Microsoft Azure Germany と、21Vianet が運営する Microsoft Azure は、常にそれぞれのクラウドに保存されます。 Microsoft Government Cloud のログ データは、常に米国内に保存されます。

## <a name="next-steps"></a>次のステップ

クラウドベースの Azure Multi-Factor Authentication Server によって収集されるユーザー情報の詳細については、「[Azure Multi-Factor Authentication によるユーザー データの収集](howto-mfa-reporting-datacollection.md)」を参照してください。
