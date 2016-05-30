<properties
	pageTitle="Azure Mobile Engagement の Android SDK の統合"
	description="Android アプリで Azure Mobile Engagement SDK を統合する方法について説明します"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="piyushjo;ricksal" />

# Azure Mobile Engagement の Android SDK の統合

> [AZURE.SELECTOR]
- [ユニバーサル Windows](mobile-engagement-windows-store-sdk-overview.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
- [iOS](mobile-engagement-ios-sdk-overview.md)
- [Android](mobile-engagement-android-sdk-overview.md)

このドキュメントでは、Azure Mobile Engagement Android SDK で使用できる統合と構成のすべてのオプションについて説明します。

## 前提条件

[AZURE.INCLUDE [前提条件](../../includes/mobile-engagement-android-prereqs.md)]

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

### 4\.2.2 (05/17/2016)

- 安定性が向上しました。

### 4\.2.1 (05/10/2016)

- セキュリティ: Web ビューのローカル ファイル アクセスが無効になりました。
- セキュリティ: 廃止され、セキュリティで保護されていない `PreferenceActivity` クラスを拡張する `EngagementPreferenceActivity` クラスを削除しました。
- セキュリティ: リーチ アクティビティが `exported="false"` を使用するように記述されました。このフラグは、SDK の以前のバージョンでも使用できます。

### 4\.2.0 (03/11/2016)

- SDK が MIT でライセンスされるようになりました。
- SDK の初期化時にカスタム デバイス識別子を指定できます。

すべてのバージョンについては、[完全なリリース ノート](mobile-engagement-android-release-notes.md)をご覧ください。

## アップグレードの手順

SDK の旧バージョンをアプリケーションに既に統合している場合、[アップグレード手順](mobile-engagement-android-upgrade-procedure.md)を参照してください。

<!---HONumber=AcomDC_0518_2016-->