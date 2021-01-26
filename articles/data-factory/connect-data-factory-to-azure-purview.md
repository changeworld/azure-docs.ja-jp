---
title: Data Factory を Azure Purview に接続する
description: Data Factory を Azure Purview に接続する方法について説明します
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 7dc05c88416bb2a23221029bc04c506271a86652
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108349"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Data Factory を Azure Purview に接続する (プレビュー)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、データ ファクトリを Azure Purview に接続する方法と、ADF アクティビティ (データのコピー、データ フロー、および SSIS パッケージの実行) のデータ系列を報告する方法について説明します。

## <a name="connect-data-factory-to-azure-purview"></a>データ ファクトリを Azure Purview に接続する
Azure Purview は、データ ユーザーがクラウド環境とオンプレミス環境にまたがるデータ資産全体のデータ ガバナンスを一元管理するために使用する新しいクラウド サービスです。 データ ファクトリを Azure Purview に接続できます。また、この接続により、Azure Purview を利用して、コピー、データ フロー、および SSIS パッケージの実行の系列データをキャプチャできます。 Azure Purview にデータ ファクトリを登録する方法については、「[Azure Data Factory と Azure Purview を接続する方法](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory)」を参照してください。 

## <a name="report-lineage-data-to-azure-purview"></a>系列データを Azure Purview に報告する
お客様が Azure Data Factory でコピー、データ フロー、または SSIS パッケージの実行アクティビティを実行すると、お客様は依存関係を取得し、データ ソースと宛先の間のワークフロー プロセス全体の概要を把握できます。
Azure Data Factory からデータ系列を収集する方法については、[Data Factory のデータ系列](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory#supported-azure-data-factory-activities)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ
[カタログ系列ユーザー ガイド](https://docs.microsoft.com/azure/purview/catalog-lineage-user-guide)

[チュートリアル: Data Factory 系列データを Azure Purview にプッシュする](turorial-push-lineage-to-purview.md)
