---
title: Azure Stack Validation as a Service の一般的なワークフロー パラメーター | Microsoft Docs
description: Azure Stack Validation as a Service に使用される一般的なワークフロー パラメーター
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 81a7be973739cfd6eb3f8fb8dc7a0723623c2b8e
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234541"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>Azure Stack Validation as a Service に使用される一般的なワークフロー パラメーター

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

一般的なパラメーターとしては、サービスとしての検証 (VaaS) のすべてのテストに必要な環境変数やユーザー資格情報などの値が含まれます。 これらの値は、ワークフロー レベルで定義します。 ワークフローを作成または変更するときにこれらの値を保存します。 スケジュールするときに、ワークフローによって、テスト用の値が読み込まれます。 

## <a name="environment-parameters"></a>環境パラメーター

環境パラメーターは、テスト対象の Azure Stack 環境を表します。 これらの値を指定するには、お使いのスタンプ構成ファイルを生成してアップロードする必要があります `&lt;link&gt;. [How to get the stamp info link].`

| パラメーター名 | 必須 | type | 説明 |
|----------------------------------|----------|------|---------------------------------------------------------------------------------------------------------------------------------|
| Azure Stack のビルド | 必須 |  | Azure Stack のデプロイのビルド番号 (例: 1.0.170330.9) |
| OEM バージョン | [はい] |  | Azure Stack のデプロイ中に使用される OEM パッケージのバージョン番号。 |
| OEM 署名 | [はい] |  | Azure Stack のデプロイ中に使用される OEM パッケージの署名。 |
| AAD テナント ID | 必須 |  | Azure Stack のデプロイ中に指定される Azure Active Directory テナント GUID。|
| リージョン | 必須 |  | Azure Stack のデプロイ リージョン。 |
| テナントの Resource Manager エンドポイント | 必須 |  | テナントの Azure Resource Manager 操作のエンドポイント (例: https://management.<ExternalFqdn>) |
| 管理者の Resource Manager エンドポイント | [はい] |  | テナントの Azure Resource Manager 操作のエンドポイント (例: https://adminmanagement.<ExternalFqdn>) |
| 外部 FQDN | [はい] |  | エンドポイントのサフィックスとして使用される外部の完全修飾ドメイン名  (例: local.azurestack.external、redmond.contoso.com)。 |
| ノードの数 | [はい] |  | デプロイのノードの数。 |

## <a name="test-parameters"></a>テスト パラメーター

一般的なテスト パラメーターには、構成ファイルに格納できない機密情報が含まれています。このパラメーターは手動で指定する必要があります。

| パラメーター名 | 必須 | type | 説明 |
|--------------------------------|------------------------------------------------------------------------------|------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| テナント ユーザー名 | 必須 |  | AAD ディレクトリのサービス管理者によって既にプロビジョニングされている、またはプロビジョニングする必要がある Azure Active Directory テナント管理者。 テナント アカウントのプロビジョニングの詳細については、「[Get started with Azure AD (Azure AD の概要)](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad)」を参照してください。 この値は、リソース (VM、ストレージ アカウントなど) のプロビジョニングやワークロードを実行する目的で、テンプレートのデプロイなどのテナント レベルの操作を行うためにテストによって使用されます。 この値は、リソース (VM、ストレージ アカウントなど) のプロビジョニングやワークロードを実行する目的で、テンプレートのデプロイなどのテナント レベルの操作を行うためにテストによって使用されます。 |
| テナント パスワード | 必須 |  | テナント ユーザーのパスワード。 |
| サービス管理者のユーザー名 | 必須: ソリューションの検証、パッケージの検証<br>不要: テスト成功 |  | Azure Stack のデプロイ中に指定される AAD ディレクトリ テナントの Azure Active Directory の管理者。 |
| サービス管理者のパスワード | 必須: ソリューションの検証、パッケージの検証<br>不要: テスト成功 |  | サービス管理者ユーザーのパスワード。 |
| クラウド管理者のユーザー名 | 必須 |  | Azure Stack ドメイン管理者アカウント (例: contoso\cloudadmin)。 構成ファイルで User Role="CloudAdmin" を検索します。また、構成ファイルで UserName タグの値を選択します。 |
| クラウド管理者のパスワード | 必須 |  | クラウド管理者ユーザーのパスワード。 |
| 診断接続文字列 | 必須 |  | テスト実行中に診断ログがコピーされる先の Azure Storage アカウントの SAS URI。 SAS URI を生成する手順については、[Blob Storage アカウントの設定](azure-stack-vaas-set-up-account.md)に関するページをご覧ください。 |


## <a name="next-steps"></a>次の手順

- [Azure Stack Validation as a Service](https://docs.microsoft.com/azure/azure-stack/partner) について、さらに詳しい情報を確認する。
