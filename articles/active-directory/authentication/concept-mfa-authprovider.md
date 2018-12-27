---
title: Azure Multi-Factor Auth プロバイダーをいつどのように使用するか
description: どのようなときに Azure MFA 認証プロバイダーを使用するかについて説明します。
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 8e77a33667bd6794f667348958e0edb9c6a8fb0d
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094979"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Azure Multi-Factor Authentication プロバイダーをいつ使用するか

Azure Active Directory を持つグローバル管理者と Office 365 ユーザーは、既定で 2 段階認証を使用できます。 ただし、[高度な機能](howto-mfa-mfasettings.md)が必要な場合は、通常版の Multi-Factor Authentication (MFA) をご購入ください。

Azure Multi-Factor Authentication プロバイダーは、Azure Multi-Factor Authentication が提供する機能を、**ライセンスを持っていない**ユーザーが利用できるようにするために使用されます。

社内の全ユーザーを対象としたライセンスを所有している場合、Azure Multi-Factor Auth プロバイダーは必要ありません。 Azure Multi-Factor Authentication プロバイダーの作成が必要になるのは、ライセンスが割り当てられていないユーザーに追加で 2 段階認証を適用しなければならないケースのみです。

> [!NOTE]
> 2018 年 9 月 1 日以降、新しい認証プロバイダーを作成できなくなります。 既存の認証プロバイダーは引き続き使用および更新できます。 多要素認証認証は、今後も Azure AD Premium ライセンスで利用できます。

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Azure MFA SDK に関する注意事項

SDK をダウンロードする場合は、Azure 多要素認証プロバイダーが必要です。 この SDK は非推奨となっており、新規のお客様に対するサポートは現在行っておりません。正常に機能するのは、2018 年 11 月 14 日までとなります。 その後は、SDK への呼び出しは失敗します。

SDK をダウンロードするには、Azure MFA、AAD Premium、またはその他のバンドル ライセンスを所有していても、Azure Multi-Factor Auth プロバイダーを作成する必要があります。 既にライセンスがある状態で、SDK のダウンロードのために Azure 多要素認証プロバイダーを作成する場合には、プロバイダーの作成に **[有効化されたユーザーごと]** モデルを使用してください。 プロバイダーを作成したら、Azure MFA、Azure AD Premium、またはその他のバンドル ライセンスが保存されているディレクトリにリンクします。 この構成により、所有しているライセンス数よりも 2 段階認証を実行する一意のユーザーの数が多い場合でも、適切な課金が行われます。

## <a name="what-is-an-mfa-provider"></a>MFA プロバイダーとは

2 種類の認証プロバイダーがあり、違いは Azure サブスクリプションの課金方法です。 認証ごとのオプションは、1 か月間にテナントに対して実行された認証の数を計算します。 このオプションは、ときどき認証を行うユーザーが多数いる場合に最適です。 ユーザーごとのオプションは、1 か月間に 2 段階認証を実行するテナント内の個人の数を計算します。 このオプションは、ライセンスを持つユーザーはいますが、ライセンス制限を超えてより多くのユーザーに MFA を拡張する必要がある場合に最適です。

## <a name="manage-your-mfa-provider"></a>MFA プロバイダーの管理

MFA プロバイダーの作成後に使用モデル (有効化されたユーザーごと、または認証ごと) を変更することはできません。

MFA が有効化されているすべてのユーザーに対応できる、十分な数のライセンスを購入している場合は、MFA プロバイダーをすべて削除することもできます。

MFA プロバイダーが Azure AD テナントにリンクされていない場合、または新しい MFA プロバイダーを別の Azure AD テナントにリンクする場合、ユーザー設定と構成オプションは転送されません。 また、既存の Azure MFA サーバーは、MFA プロバイダーによって生成されるアクティブ化資格情報を使用して再アクティブ化する必要があります。 MFA Server を MFA プロバイダーにリンクするために再アクティブ化しても、電話呼び出しやテキスト メッセージによる認証には影響ありませんが、モバイル アプリ通知は、各ユーザーがモバイル アプリを再アクティブ化するまで機能しなくなります。

## <a name="next-steps"></a>次の手順

[Multi-Factor Authentication の設定を構成する](howto-mfa-mfasettings.md)
