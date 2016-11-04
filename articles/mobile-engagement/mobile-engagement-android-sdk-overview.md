---
title: Azure Mobile Engagement の Android SDK の統合
description: Android アプリで Azure Mobile Engagement SDK を統合する方法について説明します
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''

ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal

---
# Azure Mobile Engagement の Android SDK の統合
> [!div class="op_single_selector"]
> * [ユニバーサル Windows](mobile-engagement-windows-store-sdk-overview.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
> * [iOS](mobile-engagement-ios-sdk-overview.md)
> * [Android](mobile-engagement-android-sdk-overview.md)
> 
> 

このドキュメントでは、Azure Mobile Engagement Android SDK で使用できる統合と構成のすべてのオプションについて説明します。

## 前提条件
[!INCLUDE [前提条件](../../includes/mobile-engagement-android-prereqs.md)]

## 高度な機能
### レポート機能
次の機能を追加できます。

1. [詳細なレポート オプション](mobile-engagement-android-advanced-reporting.md)
2. [場所レポート オプション](mobile-engagement-android-location-reporting.md)
3. [詳細な構成オプション](mobile-engagement-android-advanced-configuration.md)

### 通知:
[Engagement Reach を Android に統合する方法](mobile-engagement-android-integrate-engagement-reach.md)

1. Google Cloud Messaging (GCM): [GCM を Mobile Engagement に統合する方法](mobile-engagement-android-gcm-integrate.md)
2. Amazon Device Messaging (ADM): [ADM を Mobile Engagement に統合する方法](mobile-engagement-android-adm-integrate.md)

### タグ付けプランの実装:
[Engagement API を Android で使用する方法](mobile-engagement-android-use-engagement-api.md)

## リリース ノート
### 4\.2.3 (08/10/2016)
* WiFi ロックを廃止しました。
* Init の前に getDeviceId を呼び出すときのデッドロック (4.2.0 でのバグ) を修正しました。

すべてのバージョンについては、[完全なリリース ノート](mobile-engagement-android-release-notes.md)をご覧ください。

## アップグレードの手順
SDK の旧バージョンをアプリケーションに既に統合している場合、[アップグレード手順](mobile-engagement-android-upgrade-procedure.md)を参照してください。

<!---HONumber=AcomDC_0817_2016-->