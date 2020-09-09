---
title: Windows Virtual Desktop 認証 - Azure
description: Windows Virtual Desktop の認証方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8328fa9c9e32a22e8e5093aa343f333cd1914ddc
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89026119"
---
# <a name="supported-authentication-methods"></a>サポートされている認証方法

この記事では、Windows Virtual Desktop で使用できる認証の種類の概要について説明します。

## <a name="session-host-authentication"></a>セッション ホスト認証

Windows Virtual Desktop では、セッション ホスト認証に NT LAN Manager (NTLM) と Kerberos の両方がサポートされています。 ただし、クライアントで Kerberos を使用するには、ドメイン コントローラーで実行されているキー配布センター (KDC) サービスから Kerberos セキュリティ チケットを取得する必要があります。 チケットを取得するには、クライアントからドメイン コントローラーへ向かう直接の通信経路が必要になります。 企業ネットワークを使用することで、直接の通信経路を確保できます。 また、企業ネットワークへの VPN 接続を使用することもできます。

現在サポートされているサインイン方法は次のとおりです。

- Windows デスクトップクライアント
    - ユーザー名とパスワード
    - スマート カード
    - Windows Hello
- Microsoft Store クライアント
    - ユーザー名とパスワード
- Web クライアント
    - ユーザー名とパスワード
- Android
    - ユーザー名とパスワード
- iOS
    - ユーザー名とパスワード
- macOS
    - ユーザー名とパスワード

>[!NOTE]
>スマートカードと Windows Hello では、サインインに Kerberos のみが使用できます。 Kerberos でサインインするには、ドメイン コントローラーへの通信経路が必要になります。

## <a name="single-sign-on-sso"></a>シングル サインオン (SSO)

現在、Windows Virtual Desktop では認証または SSO において Active Directory フェデレーション サービス (ADFS) はサポートされていません。

セッション ホストの認証情報を要求されないようにする唯一の方法は、クライアントに保存することです。 他のユーザーがリソースにアクセスできないようにするため、これはセキュリティで保護されたデバイスを使用している場合にのみ行うことをお勧めします。

## <a name="next-steps"></a>次のステップ

デプロイをセキュリティで保護するための他の方法については、 「[セキュリティの運用方法](security-guide.md)」を確認してください。
