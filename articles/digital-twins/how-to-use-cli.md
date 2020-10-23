---
title: Azure Digital Twins CLI を使用する
titleSuffix: Azure Digital Twins
description: Azure Digital Twins CLI の概要と使用方法について説明します。
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 118e137f06a49f2c125b1ca156877514d65af86f
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047065"
---
# <a name="use-the-azure-digital-twins-cli"></a>Azure Digital Twins CLI を使用する

Azure portal で Azure Digital Twins インスタンスを管理できるだけでなく、Azure Digital Twins には**コマンドライン インターフェイス (CLI)** があり、次のように、このサービスを使用してほとんどの主なアクションを実行できます。
* Azure Digital Twins インスタンスの管理
* モデルの管理
* デジタル ツインの管理
* ツイン リレーションシップの管理
* エンドポイントの構成
* [ルート](concepts-route-events.md)の管理
* ロールベースのアクセス制御 (RBAC) を使用した[セキュリティ](concepts-security.md)の構成

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

## <a name="uses-deploy-and-validate"></a>使用する (デプロイと検証)

CLI は、インスタンスの一般的な管理だけでなく、デプロイと検証にも役立つツールです。
* コントロール プレーン コマンドを使用すると、新しいインスタンスのデプロイを反復可能にしたり、自動化したりすることができます。
* データ プレーン コマンドを使用すると、インスタンスの値をすばやく確認し、操作が期待どおりに完了したことを確認できます。

## <a name="get-the-extension"></a>拡張機能を取得する

Azure Digital Twins コマンドは、[Azure CLI の Azure IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)の一部です。 コマンドとその用途の完全な一覧は、`az iot` コマンド セット [*az dt* コマンド参照](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)の参照ドキュメントで参照できます。

拡張機能の最新バージョンがあることを確認するには、次の手順に従ってください。 これらのコマンドは、[Azure Cloud Shell](../cloud-shell/overview.md) または [ローカルの Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)で実行できます。

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>次のステップ

CLI とその完全なコマンド セットを、参照ドキュメント:
* [*az dt* コマンド参照](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)で確認する