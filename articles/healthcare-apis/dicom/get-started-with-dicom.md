---
title: 概要 DICOM サービスの使用 - Azure Healthcare API
description: このドキュメントでは、Azure Healthcare API で DICOM サービスの使用を開始する方法について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 11/24/2021
ms.author: zxue
ms.openlocfilehash: bc3c691d7a990e47b6afe624854d9085169fe0b2
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2021
ms.locfileid: "133273207"
---
# <a name="get-started-with-the-dicom-service"></a>概要 DICOM サービスを使用する

この記事では、Azure Healthcare API で DICOM サービスの使用を開始するための基本的な [手順について説明します](../healthcare-apis-overview.md)。 

前提条件として、Azure サブスクリプションが必要であり、Azure リソース グループを作成して Azure リソースをデプロイするための適切なアクセス許可が付与されています。 すべての手順に従って実行するか、既存の環境がある場合はスキップできます。 また、すべての手順を組み合わせて、PowerShell、Azure CLI、REST APIできます。

[![はじめに DICOM を使用する](media/get-started-with-dicom.png)](media/get-started-with-dicom.png#lightbox)

## <a name="create-a-workspace-in-your-azure-subscription"></a>Azure サブスクリプションでワークスペースを作成する

ワークスペースは、Azure portal から作成[](../healthcare-apis-quickstart.md)するか、PowerShell、Azure CLI、REST API。 スクリプトは、Healthcare [API のサンプル から確認できます](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)。

> [!NOTE]
> ワークスペースの数と、各 Azure サブスクリプションで作成できる DICOM サービス インスタンスの数には制限があります。

## <a name="create-a-dicom-service-in-the-workspace"></a>ワークスペースに DICOM サービスを作成する

DICOM サービス インスタンスは、Azure portalから[](deploy-dicom-services-in-azure.md)作成するか、PowerShell、Azure CLI、REST API。 スクリプトは、Healthcare [API のサンプル から確認できます](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)。

必要に応じて、ワークスペースで [FHIR サービス](../fhir/fhir-portal-quickstart.md) と [IoT コネクタ](../iot/deploy-iot-connector-in-azure.md) を作成できます。

## <a name="access-the-dicom-service"></a>DICOM サービスにアクセスする

DICOM サービスは、無効にできないAzure Active Directory (Azure AD) によって保護されます。 サービス API にアクセスするには、Azure AD でサービス プリンシパルとも呼ばれるクライアント アプリケーションを作成し、適切なアクセス許可を付与する必要があります。

### <a name="register-a-client-application"></a>クライアント アプリケーションを登録する

クライアント アプリケーションは、 から作成または登録するか [、powerShell](../register-application.md)Azure portalスクリプトを使用してAzure CLIできます。 このクライアント アプリケーションは、1 つ以上の DICOM サービス インスタンスに使用できます。 また、Azure Healthcare API の他のサービスにも使用できます。

クライアント アプリケーションが証明書またはクライアント シークレットで作成されている場合は、有効期限が切る前に証明書またはクライアント シークレットを更新し、アプリケーションのクライアント資格情報を置き換える必要があります。

クライアント アプリケーションを削除できます。 これを行う前に、それが実稼働環境、開発環境、テスト環境、または品質保証 (QA) 環境で使用されていないことを確認してください。

### <a name="grant-access-permissions"></a>アクセス許可を付与する

アクセス許可を付与したり、 からロールを割[](../configure-azure-rbac.md)り当Azure portal、PowerShell とスクリプトを使用Azure CLIできます。

### <a name="perform-create-read-update-and-delete-crud-transactions"></a>作成、読み取り、更新、削除 (CRUD) トランザクションを実行する

アプリケーションの DICOM サービスに対して、または Postman、REST Client、cURL、Python などのツールを使用して、作成、読み取り (検索)、更新、削除 (CRUD) トランザクションを実行できます。 DICOM サービスは既定でセキュリティで保護されているので、アクセス トークンを取得し、トランザクション要求に含める必要があります。

#### <a name="get-an-access-token"></a>アクセス トークンを取得する

PowerShell、Azure AD、REST CLI、または .NET SDK をAzure CLIアクセス トークンを取得できます。  詳細については、「アクセス トークンの取得 [」を参照してください](../get-access-token.md)。

#### <a name="access-using-existing-tools"></a>既存のツールを使用したアクセス

- [Postman](../use-postman.md)
- [REST クライアント](../using-rest-client.md)
- [.NET C#](dicomweb-standard-apis-c-sharp.md)
- [cURL](dicomweb-standard-apis-curl.md)
- [Python](dicomweb-standard-apis-python.md)

### <a name="dicomweb-standard-apis-and-change-feed"></a>DICOMweb 標準 API と変更フィード

DICOMweb 標準 API と変更フィードの詳細については [、DICOM サービスのドキュメントを参照](dicom-services-overview.md) してください。

#### <a name="dicomcast"></a>DICOMCast

オープン ソース [DICOMCast プロジェクトを使用して](https://github.com/microsoft/dicom-server/tree/main/converter/dicom-cast) 、FHIR データを処理できます。 今後、この機能はマネージド サービスで使用できます。

## <a name="next-steps"></a>次のステップ

この記事では、DICOM サービスの使用を開始するための基本的な手順について説明しました。 ワークスペースに DICOM サービスをデプロイする方法については、「」を参照してください。

>[!div class="nextstepaction"]
>[次のコマンドを使用して DICOM サービスをデプロイAzure portal](deploy-dicom-services-in-azure.md)