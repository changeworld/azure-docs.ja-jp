---
title: Azure Static Web Apps の構成の概要
description: Azure Static Web Apps を構成するさまざまな方法について説明します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: cshoe
ms.openlocfilehash: 34a93dc1203fb404d0d6c2edfd70954545e26b15
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154928"
---
# <a name="configuration-overview"></a>構成の概要

静的 Web アプリの構成には、次のようなさまざまな概念が適用されます。

- [アプリケーション構成](./configuration.md): `staticwebapp.config.json` ファイル内で規則を定義して、アプリケーションの動作と機能を制御します。 このファイルを使用して、ルートおよびセキュリティ規則、カスタム ヘッダー、およびネットワーク設定を定義します。

- [ビルド構成](./build-configuration.md): ビルド プロセスを制御する設定を定義します。

- [アプリケーション設定](./application-settings.md): バックエンド API で使用できるアプリケーションレベルの設定と環境変数を設定します。

## <a name="example-scenarios"></a>シナリオ例

| 目的 | 手段 |
|---|---|
| ルーティング規則の定義 | [staticwebapps.config.json ファイル内で規則を作成する](./configuration.md) |
| ビルドをトリガーするブランチの設定 | [ビルド構成ファイル内で追跡対象のブランチ名を更新する](./build-configuration.md)  |
| ルートへのアクセス権を持つセキュリティ ロールの定義 | [staticwebapps.config.json ファイル内でロールを使用してルートをセキュリティで保護する](./configuration.md#securing-routes-with-roles) |
| ルートが実際のファイルと一致しない場合にどの HTML ファイルが提供されるかの設定 | [staticwebapps.config.json ファイル内でフォールバック ルートを定義する](./configuration.md#fallback-routes) |
| HTTP 要求のグローバル ヘッダーの設定 | [staticwebapps.config.json ファイル内でグローバル ヘッダーを定義する](./configuration.md#global-headers)|
| カスタム ビルド コマンドの定義 | [アプリケーション構成ファイル内でカスタム ビルド コマンド値を設定する](./build-configuration.md) |
| フロントエンド ビルドの環境変数の設定 | [ビルド構成ファイル内で環境変数を定義する](./build-configuration.md#environment-variables) |
| API の環境変数の設定 | [ポータル内でアプリケーション設定を設定する](./application-settings.md) |

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリケーションの構成](configuration.md)
