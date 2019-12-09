---
title: Azure Security Center でのデータ サービスの脅威検出 | Microsoft Docs
description: この記事では、Azure Security Center で使用可能なデータ サービスのアラートについて説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: memildin
ms.openlocfilehash: 8b99d89e8895cd1c8d8e9fe3961f0db9855fb7ee
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196121"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Azure Security Center でのデータ サービスの脅威検出

 Azure Security Center では、データ ストレージ サービスのログが分析され、データ リソースへの脅威が検出されると、アラートがトリガーされます。 この記事では、Security Center が次のサービスに対して生成するアラートの一覧を示します。

* [Azure SQL Database と Azure SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## SQL Database と SQL Data Warehouse <a name="data-sql"></a>

SQL の脅威の検出により、データベースへのアクセスやデータベースの悪用を試みる、異常で有害と考えられる不自然な動作が識別されます。 

|アラート:|説明|
|---|---|
|**A possible vulnerability to SQL Injection (SQL インジェクションにつながる可能性のある脆弱性)**|アプリケーションにより、データベースでエラーのある SQL ステートメントが生成されました。 これは、SQL インジェクション攻撃に対する脆弱性があることを示している可能性があります。 エラーのあるステートメントの理由としては、次の 2 つが考えられます。 アプリケーション コードの欠陥により、エラーのある SQL ステートメントが作成された可能性がある。 または、アプリケーション コードまたはストアド プロシージャでユーザー入力がサニタイズされず、エラーのある SQL ステートメントが作成され、SQL インジェクションに悪用される可能性がある。|
|**SQL インジェクションの可能性**|SQL インジェクションに脆弱な特定されたアプリケーションに対してアクティブな悪用が発生しました。 これは、攻撃者が脆弱なアプリケーション コードまたはストアド プロシージャを使用して、悪意のある SQL ステートメントを挿入しようとしていることを意味します。|
|**Logon from an unusual location (通常とは異なる場所からのログオン)**|SQL Server へのアクセス パターンに変化があり、何者かが通常とは異なる地理的な場所からサーバーにサインインしました。 このアラートで正当なアクション (新しいアプリケーションや開発者メンテナンス) が検出されることがあります。 別のケースでは、このアラートによって悪意のあるアクション (元従業員や外部の攻撃者など) が検出されます。|
|**Logon by an unfamiliar principal (通常とは異なるプリンシパルによるログオン)**|SQL Server へのアクセス パターンに変化がありました。 何者かが、通常とは異なるプリンシパル (ユーザー) を使用してサーバーにサインインしました。 このアラートで正当なアクション (新しいアプリケーションや開発者メンテナンス) が検出されることがあります。 別のケースでは、このアラートによって悪意のあるアクション (元従業員や外部の攻撃者など) が検出されます。|
|**Attempted logon by a potentially harmful application (潜在的に有害なアプリケーションによりログオンが試行されました)**|データベースにアクセスするために、有害な可能性があるアプリケーションが使用されました。 このアラートで実行中の侵入テストが検出されることがあります。 別のケースでは、このアラートで一般的なツールを使用した攻撃が検出されます。|
|**Potential SQL Brute Force attempt (SQL ブルートフォース攻撃試行の可能性)**|異なる資格情報を使用したサインインの失敗が異常に多く発生しました。 このアラートで実行中の侵入テストが検出されることがあります。 このアラートでブルート フォース攻撃が検出されることもあります。|

SQL 脅威検出アラートの詳細については、[Azure SQL Database の脅威の検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)に関する記事を参照してください。 特に、脅威検出アラートに関するセクションをご覧ください。 また、[Azure Security Center の支援でサイバー攻撃がどのように明らかにされるか](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/)について参照し、Security Center で悪意のある SQL アクティビティ検出を使用して攻撃を検出した例を確認してください。

## Azure Storage <a name="azure-storage"></a>

>[!NOTE]
> Advanced Threat Protection for Storage は、現時点では Blob Storage でのみ使用できます。

Advanced Threat Protection for Storage にセキュリティ インテリジェンスの層が追加され、ストレージ アカウントにアクセスまたは悪用する、通常とは異なる潜在的に有害な試行が検出されます。 この保護層により、セキュリティの専門家でなくても脅威に対処し、セキュリティ監視システムを管理できます。

Security Center では、脅威を検出するために、BLOB ストレージに対する読み取り、書き込み、および削除要求の診断ログが分析され、アクティビティに異常がある場合にはアラートがトリガーされます。 詳細については、[Storage Analytics ログの構成](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)に関するページを参照してください。

> [!div class="mx-tableFixed"]

|アラート:|説明|
|---|---|
|**Access from an unusual location to a storage account (ストレージ アカウントへの通常とは異なる場所からのアクセス)**|Azure Storage アカウントへのアクセス パターンに変化があったことを示しています。 最近のアクティビティと比較して見慣れない IP アドレスから誰かがこのアカウントにアクセスしました。 攻撃者がアカウントへのアクセス権を取得したか、あるいは正当なユーザーが新しい、または通常とは異なる地理的場所から接続しました。 後者の例には、新しいアプリケーションまたは開発者からのリモート メンテナンスがあります。|
|**Unusual application accessed a storage account (通常とは異なるアプリケーションがストレージ アカウントにアクセスしました)**|通常とは異なるアプリケーションがこのストレージ アカウントにアクセスしたことを示します。 原因として考えられるのは、攻撃者が新しいアプリケーションを使用して、ご自分のストレージ アカウントにアクセスしたことです。|
|**Anonymous access to a storage account (ストレージ アカウントに対する匿名アクセス)**|ストレージ アカウントへのアクセス パターンに変化が生じたことを示します。 たとえば、アカウントへの匿名 (認証なし) アクセスがあり、それがこのアカウントでの最近のアクセス パターンと比較して予測外である場合などです。 原因として考えられるのは、攻撃者が Blob Storage を保持するコンテナーへのパブリック読み取りアクセスを悪用したことです。|
|**Access from a Tor exit node to a storage account (Tor 出口ノードからストレージ アカウントへのアクセス)**|このアカウントが Tor (匿名化プロキシ) のアクティブな出口ノードとして知られている IP アドレスから正常にアクセスされていることを示します。 このアラートの重大度では、使用された認証の種類 (ある場合) と、これがそのようなアクセスの最初のケースであるかどうかが考慮されます。 原因として考えられるのは、攻撃者が Tor を使用して、ご自分のストレージ アカウントにアクセスした、あるいは正当なユーザーが Tor を使用して、ご利用のストレージ アカウントにアクセスしたことです。|
|**Unusual amount of data extracted from a storage account (通常とは異なる量のデータがストレージ アカウントから抽出されました)**|このストレージ コンテナーの最近のアクティビティと比較して、異常に大量のデータが抽出されたことを示します。 原因として考えられるのは、攻撃者が Blob Storage を保持するコンテナーから大量のデータを抽出したことです。|
|**Unusual deletion in a storage account (ストレージ アカウントでの通常と異なる削除)**|このストレージ アカウントでの最近のアクティビティと比較して、アカウントで予期しない 1 つ以上の削除操作が発生したことを示します。 原因として考えられるのは、攻撃者がストレージ アカウントからデータを削除したことです。|
|**Unusual upload of .cspkg to a storage account (ストレージ アカウントへの .cspkg の通常と異なるアップロード)**|Azure Cloud Services パッケージ (.cspkg ファイル) が、このアカウントでの最近のアクティビティと比較して、通常と異なる方法でストレージ アカウントにアップロードされたことを示します。 原因として考えられるのは、攻撃者が、ストレージ アカウントから Azure クラウド サービスに悪意のあるコードをデプロイしようとしていたことです。|
|**Unusual change of access permissions in a storage account (ストレージ アカウントでのアクセス許可の通常と異なる変更)**|このストレージ コンテナーのアクセス許可が通常と異なる方法で変更されたことを示します。 原因として考えられるのは、攻撃者がコンテナーのアクセス許可を変更して、そのセキュリティ体制を弱めたり、持続性を獲得したことです。|
|**Unusual access inspection in a storage account (ストレージ アカウントでの通常と異なるアクセスの検査)**|ストレージ アカウントのアクセス許可が、このアカウントでの最近のアクティビティと比較して、通常とは異なる方法で検査されたことを示します。 原因として考えられるのは、攻撃者が将来の攻撃のための偵察を実行したことです。|
|**Unusual access inspection in a storage account (ストレージ アカウントでの通常と異なるデータの探索)**|ストレージ アカウント内の BLOB またはコンテナーが、このアカウントでの最近のアクティビティと比較して、通常と異なる方法で列挙されたことを示します。 原因として考えられるのは、攻撃者が将来の攻撃のための偵察を実行したことです。|
|**PREVIEW - Potential malware uploaded to a storage account (プレビュー - マルウェアがストレージ アカウントにアップロードされた可能性)**|マルウェアを含んだ BLOB がストレージ アカウントにアップロードされた可能性があることを示します。 原因としては、攻撃者が意図的にマルウェアをアップロードするケースと、正当なユーザーが意図せず悪意のある BLOB をアップロードしてしまうケースが考えられます。|

>[!NOTE]
>Advanced Threat Protection for Storage は、現在、Azure Government およびソブリン クラウドのリージョンでは使用できません。

ストレージのアラートの詳細については、[Advanced Threat Protection for Azure Storage](../storage/common/storage-advanced-threat-protection.md)に関する記事を参照してください。 特に、「保護アラート」セクションをご覧ください。

## Azure Cosmos DB<a name="cosmos-db"></a>

次のアラートは、Azure Cosmos DB アカウントに対して、通常と異なる潜在的に有害なアクセスやエクスプロイトが試行されると生成されます。

|アラート:|説明|
|---|---|
|**Access from an unusual location to a Cosmos DB account (Cosmos DB アカウントへの通常とは異なる場所からのアクセス)**|Azure Cosmos DB アカウントへのアクセス パターンに変化があったことを示します。 最近のアクティビティと比較して、誰かが未知の IP アドレスからこのアカウントにアクセスしました。 攻撃者がアカウントにアクセスしてたか、あるいは正当なユーザーが通常と異なる新しい地理的場所からアカウントにアクセスしました。 後者の例には、新しいアプリケーションまたは開発者からのリモート メンテナンスがあります。|
|**Unusual amount of data extracted from a Cosmos DB account (通常とは異なる量のデータが Cosmos DB アカウントから抽出されました)**|Azure Cosmos DB アカウントからのデータ抽出パターンに変化があったことを示します。 最近のアクティビティと比較して、誰かが通常と異なる量のデータを抽出しました。 攻撃者が、Azure Cosmos DB データベースから大量のデータを抽出した可能性があります (たとえば、データの窃盗や漏えい、データの不正な転送など)。 あるいは、正当なユーザーまたはアプリケーションによって、コンテナーから通常とは異なる量のデータが抽出された可能性もあります (メンテナンス バックアップ アクティビティの場合など)。|

詳細については、「[Azure Cosmos DB の Advanced Threat Protection](../cosmos-db/cosmos-db-advanced-threat-protection.md)」を参照してください。
