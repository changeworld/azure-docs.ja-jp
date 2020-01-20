---
title: Visual Studio App Center と Azure サービスによるモバイル アプリ開発の各段階
description: モバイル アプリケーション開発の各段階と、Visual Studio App Center などの Microsoft サービスを利用してハイクラスのモバイル アプリケーションを開発する方法について説明します。
author: elamalani
ms.assetid: 12bbb070-9b3c-1234-5678-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 2693155281c6ceb9cb737960df6d3201c0983c50
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454378"
---
# <a name="different-stages-in-mobile-application-development"></a>モバイル アプリケーション開発の各段階
モバイル アプリケーションのビルドには、複数の段階があります。 Microsoft のサービス、ツール、およびテクノロジを利用して、ハイクラスのモバイル アプリケーションを簡単にビルドできます。

## <a name="app-development"></a>アプリケーション開発
Objective-C や Java などの言語を使用して、ネイティブ シングルプラットフォーム クライアント アプリケーションをビルドできます。 Xamarin、React Native、および Unity を使用して、クロスプラットフォーム アプリをビルドできます。 さらに、Cordova を使用して、ハイブリッド アプリをビルドできます。 クライアント アプリケーションを構築するには、IDE とコード エディタ (Visual Studio や Visual Studio for Mac など) を使用するか、PC や Mac 上で Visual Studio Code を使用します。

## <a name="plan-and-design-with-back-end-services"></a>バックエンド サービスを使用した計画と設計
 Microsoft では、モバイル アプリケーションに対して安全で信頼性が高く、スケーラブルなバックエンドを設定するためのさまざまなサービスを用意しています。 自分のサーバーの保守管理なしで、サーバーレス API やトリガーを作成できます。 プッシュ通知、オフライン データ同期、認証、およびデータ サービスを使用して、多様なアプリ構築シナリオをサポートする包括的かつ広範囲なモバイル バックエンドを形成できます。 
 
 バックエンド サービスの構築には、多数の要件があります。 以下を実行する必要があります。
   - デバイス上のデータ ストレージを制限する。
   - 複数のデバイス間でデータを同期させる。
   - ネットワーク接続がなくてもアプリケーションが実行されるようにする。
   - ユーザーに通知を送信する。
   - 電力の消費量を減らす。
   - 安全で信頼性が高く、スケーラブルなバックエンドを構築する。
   - サーバーのメンテナンスを自動化する。

## <a name="devops-and-continuous-monitoring"></a>DevOps と継続的な監視
クラウド中心のモバイル アプリケーションを作成するには、継続的インテグレーション (CI)、継続的デリバリー (CD)、および継続的監視サービスを活用して、すべてのプラットフォーム用のネイティブ ビルドを作成します。 多数の物理デバイス上で UI オートメーション テストを実行したり、ベータ テスト チャネルを使用してリリースを自動化したり、アプリ ストアに直接デプロイしたりできます。 アプリケーションの利用状況分析、エラー、および例外情報を収集することもできます。

## <a name="additional-services"></a>その他のサービス
ビルドされるアプリケーションの種類や性質に応じて、多数の Azure サービスを利用できます。
  - Azure Cognitive Search では、高速クエリ機能を使用して、あらゆる種類の内部データを検索できます。
  - Azure Cognitive Services では、アプリケーションで人工知能を利用できます。
  - Azure SignalR では、アプリケーションでリアルタイムの通信を可能にします。
