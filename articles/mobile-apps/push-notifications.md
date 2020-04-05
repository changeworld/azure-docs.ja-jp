---
title: Visual Studio App Center と Azure Notification Hubs によるモバイル アプリのプッシュ通知の重要性
description: モバイル アプリケーション ユーザーとの連携に使用できる Visual Studio App Center などのサービスについて説明します。
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: bc59857cdb843dfff54e69f2226f13cfe70df8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235324"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>プッシュ通知の送信でアプリケーション ユーザーとつながる

消費者向けでも法人向けでも、アプリケーションを開発するなら、ユーザーにはそのアプリケーションを積極的に利用して欲しいものです。 ニュース速報、ゲームや製品のアップデート、うれしい特価セールなど、他のあらゆる関連情報をユーザーと共有したい状況がしばしばあります。 ユーザーとのつながりを増やし、アプリケーションを繰り返し利用してもらうには、リアルタイムでユーザーに情報を伝える方法が必要になります。

プッシュ通知は、アプリケーション ユーザーに情報を伝える速くて効率的な手法です。 ユーザーがモバイル デバイスでそのとき何をしているのかに関係なく、通常、ポップアップ項目やダイアログ ボックスで、時機を逸せずにユーザーが望む情報を届けることができます。

## <a name="value-of-push-notifications"></a>プッシュ通知の価値
プッシュ通知により、ユーザーと企業に価値が提供されます。

企業は次のことができます。
- サポートしているプラットフォームに関するメッセージを、時機を逸せずにユーザーに直接伝えます。
- アップデートやリマインダーをユーザーにリアルタイムで送信し、アプリケーションを定期的に使用するように奨励することで、ユーザー エンゲージメントを高めます。
- ユーザーを維持し、アプリケーションをダウンロードしたが使用していない非アクティブ ユーザーには再び積極的に使用してもらいます。
- ユーザーの行動を分析し、セグメントに分け、モバイル プッシュ通知を基盤とするキャンペーンを活用することで転換率を上げます。
- プッシュ メッセージやタイミングの良いアップデートをユーザーに送信することで、製品やサービスを宣伝します。
- 個々のユーザーに合わせたプッシュ メッセージを送信することで、ユーザーをターゲットに定めます。

アプリケーション ユーザーの場合のプッシュ通知:
- リアルタイムで価値や情報を届けます。
- ユーザーにリマインダーを送信し、アプリケーションを使用してもらいます。

次のサービスを使用し、モバイル アプリでプッシュ通知を有効にします。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Push](/appcenter/push/) サービスでは、ターゲットを定めたメッセージを iOS、Android、Windows ユーザーに送信できます。プッシュ通知サービス (PNS) を使用してデバイスに通知を送信するプロセスを管理する必要はありません。 このサービスは Azure Notification Hubs を基盤として構築されており、強力なダッシュボードを提供することで、通知を手動でプッシュする場合に付随する複雑な作業をなくします。

**主な機能**
- プラットフォームの種類を問わず、モバイル デバイスにプッシュ通知を送信します。
- 通知を利用してデータをアプリケーションに送信したり、ユーザーに宛てたメッセージを表示したり、アプリケーションでアクションをトリガーしたりします。
- 通知を使用して次の操作を実行します。 
    - 登録されているすべてのデバイスにメッセージをブロードキャストします。
    - デバイス情報とカスタム プロパティに基づいて、対象ユーザーに通知を送信します。
    - 特定のユーザーに通知を送信します。
    - 特定のデバイスに通知を送信します。
- App Center ポータルで使用できる、プッシュ、デバイス、およびエラーに対する豊富なテレメトリを活用します。
- iOS、Android、macOS、Xamarin、React Native、Unity、および Cordova のプラットフォーム サポートを利用できます。

**参照**
- [Visual Studio App Center にサインアップする](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center Push を使ってみる](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure 通知ハブ
[Notification Hubs](/azure/notification-hubs/notification-hubs-push-notification-overview) では、スケールアウトされた使いやすいプッシュ エンジンが提供されています。 このエンジンを使用して、クラウドまたはオンプレミスで、あらゆるプラットフォームからあらゆるバックエンドに通知を送信できます。

**主な機能**
- クラウドまたはオンプレミスで、あらゆるプラットフォームからあらゆるバックエンドにプッシュ通知を送信します。
- 1 回の API 呼び出しで、数百万単位のモバイル デバイスにプッシュを高速配信します。
- 顧客、言語、場所に合わせてプッシュ通知を作成します。
- 顧客セグメントを動的に定義し、それに通知します。
- 数百万単位のモバイル デバイスに合わせて一瞬でスケーリングします。
- iOS、Android、Windows、Kindle、および Baidu のプラットフォーム サポートを利用できます。
        
**参照**
- [Azure Portal](https://portal.azure.com) 
- [Azure Notification Hubs の使用](/azure/notification-hubs/)
- [クイック スタート](/azure/notification-hubs/create-notification-hub-portal)
- [サンプル](/azure/notification-hubs/samples)
