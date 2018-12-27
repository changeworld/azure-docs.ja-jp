---
title: Azure CLI のサンプル スクリプト - ジョブの作成と送信 | Microsoft Docs
description: このトピックの Azure CLI スクリプトは、HTTPs URL を使用して、単純なエンコード変換にジョブを送信する方法を示しています。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: 11e148181568f0c550b5eb094055c21c47c00cfc
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615825"
---
# <a name="cli-example-create-and-submit-a-job"></a>CLI の例: ジョブの作成と送信

この記事の Azure CLI スクリプトは、HTTPs URL を使用して、ジョブを作成して単純なエンコード変換に送信する方法を示しています。

## <a name="prerequisites"></a>前提条件 

- CLI をローカルにインストールして使用します。この記事では、Azure CLI バージョン 2.0 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

    現在、一部の [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) コマンドが Azure Cloud Shell では正常に動作しません。 CLI はローカルで使用することをお勧めします。

- [Media Services アカウントを作成する](../create-account-cli-how-to.md)

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-jobs/Create-Jobs.sh "Create and submit jobs")]

## <a name="next-steps"></a>次の手順

他の例については、[Azure CLI のサンプル](../cli-samples.md)をご覧ください。
