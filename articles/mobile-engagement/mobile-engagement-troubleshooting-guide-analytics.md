<properties 
   pageTitle="Azure モバイル エンゲージメント関連のトラブルシューティング ガイド - 分析" 
   description="Azure モバイル エンゲージメントにおける分析、監視、セグメント化、ダッシュボードの問題に関するトラブルシューティング" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="piyushjo"/>

# 分析、監視、セグメント化、ダッシュ ボードの問題のトラブルシューティング ガイド

次に説明するのは、Azure モバイル エンゲージメントが、アプリケーション、デバイス、ユーザーの情報を収集する方法に関して発生する可能性のある問題です。

## 情報の不足 / 遅延

### 問題
- 分析、セグメント化、ダッシュ ボードに表示される際に情報が遅延します。
- 監視によって情報が失われます。
- 分析、セグメント化、ダッシュ ボードによって情報が失われます。
- セグメント化が限界に達しています。

### 原因

- 分析 API、監視 API、セグメント API を使用して、UI で失われたデータが API で表示できるかどうかを確認できます。
- Azure モバイル エンゲージメント SDK がアプリに正しく統合されていない場合は、分析、セグメント化、監視、またはダッシュ ボードで情報を表示できません。
- セグメントは一度作成すると変更できませんが、「複製」(コピー) または「破棄」(削除) することはできます。セグメントには、10 の条件のみを含めることができます。
- 監視によって失われた情報をテストする最適な方法 (テスト デバイスのセットアップ、テスト デバイスのアプリのアンインストール / 再インストール)。
- 分析、セグメント化、ダッシュ ボードに関する情報は、24 時間ごとに更新されます。
- 新しいセグメントの情報は、(セグメントが前の情報に基づいている場合でも) 作成後 24 時間は表示されません。
- UI の分析データのフィルター処理では、アプリのバージョンにかかわらず、この型のすべての例が表示されます (たとえば、名前でフィルター処理された「Crashes」は、バージョン 1 とバージョン 2 のアプリで表示されます)。
- 分析期間はユーザーのデバイス設定の日付に基づくため、電話の日付が正しく設定されていないユーザーには、誤った期間が表示されることがあります。
- プッシュを "テスト" するボタンを使用するときは、サーバー側のデータは記録されません。データの記録は、実際のプッシュ キャンペーンでのみ行われます。

### 関連項目

- [トラブルシューティング ガイド - SDK][Link 25]、[API ドキュメント][Link 4]、[UI ドキュメント - セグメント][Link 18]

## UI で項目が見つからない

### 問題
- 特定の組み込みアプリまたはカスタム アプリの情報タグの条件に基づいて、セグメントを作成できません。
- 分析、監視、ダッシュ ボードで、組み込みアプリまたはカスタム アプリの情報タグの条件が見つかりません。
- 分析、監視、セグメント化、ダッシュ ボードでデータを解析できません。

### 原因

- 一部の組み込みアイテムやアプリ情報タグは、プッシュ条件としてのみ利用できますが、セグメントに追加したり、分析、監視、ダッシュ ボードから表示したりすることはできません。 
- セグメントに追加できない組み込みアイテムとアプリ情報タグの場合は、セグメントに基づく対象と同じ機能を実行するように、各キャンペーンの対象条件の一覧を設定する必要があります。
- 詳細については、Azure モバイル エンゲージメント UI の分析、監視、セグメント化、ダッシュ ボードの各セクションにあるコンテキスト メニューをご覧ください。

### 関連項目

- [UI ドキュメント - 対象ユーザーに新しいプッシュ条件を通知する][Link 28]
 
## トラブルシューティングのクラッシュ

### 問題
- アプリケーションのクラッシュが分析、監視、ダッシュ ボードに表示されます。

### 原因

- 分析、監視、ダッシュ ボードに表示されるアプリケーションのクラッシュをトラブルシューティングするには、以前のバージョンの SDK に関する既知の問題についてのリリース ノートをご確認ください。
- アプリケーションのクラッシュをさらにトラブルシューティングするには、インストールされているアプリケーションを使用してテスト デバイスからイベントを実行し、Azure モバイル エンゲージメント UI の [Monitor – Events (監視 - イベント)] セクションでデバイス ID を検索します。次に、アプリケーションをクラッシュさせたイベントを実行し、Azure モバイル エンゲージメント UI の [Monitor – Crash (監視 - クラッシュ)] セクションで追加情報を検索します。 

### 関連項目

- [概念 - よく寄せられる質問][Link 6]、[概念 - 用語集][Link 6]、[UI ドキュメント][Link 1]、[SDK ドキュメント - リリース ノート][Link 5]、[SDK ドキュメント - アップグレード ガイド][Link 5]

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

<!--HONumber=54--> 