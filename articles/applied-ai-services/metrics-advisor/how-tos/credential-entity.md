---
title: 資格情報エンティティを作成する
titleSuffix: Azure Cognitive Services
description: 資格情報エンティティを作成して資格情報を安全に管理する方法。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 06/22/2021
ms.author: mbullwin
ms.openlocfilehash: 0e16b92ff4a2316042e2177f9525734ba1949db1
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341545"
---
# <a name="how-to-create-a-credential-entity"></a>方法: 資格情報エンティティを作成する

データ フィードをオンボードする際に、認証の種類を選択する必要があります。*Azure SQL 接続文字列* や *サービス プリンシパル* などの一部の認証の種類では、資格情報を安全に管理するために、資格情報に関連する情報を格納する資格情報エンティティが必要です。 この記事では、Metrics Advisor でさまざまな資格情報の種類の資格情報エンティティを作成する方法について説明します。
    

## <a name="basic-procedure-create-a-credential-entity"></a>基本的な手順: 資格情報エンティティを作成する

**資格情報エンティティ** を作成して資格情報に関連する情報を格納し、それを使用してデータ ソースに対する認証を行うことができます。 資格情報エンティティを他のユーザーと共有すると、実際の資格情報を共有せずに、それらのユーザーがデータ ソースに接続できるようになります。 これは、[データ フィードの追加] タブまたは [資格情報エンティティ] タブで作成できます。特定の認証の種類の資格情報エンティティを作成した後は、新しいデータ フィードを追加するときに、作成した 1 つの資格情報エンティティを選択するだけで済みます。これは、複数のデータ フィードを作成するときに役立ちます。 次に、資格情報エンティティを作成して使用する一般的な手順を示します。

1. [データ フィードの追加] タブで新しい資格情報エンティティを作成するには、[+] を選択します ([資格情報エンティティ フィード] タブで作成することもできます)。

   ![資格情報エンティティを作成する](../media/create-credential-entity.png)
 
2. 資格情報エンティティの名前、説明 (必要な場合)、資格情報の種類 ("*認証の種類*" と同じ)、その他の項目を設定します。

   ![資格情報エンティティを設定する](../media/set-credential-entity.png)
 
3. 資格情報エンティティを作成した後は、認証の種類を指定するときにそれを選択できます。

   ![資格情報エンティティを選択する](../media/choose-credential-entity.png)
 
Metrics Advisor には、**4 つの資格情報の種類** (Azure SQL 接続文字列、Azure Data Lake Storage Gen2 共有キー エンティティ、サービス プリンシパル、キー コンテナーからのサービス プリンシパル) があります。 異なる資格情報の種類の設定については、次の手順を参照してください。

## <a name="azure-sql-connection-string"></a>Azure SQL 接続文字列

**[名前]** と **[接続文字列]** を設定してから、[作成] を選択する必要があります。

![SQL 接続文字列の資格情報エンティティの設定](../media/credential-entity/credential-entity-sql-connection-string.png)

## <a name="azure-data-lake-storage-gen2-shared-key-entity"></a>Azure Data Lake Storage Gen2 共有キー エンティティ

**[名前]** と **[アカウント キー]** を設定してから、[作成] を選択する必要があります。 アカウント キーは、Azure ストレージ アカウント (Azure Data Lake Storage Gen2) リソースの **[アクセス キー]** 設定で確認できます。

<!-- 增加basic说明，tips是错的；增加一下怎么管理；加一个step1的link
-->
![Data Lake の資格情報エンティティの設定](../media/credential-entity/credential-entity-data-lake.png)

## <a name="service-principal"></a>サービス プリンシパル

データ ソースのサービス プリンシパルを作成する手順の詳細については、「[さまざまなデータ ソースを接続する](../data-feeds-from-different-sources.md)」を参照してください。 サービス プリンシパルを作成したら、資格情報エンティティの次の構成を入力する必要があります。

![サービス プリンシパルの資格情報エンティティ](../media/credential-entity/credential-entity-service-principal.png)

* **名前**: サービス プリンシパルの資格情報エンティティの名前を設定します。
* **テナント ID とクライアント ID**: Azure portal でサービス プリンシパルを作成した後、 **[概要]** で `Tenant ID` と `Client ID` を確認できます。

    ![サービス プリンシパルのクライアント ID とテナント ID](../media/credential-entity/sp-client-tenant-id.png)

* **クライアント シークレット**: Azure portal でサービス プリンシパルを作成した後、 **[証明書とシークレット]** に移動して新しいクライアント シークレットを作成し、その **値** を資格情報エンティティの `Client Secret` として使用する必要があります。 (注: この値は 1 回しか表示されないため、どこかに保管することをお勧めします。)


    ![サービス プリンシパルのクライアント シークレットの値](../media/credential-entity/sp-secret-value.png)

## <a name="span-idsp-from-kvservice-principal-from-key-vaultspan"></a><span id="sp-from-kv">キー コンテナーからのサービス プリンシパル</span>

キー コンテナーからサービス プリンシパルを作成するには、いくつかの手順を実行します。

**手順 1. サービス プリンシパルを作成し、データベースへのアクセスを許可します。** 手順の詳細については、「[さまざまなデータ ソースを接続する](../data-feeds-from-different-sources.md)」の各データ ソースのサービス プリンシパルの作成に関するセクションをご覧ください。 

