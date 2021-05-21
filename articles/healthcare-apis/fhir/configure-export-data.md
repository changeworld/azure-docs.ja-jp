---
title: Azure API for FHIR でエクスポート設定を構成する
description: この記事では、Azure API for FHIR でエクスポート設定を構成する方法について説明します
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/11/2021
ms.author: matjazl
ms.openlocfilehash: 20c08851d082702342b43bad12409cc71a0cae46
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110062022"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>エクスポート設定を構成してストレージ アカウントを設定する

Azure API for FHIR では、Azure API for FHIR アカウントからストレージ アカウントにデータをエクスポートできるようにする $export コマンドがサポートされています。

Azure API for FHIR でエクスポートを構成するには、次の 3 つの手順があります。

1. Azure API for FHIR サービスでマネージド ID を有効にする。
2. Azure ストレージ アカウントを作成し (まだ実行していない場合)、ストレージ アカウントにアクセス許可Azure API for FHIR割り当てる。
3. [ストレージ アカウント] でストレージ アカウントをAzure API for FHIRストレージ アカウントとして選択します。

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Azure API for FHIR のマネージド ID の有効化

Azure API for FHIR のエクスポートを構成する最初の手順は、サービスに対するシステム全体のマネージド ID を有効にすることです。 Azure のマネージド ID の詳細については、「Azure リソースのマネージド [ID について」を参照してください](../../active-directory/managed-identities-azure-resources/overview.md)。

これを行うには、サービスの [ID] Azure API for FHIR選択 **します**。 状態を [オン] **に変更** すると、Azure API for FHIR サービスでマネージド ID が有効になります。

![マネージド ID の有効化](media/export-data/fhir-mi-enabled.png)

これで、ストレージ アカウントを作成し、サービスにアクセス許可を割り当て、次の手順に進みます。

## <a name="adding-permission-to-storage-account"></a>ストレージ アカウントへのアクセス許可の追加

データのエクスポートの次の手順は、ストレージ アカウントに書き込Azure API for FHIRサービスにアクセス許可を割り当てる方法です。

ストレージ アカウントを作成したら、ストレージ アカウントの Access Control **(IAM)** に移動し、[ロールの割り当ての追加 **] を選択します**。 

アプリケーションでロールを割り当てる方法の詳細についてはAzure portal Azure 組み込みロールに関 [するページを参照してください](../../role-based-access-control/role-assignments-portal.md)。

ここでは、ストレージ BLOB データ共同作成者ロール [](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)をサービス名に追加し、[保存] を選択 **します**。

![[ロールの割り当ての追加] ページ](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

これで、 のストレージ アカウントを選択し、Azure API for FHIR既定のストレージ アカウントとして選択$export。

## <a name="selecting-the-storage-account-for-export"></a>$export 用のストレージ アカウントの選択

最後の手順では、データのエクスポートに使用Azure API for FHIR Azure ストレージ アカウントを割り当てる必要があります。 これを行うには、サービスの **[統合** ] にAzure API for FHIRストレージ アカウントを選択します。

![FHIR エクスポート ストレージ](media/export-data/fhir-export-storage.png)

この最後の手順を完了すると、 コマンドを使用してデータをエクスポート$exportできます。

> [!Note]
> 同じサブスクリプション内のストレージ アカウントのみを、Azure API for FHIR操作の宛先として登録$exportできます。

データベース設定の構成、アクセス制御、診断ログの有効化、およびカスタム ヘッダーを使用した監査ログへのデータの追加の詳細については、次を参照してください。

>[!div class="nextstepaction"]
>[[追加設定]](azure-api-for-fhir-additional-settings.md)
