---
title: SAS URI に関する一般的な問題と解決策 - Azure Marketplace
description: 共有アクセス署名の使用時に発生する一般的な問題と推奨される解決策。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265095"
---
# <a name="common-sas-uri-issues-and-fixes"></a>SAS URI に関する一般的な問題と解決策

> [!IMPORTANT]
> 現在、Azure 仮想マシン オファーの管理を Cloud パートナー ポータルからパートナー センターに移行しています。 オファーが移行されるまでは、Cloud パートナー ポータル向けの「[SAS URL に関する一般的な問題と解決策](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues)」の手順に従って、オファーを管理してください。

以下は、共有アクセス署名 (ソリューション用のアップロードされた VHD を識別して共有するために使用されます) を使用するときに発生する一般的な問題と、推奨される解決策です。

| **問題点** | **エラー メッセージ** | **解決策** |
| --------- | ------------------- | ------- |
| *イメージのコピーの失敗* |  |  |
| SAS URI に "?" が見つからない | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 推奨ツールを使用して SAS URI を更新します。 |
| "st" および "se" パラメーターが SAS URI にない | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 適切な**開始日**と**終了日**の値で SAS URI を更新します。 |
| "sp=rl" が SAS URI にない | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | `Read` および `List` として設定されたアクセス許可で SAS URI を更新します。 |
| SAS URI の VHD 名に空白文字がある | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | SAS URI を更新して空白文字を削除します。 |
| SAS URI の承認エラー | `Failure: Copying Images. Not able to download blob due to authorization error.` | SAS URI の形式を確認して修正します。 必要に応じて再生成します。 |
| SAS URI の "st" および "se" パラメーターで完全な日付と時刻が指定されていない | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | SAS URI の**開始日**および**終了日**パラメーター (`st` および`se` 部分文字列) は、`11-02-2017T00:00:00Z` のように、完全な日時形式になっている必要があります。 短縮バージョンは無効です (Azure CLI の一部のコマンドでは、簡略化された値が既定で生成されることがあります)。 |
|  |  |  |

詳細については、[Shared Access Signature (SAS) の使用](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)に関する記事を参照してください。
