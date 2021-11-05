---
title: パイプライン アクティビティでの Azure Key Vault シークレットの使用
description: Data Factory パイプラインの実行中に、Azure Key Vault から格納済みの資格情報をフェッチして使用する方法について説明します。
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.subservice: security
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: 09a2c057cbefefc02e40d326eea094e37b092cc5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851156"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>パイプライン アクティビティでの Azure Key Vault シークレットの使用

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

資格情報またはシークレット値を Azure Key Vault に格納し、パイプラインの実行時にそれらを使用してアクティビティに渡すことができます。

## <a name="prerequisites"></a>前提条件

この機能は、データ ファクトリのマネージド ID に依存しています。  [データ ファクトリのマネージド ID](./data-factory-service-identity.md) からのその動作方法について理解し、使用中のデータ ファクトリに ID が関連付けられていることを確認します。

## <a name="steps"></a>手順

1. データ ファクトリのプロパティを開き、マネージド ID アプリケーション ID の値をコピーします。

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png" alt-text="マネージド ID の値":::

2. キー コンテナー アクセス ポリシーを開き、シークレットの取得および一覧表示のためのマネージド ID アクセス許可を追加します。

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png" alt-text="&quot;[アクセス ポリシーの追加]&quot; 操作が強調して示されている &quot;[アクセス ポリシー]&quot; ページを示すスクリーンショット。":::

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png" alt-text="キー コンテナー アクセス ポリシー":::

    **[追加]** をクリックし、 **[保存]** をクリックします。

3. キー コンテナー シークレットに移動し、シークレット識別子をコピーします。

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png" alt-text="シークレット識別子":::

    データ ファクトリ パイプラインの実行中に取得するシークレットの URI をメモしておきます。

4. データ ファクトリ パイプラインで、新しい Web アクティビティを追加し、次のように構成します。  

    |プロパティ  |値  |
    |---------|---------|
    |セキュリティで保護された出力     |True         |
    |URL     |[自分のシークレットの URI 値]?api-version=7.0         |
    |方法     |GET         |
    |認証     |MSI         |
    |リソース        |https://vault.azure.net       |

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png" alt-text="Web アクティビティ":::

    > [!IMPORTANT]
    > シークレットの URI の末尾に **? api-version = 7.0** を追加する必要があります。  

    > [!CAUTION]
    > シークレット値がプレーンテキストでログに記録されないようにするには、[セキュリティで保護された出力] オプションを true に設定します。  この値を使用するその他のアクティビティでは、[セキュリティで保護された入力] オプションが true に設定されている必要があります。

5. この値を別のアクティビティで使用するには、コード式 **@activity('Web1').output.value** を使用します。

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png" alt-text="コード式":::

## <a name="next-steps"></a>次のステップ

Azure Key Vault を使用して、データ ストアおよび計算のための資格情報を格納する方法については、「[Azure Key Vault への資格情報の格納](./store-credentials-in-key-vault.md)」を参照してください