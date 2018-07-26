---
title: Azure MFA における 2 段階認証について | Microsoft Docs
description: Multi-Factor Authentication とは何か、なぜ MFA を使用するのか、および使用可能なさまざまな方法とバージョン。
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: d58d81d85dac7e5cd520b8d8a3fb5d91650e0cbe
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2018
ms.locfileid: "39161607"
---
# <a name="what-is-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication とは

2 段階認証は、複数の確認方法を要求することで、ユーザーのサインインとトランザクションにさらなる重要なセキュリティ レイヤーを追加する認証方法です。 これらは、次の確認方法のうち 2 つ以上を要求することで機能します。

* ユーザーが知っているもの (通常はパスワード)
* ユーザーが持っているもの (携帯電話など、簡単には複製できない信頼できるデバイス)
* ユーザー自身 (生体認証)

<center>![ユーザー名とパスワード](./media/multi-factor-authentication/pword.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![証明書](./media/multi-factor-authentication/phone.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![スマート フォン](./media/multi-factor-authentication/hware.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![スマート カード](./media/multi-factor-authentication/smart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![仮想スマート カード](./media/multi-factor-authentication/vsmart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![ユーザー名とパスワード](./media/multi-factor-authentication/cert.png)</center>

Azure Multi-Factor Authentication (MFA) は、Microsoft の 2 段階認証ソリューションです。 Azure MFA では、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することができます。 電話、テキスト メッセージ、モバイル アプリによる確認など、一連の照合方法を通じて確実な認証を行うことができます。

## <a name="why-use-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication を使う理由
インターネットの接続人口は今やかつてない水準にまで増加しています。 スマート フォン、タブレット、ノート パソコン、および PC を使用して、ユーザーはどこからでも自分のアカウントとアプリケーションにアクセスし、いつでも接続するための複数のオプションがあります。

Azure Multi-Factor Authentication は、ユーザーを保護するための 2 つ目の認証方法を提供する使いやすくスケーラブルで信頼性の高いソリューションです。

| ![使いやすい](./media/multi-factor-authentication/simple.png) | ![拡張性](./media/multi-factor-authentication/scalable.png) | ![常時保護](./media/multi-factor-authentication/protected.png) | ![信頼性](./media/multi-factor-authentication/reliable.png) |
|:---:|:---:|:---:|:---:|
| **使いやすい** |**拡張性** |**常時保護** |**信頼性** |

* **使いやすい** -Azure Multi-Factor Authentication は、簡単にセットアップして使用することができます。 ユーザーは、Azure Multi-Factor Authentication に付属している追加の保護を使用して自分のデバイスを管理できます。 多くの場合、セットアップは数回のクリックで簡単に行うことができます。
* **拡張性** - Azure Multi-Factor Authentication はクラウドの強みを活かし、オンプレミスの AD やカスタム アプリと連携します。 その保護の範囲は、ハイボリュームのミッション クリティカルなシナリオにまで広げることができます。
* **常時保護** - Azure Multi-Factor Authentication は、きわめて高い業界標準に基づく強力な認証手段です。
* **信頼性** - Microsoft では、Azure Multi-Factor Authentication の 99.9% の可用性を保証しています。 確認要求を受信することも処理することもできなくなったときに、2 段階認証のサービスは利用不可と見なされます。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]


## <a name="next-steps"></a>次の手順

- [Azure Multi-Factor Authentication のしくみ](concept-mfa-howitworks.md)を確認する

- [Azure Multi-Factor Authentication のさまざまなバージョンと使用方法](concept-mfa-licensing.md)を確認する