Azure portal でサービス プリンシパルを作成した後、 **[概要]** で `Tenant ID` と `Client ID` を確認できます。 **[ディレクトリ (テナント) ID]** が資格情報エンティティ構成の `Tenant ID` になります。

![サービス プリンシパルのクライアント ID とテナント ID](../media/credential-entity/sp-client-tenant-id.png)

**手順 2. 新しいクライアント シークレットを作成します。** **[証明書とシークレット]** に移動して、新しいクライアント シークレットを作成する必要があります。この **値** を次の手順で使用します。 (注: この値は 1 回しか表示されないため、どこかに保管することをお勧めします。)

![サービス プリンシパルのクライアント シークレットの値](../media/credential-entity/sp-secret-value.png)

**手順 3. キー コンテナーを作成します。** [Azure portal](https://ms.portal.azure.com/#home) で、 **[キー コンテナー]** を選択して作成します。

![Azure portal でキー コンテナーを作成する](../media/credential-entity/create-key-vault.png)

キー コンテナーを作成した後、 **[コンテナー URI]** が MA (Metrics Advisor) の資格情報エンティティの `Key Vault Endpoint` になります。

![キー コンテナー エンドポイント](../media/credential-entity/key-vault-endpoint.png)

**手順 4. キー コンテナー用のシークレットを作成します。** キー コンテナーの Azure portal の **[設定] -> [シークレット]** で、2 つのシークレットを生成します。
1 つは `Service Principal Client Id` 用、もう 1 つは `Service Principal Client Secret` 用です。両方の名前を資格情報エンティティ構成で使用します。

![シークレットを生成する](../media/credential-entity/generate-secrets.png)

* **サービス プリンシパルのクライアント ID**: このシークレットの `Name` を設定します。この名前を資格情報エンティティ構成で使用します。この値は、**手順 1** のサービス プリンシパルの `Client ID` である必要があります。

    ![シークレット 1: サービス プリンシパルのクライアント ID](../media/credential-entity/secret-1-sp-client-id.png)

* **サービス プリンシパルのクライアント シークレット**: このシークレットの `Name` を設定します。この名前を資格情報エンティティ構成で使用します。この値は、**手順 2** のサービス プリンシパルの `Client Secret Value` である必要があります。

    ![シークレット 2: サービス プリンシパルのクライアント シークレット](../media/credential-entity/secret-2-sp-secret-value.png)

これで、ようやくサービス プリンシパルの "*クライアント ID*" と "*クライアント シークレット*" がキー コンテナーに格納されます。 次に、キー コンテナーを格納する別のサービス プリンシパルを作成する必要があります。 したがって、**サービス プリンシパルを 2 つ作成する** 必要があります。1 つはキー コンテナーに格納されるクライアント ID とクライアント シークレットを保存するためのもので、もう 1 つはキー コンテナーを格納するためのものです。

**手順 5. キー コンテナーを格納するためのサービス プリンシパルを作成します。** 

1. [Azure portal の AAD (Azure Active Directory)](https://portal.azure.com/?trace=diagnostics&feature.customportal=false#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) にアクセスして、新しい登録を作成します。

    ![新しい登録を作成する](../media/credential-entity/create-registration.png)

    サービス プリンシパルを作成した後、[概要] の **[アプリケーション (クライアント) ID]** が資格情報エンティティ構成の `Key Vault Client ID` になります。

2. **[管理] -> [証明書とシークレット]** で、[新しいクライアント シークレット] を選択してクライアント シークレットを作成します。 その後、値は 1 回しか表示されないため、**値を書き留めておく** 必要があります。 この値が資格情報エンティティ構成の `Key Vault Client Secret` になります。

    ![クライアント シークレットを追加する](../media/credential-entity/add-client-secret.png)

**手順 6. サービス プリンシパルにキー コンテナーへのアクセス権を付与します。**  作成したキー コンテナー リソースにアクセスし、 **[設定] -> [アクセス ポリシー]** で [アクセス ポリシーの追加] を選択して、キー コンテナーと **手順 5** の 2 つ目のサービス プリンシパルとの接続を確立し、[保存] をクリックします。

![サービス プリンシパルにキー コンテナーへのアクセス権を付与する](../media/credential-entity/grant-sp-to-kv.png)


## <a name="configurations-conclusion"></a>構成のまとめ
最後に、"*キー コンテナーから取得するサービス プリンシパル*" に関する Metrics Advisor の資格情報エンティティ構成とその取得方法を、次の表に示します。

| 構成 | 取得方法 |
|-------------| ---------------------|
| キー コンテナー エンドポイント | **手順 3**: キー コンテナーのコンテナー URI。 |
| テナント ID | **手順 1**: 1 つ目のサービス プリンシパルのディレクトリ (テナント) ID。 |
| キー コンテナーのクライアント ID | **手順 5**: 2 つ目のサービス プリンシパルのアプリケーション (クライアント) ID。 |
| キー コンテナーのクライアント シークレット | **手順 5**: 2 つ目のサービス プリンシパルのクライアント シークレット値。 |
| サービス プリンシパルのクライアント ID 名 | **手順 4**: クライアント ID として設定したシークレット名。 |
| サービス プリンシパルのクライアント シークレット名 | **手順 4**: クライアント シークレット値として設定したシークレット名。 |


## <a name="next-steps"></a>次のステップ

- [データをオンボードする](onboard-your-data.md)
- [さまざまなデータ ソースを接続する](../data-feeds-from-different-sources.md)
