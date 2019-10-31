---
title: Visual Studio App Center と Azure Notification Hubs によるモバイル アプリのプッシュ通知の重要性
description: App Center など、モバイル アプリケーション ユーザーとつながることを可能にするサービスについて説明します。
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 05a9bc6ccd26aaecd3e0bf615880e0543b8604f7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795215"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>プッシュ通知の送信でアプリケーション ユーザーとつながる 

消費者向けでも法人向けでも、アプリケーションを開発するなら、ユーザーにはそのアプリケーションを積極的に利用して欲しいものです。 ニュース速報、ゲームや製品のアップデート、うれしい特価セールなど、あらゆる関連情報をユーザーと共有したい状況がしばしばあります。 ユーザーとのつながりを増やし、アプリケーションを頻繁に利用してもらうには、リアルタイムでユーザーに情報を伝える方法が必要になります。

プッシュ通知は、アプリケーション ユーザーに情報を伝える速くて効率的な手法です。 ユーザーがモバイル デバイスでそのとき何をしているのかに関係なく、通常、ポップアップやダイアログ ボックスで、時機を逸せずにユーザーが望む情報を届けることができます。

## <a name="value-of-push-notifications"></a>プッシュ通知の価値
プッシュ通知は、エンド ユーザーと企業の両方に価値をもたらします。

企業は次のことができます。
- サポートしているプラットフォームに関するメッセージを時機を逸せずに**ユーザーに直接、伝える**ことができます。
- アップデートやリマインダーをユーザーにリアルタイムで送信し、アプリケーションを定期的に使用するように奨励することで**ユーザー エンゲージメントを高めます**。
- **ユーザーを維持**し、アプリケーションをダウンロードしたが使用していない非アクティブ ユーザーには再び積極的に使用してもらいます。
- エンドユーザーの行動を分析し、セグメントに分け、モバイル プッシュ通知を基盤とするキャンペーンを活用することで**転換率**を上げます。
- プッシュ メッセージやタイミングの良いアップデートをユーザーに送信することで、**製品やサービスを宣伝**します。
- 個々のユーザーに合わせたプッシュ メッセージを送信することで**ユーザーをターゲットに定めます**。

アプリケーション ユーザーの場合のプッシュ通知:
- リアルタイムで**価値や情報を届けます**。
- **ユーザーにリマインダーを送信**し、アプリケーションを使用してもらいます。

次のサービスを使用し、モバイル アプリでプッシュ通知を有効にします。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Push](/appcenter/push/) サービスでは、ターゲットを定めたメッセージを iOS、Android、Windows ユーザーに送信することでユーザーとのつながりを増やすことができます。プッシュ通知サービス (PNS) を使用してデバイスに通知を送信するプロセスを管理する必要がありません。 このサービスは Azure Notification Hubs を基盤として構築されており、強力なダッシュボードを提供することで、通知を手動でプッシュする場合に付随する複雑な作業をなくします。

**主な機能**
- プラットフォームの種類を問わず、**モバイル デバイスにプッシュ通知を送信します**。
- 通知を利用してデータをアプリケーションに送信したり、ユーザーに宛てたメッセージを表示したり、アプリケーションでアクションをトリガーしたりします。
- 通知の対象: 
    - **登録されているすべてのデバイス**にブロードキャスト メッセージを送信します。
    - デバイス情報とカスタム プロパティに基づいて構築された**対象ユーザー**に通知を送信します。
    - **特定のユーザー**に通知を送信します。
    - **特定のデバイス**に通知を送信します。
- プッシュ、デバイス、エラーに関する**豊富な利用統計情報**が App Center ポータルで利用できます。
- **プラットフォーム サポート** - iOS、Android、macOS、Xamarin、React Native、Unity、Cordova。

**参照**
- [App Center にサインアップする](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center Push を使ってみる](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure 通知ハブ
[Notification Hubs](/azure/notification-hubs/notification-hubs-push-notification-overview) から与えられるスケールアウトされた使いやすいプッシュ エンジンでは、任意のバックエンド (クラウドまたはオンプレミス) から任意のプラットフォームに通知を送信できます。

**主な機能**
- クラウドまたはオンプレミスで。あらゆるプラットフォームからあらゆるバックエンドに**プッシュ通知を送信します**。
- 1 回の API 呼び出しで、数百万単位のモバイル デバイスにプッシュを**高速配信**します。
- 顧客、言語、場所に合わせてプッシュ通知を作成します。
- **顧客セグメント**を動的に定義し、それに通知します。
- 数百万単位のモバイル デバイスに合わせて**一瞬でスケーリングします**。
- **プラットフォーム サポート** - iOS、Android、Windows、Kindle、Baidu。
        
**参照**
- [Azure Portal](https://portal.azure.com) 
- [Azure Notification Hubs の使用](/azure/notification-hubs/)   
- [クイック スタート](/azure/notification-hubs/create-notification-hub-portal)
- [サンプル](/azure/notification-hubs/samples)
