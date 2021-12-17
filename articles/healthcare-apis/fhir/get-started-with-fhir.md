---
title: FHIR サービスの概要-Azure の医療 Api
description: このドキュメントでは、Azure の医療 Api で FHIR サービスを開始する方法について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 11/24/2021
ms.author: zxue
ms.openlocfilehash: f679f82531c84b5c05de4bddc2551c9c51c6d09b
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2021
ms.locfileid: "133273228"
---
# <a name="get-started-with-the-fhir-service"></a>FHIR サービスを使ってみる

この記事では、 [Azure の医療 api](../healthcare-apis-overview.md)で FHIR サービスを開始するための基本的な手順について説明します。

前提条件として、Azure サブスクリプションが必要であり、Azure リソースグループを作成して Azure リソースをデプロイするための適切なアクセス許可が付与されている必要があります。 すべての手順を実行するか、既存の環境がある場合はスキップすることができます。 また、すべての手順を組み合わせて、PowerShell、Azure CLI、REST API スクリプトで完了することもできます。

[![FHIR サービスフローダイアグラムの使用を開始します。](media/get-started-with-fhir.png)](media/get-started-with-fhir.png#lightbox)

## <a name="create-a-workspace-in-your-azure-subscription"></a>Azure サブスクリプションでワークスペースを作成する

ワークスペースは、 [Azure portal](../healthcare-apis-quickstart.md)から作成することも、PowerShell、Azure CLI、および REST API を使用して作成することもできます。 スクリプトは、 [医療 api サンプル](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)から入手できます。

> [!NOTE]
> ワークスペースの数と、各 Azure サブスクリプションで作成できる FHIR サービスインスタンスの数には制限があります。

## <a name="create-a-fhir-service-in-the-workspace"></a>ワークスペースでの FHIR サービスの作成

FHIR サービスインスタンスは、 [Azure portal](../fhir/fhir-portal-quickstart.md)から作成することも、PowerShell、Azure CLI、および REST API を使用して作成することもできます。 スクリプトは、 [医療 api サンプル](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)から入手できます。

必要に応じて、ワークスペースに、 [DICOM サービス](../dicom/deploy-dicom-services-in-azure.md) と [IoT コネクタ](../iot/deploy-iot-connector-in-azure.md) を作成することもできます。

## <a name="access-the-fhir-service"></a>FHIR サービスへのアクセス

fhir サービスは Azure Active Directory (Azure AD) によって保護されており、無効にすることはできません。 サービス API にアクセスするには、Azure AD でサービスプリンシパルとも呼ばれるクライアントアプリケーションを作成し、適切なアクセス許可を付与する必要があります。

### <a name="register-a-client-application"></a>クライアントアプリケーションを登録する

クライアントアプリケーションを作成または登録するには、 [Azure portal](../register-application.md)、または PowerShell と Azure CLI スクリプトを使用します。 このクライアントアプリケーションは、1つまたは複数の FHIR サービスインスタンスに使用できます。 また、Azure の医療 Api のその他のサービスにも使用できます。

クライアントアプリケーションが証明書またはクライアントシークレットを使用して作成されている場合は、有効期限が切れる前に証明書またはクライアントシークレットを更新し、アプリケーションのクライアント資格情報を置き換えてください。

クライアントアプリケーションを削除できます。 クライアントアプリケーションを削除する前に、実稼働、開発、テスト、または品質保証 (QA) 環境で使用されていないことを確認してください。

### <a name="grant-access-permissions"></a>アクセス許可を付与する

アクセス許可を付与したり、 [Azure portal](../configure-azure-rbac.md)からロールを割り当てたり、PowerShell および Azure CLI スクリプトを使用したりすることができます。

### <a name="perform-create-read-update-and-delete-crud-transactions"></a>作成、読み取り、更新、および削除 (CRUD) トランザクションの実行

アプリケーションの FHIR サービスに対して、または Postman、REST クライアント、cURL などのツールを使用して、作成、読み取り (検索)、更新、および削除 (CRUD) トランザクションを実行できます。 FHIR サービスは既定でセキュリティ保護されているので、アクセストークンを取得してトランザクション要求に含める必要があります。

#### <a name="get-an-access-token"></a>アクセス トークンを取得する

PowerShell、Azure CLI、REST CCI、または .net SDK を使用して、Azure AD アクセストークンを取得できます。  詳細については、「 [アクセストークンを取得](../get-access-token.md)する」を参照してください。

#### <a name="access-using-existing-tools"></a>既存のツールを使用したアクセス

- [Postman](../use-postman.md)
- [Rest クライアント](../using-rest-client.md)
- [cURL](../using-curl.md)

#### <a name="load-data"></a>データの読み込み

FHIR サービスに対して POST または PUT メソッドを使用して、データを直接読み込むことができます。 データを一括読み込みするには、次に示すオープンソースツールのいずれかを使用できます。
 
- [Fhir ローダー](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/FHIRDL) これは .NET コンソールアプリケーションであり、Azure storage に格納されているデータを FHIR サービスに読み込みます。 シングルスレッドアプリですが、複数のコピーをローカルまたは Docker コンテナーで実行できます。 
- [Fhir 一括ローダー](https://github.com/microsoft/fhir-loader) このツールは、Azure function app (マイクロサービス) であり、並列スレッドで実行されます。
- [一括インポート](https://github.com/microsoft/fhir-server/blob/main/docs/BulkImport.md) このツールは、オープンソースの FHIR サーバーでのみ動作します。 ただし、今後、Azure の医療 Api で使用できるようになります。

### <a name="cms-search-profile-validation-and-reindex"></a>CMS、検索、プロファイル検証、および再インデックス

詳細については、 [Fhir サービス](overview.md) のドキュメントの「相互運用性と患者のアクセス、検索、プロファイルの検証、および再インデックス作成」を参照してください。

### <a name="export-data"></a>データのエクスポート

必要に応じて、データを[Azure Storage](../data-transformation/export-data.md)にエクスポート ($export) し、分析または機械学習プロジェクトで使用することができます。 形式でデータを "その他" または " [id](../data-transformation/de-identified-export.md) " としてエクスポートでき `ndjson` ます。 

オープンソースプロジェクトを使用して、データを [Synapse](../data-transformation/move-to-synapse.md) にエクスポートすることもできます。 今後、この機能はマネージドサービスに統合されます。

### <a name="converting-data"></a>データの変換

必要に応じて、 [HL7 v2](../data-transformation/convert-data.md) とその他の形式のデータを FHIR に変換できます。

### <a name="using-fhir-data-in-power-bi-dashboard"></a>Power BI ダッシュボードでの fhir データの使用

必要に応じて、fhir データを使用して Power BI ダッシュボードレポートを作成できます。

- [FHIR の Power Query コネクタ](https://docs.microsoft.com/power-query/connectors/fhir/fhir)
- [IoT コネクタと Microsoft Power BI](../iot/iot-connector-power-bi.md)

## <a name="next-steps"></a>次のステップ

この記事では、FHIR サービスの使用を開始するための基本的な手順について説明しました。 ワークスペースへの FHIR サービスのデプロイの詳細については、「」を参照してください。

>[!div class="nextstepaction"]
>[Azure の医療 Api 内での FHIR サービスのデプロイ](fhir-portal-quickstart.md)
