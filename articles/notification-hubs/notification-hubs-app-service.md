---
title: App Service Mobile Apps との Notification Hubs の統合
description: Azure Notification Hubs と Azure App Service Mobile Apps がどのように連携するかについて説明します。
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: ''
ms.assetid: 83132dff-a01d-4b31-a426-b57496852b81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e6c4fb767e6237f390cdb467b35c323f637bebf2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "76264154"
---
# <a name="integration-with-app-service-mobile-apps"></a>App Service Mobile Apps との統合

Azure のサービス間でシームレスかつ同一のエクスペリエンスを実現するために、 [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) には Notification Hubs を使用したプッシュ通知のサポートが組み込まれています。 [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) は、拡張性が高くグローバルに使用可能な、企業の開発者やシステム インテグレーター向けのモバイル アプリ開発プラットフォームで、モバイル開発者に豊富な機能のセットを提供します。

Mobile Apps の開発者は、次のワークフローで Notification Hubs を使用します。

1. デバイスの PNS ハンドルを取得する
2. 便利な Mobile Apps クライアント SDK 登録 API を介して Notification Hubs でデバイスを登録する

    > [!NOTE]
    > Mobile Apps ではセキュリティの目的で登録時にすべてのタグを除去することに注意してください。 タグをデバイスに関連付けるには、Notification Hubs をバックエンドから直接操作します。

3. Notification Hubs を使用してアプリのバックエンドから通知を送信する

この統合は、開発者にいくつかの便利な機能を提供します。

- **Mobile Apps クライアント SDK**: これらのマルチプラットフォーム SDK は登録用のシンプルな API を提供し、モバイル アプリにリンクされている通知ハブと自動的に対話します。 開発者は Notification Hubs の資格情報を掘り下げて追加のサービスを操作する必要はありません。
  - *ユーザーへのプッシュ*: SDK が自動的に指定のデバイスを Mobile Apps の認証済みユーザー ID にタグ付けして、ユーザーへのプッシュ シナリオを有効にします。
  - *デバイスへのプッシュ*: SDK が自動的に Mobile Apps のインストール ID を Notification Hubs に登録する GUID として使用するので、開発者が複数サービスの GUID を維持する手間が省けます。
- **インストール モデル**: Mobile Apps は Notification Hubs の最新のプッシュ モデルと簡単に連携し、プッシュ通知サービスと連携する JSON インストール内のデバイスに関連付けられたすべてのプッシュ プロパティを表します。
- **柔軟性**: 開発者は統合中でも常に Notification Hubs を直接操作することを選択できます。
- **[Azure Portal](https://portal.azure.com) の統合されたエクスペリエンス**: 機能としてのプッシュが Mobile Apps に視覚的に表示され、開発者は Mobile Apps を通じて関連付けられた通知ハブを簡単に操作できます。
