---
title: Azure CLI スクリプトのサンプル - コンテナーへのファイルのアップロード | Microsoft Docs ドキュメント
description: Azure CLI スクリプトを使用して、ローカル ファイルをストレージ コンテナーにアップロードします。
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
ms.openlocfilehash: 5120d938d137efef77eeb0b69a5bf571bd4c509b
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614499"
---
# <a name="cli-example-upload-a-local-file-to-a-container"></a>CLI サンプル: コンテナーへのローカル ファイルのアップロード 

この記事の Azure CLI スクリプトでは、ローカル ファイルをストレージ コンテナーにアップロードする方法を示します。

## <a name="prerequisites"></a>前提条件 

- CLI をローカルにインストールして使用します。この記事では、Azure CLI バージョン 2.0 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

    現在、一部の [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) コマンドが Azure Cloud Shell では正常に動作しません。 CLI はローカルで使用することをお勧めします。

- [Media Services アカウントを作成する](../create-account-cli-how-to.md)

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/upload-file-asset/UploadFile-Asset.sh "Upload a file")]

## <a name="next-steps"></a>次の手順

他の例については、[Azure CLI のサンプル](../cli-samples.md)をご覧ください。
