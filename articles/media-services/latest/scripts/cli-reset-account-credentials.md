---
title: Azure CLI のサンプル スクリプト - アカウント資格情報のリセット | Microsoft Docs
description: Azure CLI スクリプトを使用して、アカウントの資格情報をリセットし、app.config 設定を取得します。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/11/2018
ms.author: juliako
ms.openlocfilehash: 5ec63bd8be84481780337d0b5bc2497770f22b0d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161382"
---
# <a name="cli-example-reset-the-account-credentials"></a>CLI の例: アカウント資格情報のリセット

この記事の Azure CLI スクリプトは、アカウントの資格情報をリセットし、app.config 設定を取得する方法を示しています。

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0.20 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/reset-account-credentials/Reset-Account-Credentials.sh "Reset credentials")]

## <a name="next-steps"></a>次の手順

他の例については、[Azure CLI のサンプル](../cli-samples.md)をご覧ください。
