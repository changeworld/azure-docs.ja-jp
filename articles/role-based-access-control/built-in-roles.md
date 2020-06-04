---
title: Azure 組み込みロール - Azure RBAC
description: この記事では、Azure ロールベースのアクセス制御 (Azure RBAC) の Azure 組み込みロールについて説明します。 Actions、NotActions、DataActions、NotDataActions を一覧表示します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/04/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 0a574ba281a037a06ddda1981ae6fa35b905bca1
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683649"
---
# <a name="azure-built-in-roles"></a>Azure 組み込みロール

[Azure ロールベースのアクセス制御 (Azure RBAC)](overview.md) には、ユーザー、グループ、サービス プリンシパル、マネージド ID に割り当てることのできる Azure 組み込みロールがいくつかあります。 ロールの割り当ては、Azure リソースへのアクセスを制御する方法です。 組み込みロールが組織の特定のニーズを満たさない場合は、独自の [Azure カスタム ロール](custom-roles.md)を作成することができます。

この記事では、常に進化している Azure 組み込みロールが一覧表示されています。 最新のロールを取得するには、[Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) または [az role definition list](/cli/azure/role/definition#az-role-definition-list) を使用してください。 Azure Active Directory (Azure AD) の管理者ロールについては、「[Azure Active Directory での管理者ロールのアクセス許可](../active-directory/users-groups-roles/directory-assign-admin-roles.md)」を参照してください。

## <a name="all"></a>All

次の表に、各組み込みロールの簡単な説明と一意の ID を示します。 ロール名を選択すると、各ロールの `Actions`、`NotActions`、`DataActions`、`NotDataActions` の一覧が表示されます。 これらのアクションの意味と、管理とデータ プレーンへの適用方法については、「[Azure ロールの定義について](role-definitions.md)」を参照してください。


> [!div class="mx-tableFixed"]
> | 組み込みのロール | 説明 | id |
> | --- | --- | --- |
> | **全般** |  |  |
> | [Contributor](#contributor) | リソースへのアクセス権の付与以外のすべてを管理できます。 | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [所有者](#owner) | リソースへのアクセスを含め、すべてを管理できます。 | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Reader](#reader) | すべてを表示できますが、変更することはできません。 | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [User Access Administrator](#user-access-administrator) | Azure リソースに対するユーザー アクセスを管理します。 | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Compute** |  |  |
> | [Classic Virtual Machine Contributor](#classic-virtual-machine-contributor) | 従来の仮想マシンを管理できますが、アクセスすることはできません。また、接続先の仮想ネットワークやストレージ アカウントにもアクセスできません。 | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Virtual Machine Administrator Login](#virtual-machine-administrator-login) | ポータルで仮想マシンを表示し、管理者としてログインします | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Virtual Machine Contributor](#virtual-machine-contributor) | 仮想マシンを管理できますが、アクセスすることはできません。また、接続先の仮想ネットワークやストレージ アカウントにもアクセスできません。 | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Virtual Machine User Login](#virtual-machine-user-login) | ポータルで仮想マシンを表示し、通常のユーザーとしてログインします。 | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **ネットワーク** |  |  |
> | [CDN Endpoint Contributor](#cdn-endpoint-contributor) | CDN エンドポイントを管理できますが、アクセス権を他のユーザーに付与することはできません。 | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [CDN Endpoint Reader](#cdn-endpoint-reader) | CDN エンドポイントを表示できますが、変更はできません。 | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [CDN Profile Contributor](#cdn-profile-contributor) | CDN プロファイルとそのエンドポイントを管理できますが、アクセス権を他のユーザーに付与することはできません。 | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [CDN Profile Reader](#cdn-profile-reader) | CDN プロファイルとそのエンドポイントを表示できますが、変更はできません。 | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Classic Network Contributor](#classic-network-contributor) | 従来のネットワークを管理できます。ただし、それらへのアクセスは含まれません。 | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [DNS Zone Contributor](#dns-zone-contributor) | Azure DNS の DNS ゾーンとレコード セットを管理できますが、それにアクセスできるユーザーを制御することはできません。 | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Network Contributor](#network-contributor) | ネットワークを管理できます。ただし、それらへのアクセスは含まれません。 | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Traffic Manager Contributor](#traffic-manager-contributor) | Traffic Manager プロファイルを管理できますが、それにアクセスできるユーザーを制御することはできません。 | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Storage** |  |  |
> | [Avere 共同作成者](#avere-contributor) | Avere vFXT クラスターを作成および管理できます。 | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Avere オペレーター](#avere-operator) | クラスターを管理するために Avere vFXT クラスターによって使用されます | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Backup Contributor](#backup-contributor) | バックアップ サービスを管理できますが、資格情報コンテナーの作成や他のユーザーに対するアクセス権の付与を行うことはできません | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Backup Operator](#backup-operator) | バックアップ サービスを管理できます (バックアップの削除、資格情報コンテナーの作成、他のユーザーに対するアクセス権の付与を除く) | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Backup Reader](#backup-reader) | バックアップ サービスを表示できますが、変更を行うことはできません | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Classic Storage Account Contributor](#classic-storage-account-contributor) | 従来のストレージ アカウントを管理できますが、アクセスすることはできません。 | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [従来のストレージ アカウント キー オペレーターのサービス ロール](#classic-storage-account-key-operator-service-role) | 従来のストレージ アカウント キー オペレーターは、従来のストレージ アカウントでのキーの一覧表示と再生成を行うことができます | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Data Box Contributor](#data-box-contributor) | Data Box サービスですべてを管理できます (他のユーザーに対するアクセス権の付与を除く)。 | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Data Box 閲覧者](#data-box-reader) | Data Box サービスを管理できます (注文の作成または注文の詳細の編集、および他のユーザーに対するアクセス権の付与を除く)。 | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Data Lake Analytics Developer](#data-lake-analytics-developer) | 独自のジョブを送信、監視、管理できますが、Data Lake Analytics アカウントを作成または削除することはできません。 | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Reader and Data Access](#reader-and-data-access) | すべてを表示することができますが、ストレージ アカウントや含まれるリソースの削除や作成はできません。 ストレージ アカウント キーへのアクセスを使用して、ストレージ アカウントに含まれるすべてのデータへの読み取り/書き込みアクセスも許可されます。 | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Storage Account Contributor](#storage-account-contributor) | ストレージ アカウントの管理を許可します。 アカウント キーへのアクセスを提供します。これを使用して、共有キー認証を使用してデータにアクセスすることができます。 | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [ストレージ アカウント キー オペレーターのサービス ロール](#storage-account-key-operator-service-role) | ストレージ アカウント アクセス キーを一覧表示および再生成できます。 | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [ストレージ BLOB データ共同作成者](#storage-blob-data-contributor) | Azure Storage コンテナーと BLOB の読み取り、書き込み、削除を行います。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。 | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [ストレージ BLOB データ所有者](#storage-blob-data-owner) | Azure Storage Blob コンテナーとデータに対するフル アクセス (POSIX アクセスの制御の割り当てを含む) を提供します。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。 | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [ストレージ BLOB データ閲覧者](#storage-blob-data-reader) | Azure Storage コンテナーと BLOB の読み取りと一覧表示を行います。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。 | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Storage Blob デリゲータ](#storage-blob-delegator) | Azure AD 資格情報で署名されたコンテナーまたは BLOB 用の共有アクセス署名を作成するために使用できるユーザー委任キーを取得します。 詳細については、「[ユーザー委任 SAS を作成する](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)」を参照してください。 | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [記憶域ファイル データの SMB 共有の共同作成者](#storage-file-data-smb-share-contributor) | Azure ファイル共有のファイルまたはディレクトリに対する読み取り、書き込み、削除のアクセス権を許可します。 このロールに相当する機能は Windows ファイル サーバーに組み込まれていません。 | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [記憶域ファイル データの SMB 共有の管理者特権共同作成者](#storage-file-data-smb-share-elevated-contributor) | Azure ファイル共有のファイルまたはディレクトリに対する ACL の読み取り、書き込み、削除、変更を許可します。 このロールは、Windows ファイル サーバーでのファイル共有 ACL の変更に相当します。 | a7264617-510b-434b-a828-9731dc254ea7 |
> | [ストレージ ファイル データの SMB 共有の閲覧者](#storage-file-data-smb-share-reader) | Azure ファイル共有のファイルまたはディレクトリに対する読み取りアクセスを許可します。 このロールは、Windows ファイル サーバーでのファイル共有 ACL の読み取りに相当します。 | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [ストレージ キュー データ共同作成者共同作成者](#storage-queue-data-contributor) | Azure Storage キューおよびキュー メッセージの読み取り、書き込み、削除を行います。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。 | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [ストレージ キュー データのメッセージ プロセッサ](#storage-queue-data-message-processor) | Azure Storage キューからのメッセージのピーク、取得、削除を行います。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。 | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [ストレージ キュー データ メッセージ送信者](#storage-queue-data-message-sender) | Azure Storage キューにメッセージを追加します。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。 | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [ストレージ キュー データ閲覧者](#storage-queue-data-reader) | Azure Storage キューおよびキュー メッセージの読み取りと一覧表示を行います。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。 | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Web** |  |  |
> | [Azure Maps データ閲覧者](#azure-maps-data-reader) | Azure Maps アカウントからマップ関連データを読み取るためのアクセス権を付与します。 | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Search Service Contributor](#search-service-contributor) | Search サービスを管理できます。ただし、それらへのアクセスは含まれません。 | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Web Plan Contributor](#web-plan-contributor) | Web サイトの Web プランを管理できます。ただし、それらへのアクセスは含まれません。 | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Website Contributor](#website-contributor) | Web サイト (Web プランではない) を管理できます。ただし、それらへのアクセスは含まれません。 | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Containers** |  |  |
> | [AcrDelete](#acrdelete) | acr の削除 | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | ACR イメージ署名者 | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | acr のプル | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | acr のプッシュ | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | ACR 検査データ閲覧者 | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | ACR 検査データ作成者 | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Azure Kubernetes Service クラスター管理者ロール](#azure-kubernetes-service-cluster-admin-role) | クラスター管理者の資格情報アクションを一覧表示します。 | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Azure Kubernetes Service クラスター ユーザー ロール](#azure-kubernetes-service-cluster-user-role) | クラスター ユーザーの資格情報アクションを一覧表示します。 | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **データベース** |  |  |
> | [Cosmos DB アカウントの閲覧者ロール](#cosmos-db-account-reader-role) | Cosmos DB アカウントのデータを読み取ることができます。 Azure Cosmos DB アカウントの管理については、「[DocumentDB Account Contributor](#documentdb-account-contributor)」をご覧ください。 | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Cosmos DB オペレーター](#cosmos-db-operator) | Azure Cosmos DB アカウントを管理することができます。ただし、アカウント内のデータにはアクセスできません。 アカウント キーと接続文字列へのアクセスは禁止されます。 | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Cosmos DB データベースまたはアカウントのコンテナーの復元要求を送信できます | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [DocumentDB Account Contributor](#documentdb-account-contributor) | Azure Cosmos DB アカウントを管理できます。 Azure Cosmos DB は以前は DocumentDB と呼ばれていました。 | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Redis Cache Contributor](#redis-cache-contributor) | Redis Caches を管理できます。ただし、それらへのアクセスは含まれません。 | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [SQL DB Contributor](#sql-db-contributor) | SQL データベースを管理できます。ただし、それらへのアクセスは含まれません。 また、セキュリティ関連のポリシーまたは親 SQL Server を管理することはできません。 | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [SQL マネージド インスタンス共同作成者](#sql-managed-instance-contributor) | SQL マネージド インスタンスと必要なネットワーク構成を管理することができますが、他のユーザーにアクセス権を付与することはできません。 | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [SQL Security Manager](#sql-security-manager) | SQL サーバーとデータベースのセキュリティ関連のポリシーを管理できます。ただし、それらへのアクセスは管理できません。 | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [SQL Server Contributor](#sql-server-contributor) | SQL サーバーとデータベースを管理できます。ただし、それらへのアクセスや、それらのセキュリティ関連ポリシーは管理できません。 | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Analytics** |  |  |
> | [Azure Event Hubs データ所有者](#azure-event-hubs-data-owner) | Azure Event Hubs リソースへのフル アクセスを許可します。 | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Azure Event Hubs データ受信者](#azure-event-hubs-data-receiver) | Azure Event Hubs リソースへの受信アクセスを許可します。 | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Azure Event Hubs データ送信者](#azure-event-hubs-data-sender) | Azure Event Hubs リソースへの送信アクセスを許可します。 | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Data Factory Contributor](#data-factory-contributor) | データ ファクトリまたデータ ファクトリ内の子リソースを作成し管理します。 | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Data Purger](#data-purger) | 分析データを削除することができます。 | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [HDInsight クラスター オペレーター](#hdinsight-cluster-operator) | HDInsight クラスター構成の読み取りと変更を実行できます。 | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [HDInsight ドメイン サービス共同作成者](#hdinsight-domain-services-contributor) | HDInsight Enterprise セキュリティ パッケージに必要なドメイン サービス関連の操作の読み取り、作成、変更、削除を行うことができます。 | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Log Analytics Contributor](#log-analytics-contributor) | Log Analytics 共同作成者は、すべての監視データを読み取り、監視設定を編集できます。 監視設定の編集には、VM 拡張機能の VM への追加、Azure Storage からログの収集を設定できるようにするためのストレージ アカウント キーの読み取り、Automation アカウントの作成と構成、ソリューションの追加、すべての Azure リソースでの Azure Diagnostics の構成が含まれます。 | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Log Analytics Reader](#log-analytics-reader) | Log Analytics Reader は、すべての監視データの表示と検索、およびすべての Azure リソース上の Azure Diagnostics 構成の表示など、監視設定の表示を行うことができます。 | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **ブロックチェーン** |  |  |
> | [ブロックチェーン メンバー ノードへのアクセス (プレビュー)](#blockchain-member-node-access-preview) | ブロックチェーン メンバー ノードにアクセスできるようにします | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **AI + 機械学習** |  |  |
> | [Cognitive Services 共同作成者](#cognitive-services-contributor) | Cognitive Services のキーの作成、読み取り、更新、削除、管理を行うことができます。 | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Cognitive Services データ閲覧者 (プレビュー)](#cognitive-services-data-reader-preview) | Cognitive Services データを読み取ります。 | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Cognitive Services ユーザー](#cognitive-services-user) | Cognitive Services のキーの読み取りおよび一覧表示を行うことができます。 | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **複合現実** |  |  |
> | [Spatial Anchors アカウント共同作成者](#spatial-anchors-account-contributor) | アカウントで Spatial Anchors を管理します (削除は含まない) | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Spatial Anchors アカウント所有者](#spatial-anchors-account-owner) | アカウントで Spatial Anchors を管理します (削除も含む) | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Spatial Anchors アカウント閲覧者](#spatial-anchors-account-reader) | アカウントで Spatial Anchors のプロパティを検索して読み取ります | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **統合** |  |  |
> | [API Management Service Contributor](#api-management-service-contributor) | サービスと API を管理できます | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [API Management Service Operator Role](#api-management-service-operator-role) | サービスを管理できますが、API は対象外です | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [API Management Service Reader Role](#api-management-service-reader-role) | サービスと API への読み取り専用アクセスです | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [App Configuration データ所有者](#app-configuration-data-owner) | App Configuration データへのフル アクセスを許可します。 | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [App Configuration データ閲覧者](#app-configuration-data-reader) | App Configuration データへの読み取りアクセスを許可します。 | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Azure Service Bus データ所有者](#azure-service-bus-data-owner) | Azure Service Bus リソースへのフル アクセスを許可します。 | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Azure Service Bus データ受信者](#azure-service-bus-data-receiver) | Azure Service Bus リソースへの受信アクセスを許可します。 | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Azure Service Bus データ送信者](#azure-service-bus-data-sender) | Azure Service Bus リソースへの送信アクセスを許可します。 | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Azure Stack Registration Owner](#azure-stack-registration-owner) | Azure Stack の登録を管理できます。 | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [EventGrid EventSubscription 共同作成者](#eventgrid-eventsubscription-contributor) | EventGrid のイベント サブスクリプション操作を管理できます。 | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription 閲覧者](#eventgrid-eventsubscription-reader) | EventGrid のイベント サブスクリプションを読み取ることができます。 | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [Intelligent Systems Account Contributor](#intelligent-systems-account-contributor) | Intelligent Systems のアカウントを管理できます。ただし、それらへのアクセスは含まれません。 | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Logic App Contributor](#logic-app-contributor) | ロジック アプリを管理できますが、アクセス権を変更することはできません。 | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Logic App Operator](#logic-app-operator) | ロジック アプリの読み取り、有効化、無効化ができますが、編集または更新はできません。 | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **ID** |  |  |
> | [Managed Identity Contributor](#managed-identity-contributor) | ユーザー割り当て ID の作成、読み取り、更新、削除を行います | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Managed Identity Operator](#managed-identity-operator) | ユーザー割り当て ID の読み取りと割り当てを行います | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Security** |  |  |
> | [Azure Sentinel 共同作成者](#azure-sentinel-contributor) | Azure Sentinel 共同作成者 | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Azure Sentinel 閲覧者](#azure-sentinel-reader) | Azure Sentinel 閲覧者 | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel レスポンダー](#azure-sentinel-responder) | Azure Sentinel レスポンダー | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Key Vault Contributor](#key-vault-contributor) | キー コンテナーを管理できますが、アクセスすることはできません。 | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Security Admin](#security-admin) | Security Center の表示および更新のアクセス許可。 セキュリティ閲覧者と同じアクセス許可があり、セキュリティ ポリシーの更新、アラートと推奨事項の無視も可能になります。 | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Security Assessment Contributor](#security-assessment-contributor) | 評価を Security Center にプッシュできます | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [セキュリティ マネージャー (レガシ)](#security-manager-legacy) | これは、レガシ ロールです。 代わりに Security Admin を使用してください。 | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Security Reader](#security-reader) | Security Center の表示アクセス許可。 推奨事項、警告、セキュリティ ポリシー、セキュリティの状態を閲覧できますが、変更することはできません。 | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [DevTest Labs User](#devtest-labs-user) | Azure DevTest Labs で仮想マシンの接続、起動、再起動、シャットダウンができます。 | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Lab Creator](#lab-creator) | Azure Lab アカウントで管理対象のラボを作成、管理、削除できます。 | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **監視** |  |  |
> | [Application Insights Component Contributor](#application-insights-component-contributor) | Application Insights コンポーネントを管理できます | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Application Insights スナップショット デバッガーで収集されたデバック スナップショットの表示とダウンロードを実行できるアクセス許可をユーザーに与えます。 これらのアクセス許可は、[所有者](#owner)ロールまたは[共同作成者](#contributor)ロールには含まれないことに注意してください。 ユーザーに Application Insights スナップショット デバッガー ロールを与える場合は、そのロールをユーザーに直接付与する必要があります。 このロールは、カスタム ロールに追加されるときに認識されません。 | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Monitoring Contributor](#monitoring-contributor) | すべての監視データを読み取り、監視設定を編集できます。 [「Azure Monitor での役割、アクセス許可、およびセキュリティの概要」](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles)も参照してください。 | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [監視メトリック パブリッシャー](#monitoring-metrics-publisher) | Azure リソースに対するメトリックの公開を有効にします | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Monitoring Reader](#monitoring-reader) | すべての監視データ (メトリック、ログなど) を読み取ることができます。 [「Azure Monitor での役割、アクセス許可、およびセキュリティの概要」](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles)も参照してください。 | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Workbook Contributor](#workbook-contributor) | 共有ブックを保存できます。 | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Workbook Reader](#workbook-reader) | ブックの読み取りが可能です。 | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **管理 + ガバナンス** |  |  |
> | [Automation Job Operator](#automation-job-operator) | Automation Runbook を使用してジョブを作成および管理します。 | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Automation Operator](#automation-operator) | Automation オペレーターはジョブを開始、停止、中断、再開することができます | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Automation Runbook Operator](#automation-runbook-operator) | Runbook のジョブを作成する方法については、Runbook のプロパティを参照してください。 | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Azure Connected Machine のオンボード](#azure-connected-machine-onboarding) | Azure Connected Machine をオンボードできます。 | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Azure Connected Machine のリソース管理者](#azure-connected-machine-resource-administrator) | Azure Connected Machine の読み取り、書き込み、削除、再オンボードを実行できます。 | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Billing Reader](#billing-reader) | 課金データへの読み取りアクセスを許可します | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [ブループリント共同作成者](#blueprint-contributor) | ブループリントの定義を管理できますが、それらを割り当てることはできません。 | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [ブループリント オペレーター](#blueprint-operator) | 既存の発行済みのブループリントを割り当てることはできますが、ブループリントの新規作成はできません。 これは、ユーザーが割り当てたマネージド ID を使用して割り当てが行われた場合にのみ機能することに注意してください。 | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Cost Management 共同作成者](#cost-management-contributor) | コストを表示し、コストの構成 (予算、エクスポートなど) を管理することができます。 | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Cost Management 閲覧者](#cost-management-reader) | コストのデータと構成 (予算、エクスポートなど) を表示することができます。 | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Hierarchy Settings Administrator](#hierarchy-settings-administrator) | ユーザーに、階層設定の編集と削除を許可します | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Managed Application Contributor Role](#managed-application-contributor-role) | マネージド アプリケーション リソースの作成を許可します。 | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Managed Application Operator Role](#managed-application-operator-role) | マネージド アプリケーション リソースに対する読み取りとアクションの実行が可能です。 | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Managed Applications 閲覧者](#managed-applications-reader) | マネージド アプリおよび要求 JIT アクセスでリソースを読み取ることができます。 | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [マネージド サービスの登録割り当て削除ロール](#managed-services-registration-assignment-delete-role) | マネージド サービスの登録割り当て削除ロールを使用すると、テナント管理ユーザーは、テナントに割り当てられている登録割り当てを削除できます。 | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [管理グループ共同作成者](#management-group-contributor) | 管理グループ共同作成者ロール | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [管理グループ閲覧者](#management-group-reader) | 管理グループ閲覧者ロール | ac63b705-f282-497d-ac71-919bf39d939d |
> | [New Relic APM Account Contributor](#new-relic-apm-account-contributor) | New Relic Application Performance Management のアカウントとアプリケーションを管理できます。ただし、それらへのアクセスは含まれません。 | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Policy Insights データ ライター (プレビュー)](#policy-insights-data-writer-preview) | リソース ポリシーに対する読み取りアクセスとリソース コンポーネント ポリシー イベントへの書き込みアクセスを許可します。 | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [リソース ポリシーの共同作成者](#resource-policy-contributor) | リソース ポリシーの作成または変更、サポート チケットの作成、リソースまたは階層の読み取りを行う権限を持つユーザー。 | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Site Recovery Contributor](#site-recovery-contributor) | 資格情報コンテナーの作成とロールの割り当てを除く、Site Recovery サービスを管理できます | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Site Recovery Operator](#site-recovery-operator) | フェールオーバーとフェールバックを実行できますが、その他の Site Recovery 管理操作は実行しません | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Site Recovery Reader](#site-recovery-reader) | Site Recovery の状態を表示できますが、その他の管理操作は実行できません | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Support Request Contributor](#support-request-contributor) | Support request を作成して管理できます | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [タグ共同作成者](#tag-contributor) | エンティティ自体へのアクセスを提供することなく、エンティティのタグを管理できます。 | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **その他** |  |  |
> | [BizTalk Contributor](#biztalk-contributor) | BizTalk Services を管理できます。ただし、それらへのアクセスは含まれません。 | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Scheduler Job Collections Contributor](#scheduler-job-collections-contributor) | スケジューラ ジョブ コレクションを管理できます。ただし、それらへのアクセスは含まれません。 | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |


## <a name="general"></a>全般


### <a name="contributor"></a>Contributor

リソースへのアクセス権の付与以外のすべてを管理できます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | * | あらゆる種類のリソースの作成と管理 |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | ロール、ポリシーの割り当て、ポリシーの定義、ポリシー セットの定義を削除します。 |
> | Microsoft.Authorization/*/Write | ロール、ロールの割り当て、ポリシーの割り当て、ポリシーの定義、ポリシー セットの定義を作成します。 |
> | Microsoft.Authorization/elevateAccess/Action | テナント スコープで、ユーザー アクセス管理者のアクセス権を呼び出し元に付与します。 |
> | Microsoft.Blueprint/blueprintAssignments/write | 任意のブループリント割り当てを作成または更新します |
> | Microsoft.Blueprint/blueprintAssignments/delete | 任意のブループリント割り当てを削除します |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>所有者

リソースへのアクセスを含め、すべてを管理できます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | * | あらゆる種類のリソースの作成と管理 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything, including access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>Reader

すべてを表示できますが、変更することはできません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything, but not make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>User Access Administrator

Azure リソースに対するユーザー アクセスを管理します。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | Microsoft.Authorization/* | 承認の管理 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>Compute


### <a name="classic-virtual-machine-contributor"></a>Classic Virtual Machine Contributor

従来の仮想マシンを管理できますが、アクセスすることはできません。また、接続先の仮想ネットワークやストレージ アカウントにもアクセスできません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.ClassicCompute/domainNames/* | 従来のコンピューティング ドメイン名の作成と管理 |
> | Microsoft.ClassicCompute/virtualMachines/* | 仮想マシンの作成と管理 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | 予約済み IP をリンクします。 |
> | Microsoft.ClassicNetwork/reservedIps/read | 予約済み IP を取得します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | 仮想ネットワークに参加します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/read | 仮想ネットワークを取得します。 |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | ストレージ アカウント ディスクを返します。 |
> | Microsoft.ClassicStorage/storageAccounts/images/read | ストレージ アカウント イメージを返します。 (非推奨になりました。 'Microsoft.ClassicStorage/storageAccounts/vmImages' を使用してください。) |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | ストレージ アカウントのアクセス キーを一覧表示します。 |
> | Microsoft.ClassicStorage/storageAccounts/read | 特定のアカウントのストレージ アカウントを返します。 |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>Virtual Machine Administrator Login

ポータルで仮想マシンを表示し、管理者としてログインします

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Network/publicIPAddresses/read | パブリック IP アドレス定義を取得します。 |
> | Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | Microsoft.Network/loadBalancers/read | ロード バランサー定義を取得します。 |
> | Microsoft.Network/networkInterfaces/read | ネットワーク インターフェイスの定義を取得します。  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | 仮想マシンに通常のユーザーとしてログインします。 |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Windows 管理者または Linux のルート ユーザーの権限で仮想マシンにログインします。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>Virtual Machine Contributor

仮想マシンを管理できますが、アクセスすることはできません。また、接続先の仮想ネットワークやストレージ アカウントにもアクセスできません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Compute/availabilitySets/* | コンピューティング可用性セットの作成と管理 |
> | Microsoft.Compute/locations/* | コンピューティングの場所の作成と管理 |
> | Microsoft.Compute/virtualMachines/* | 仮想マシンの作成と管理 |
> | Microsoft.Compute/virtualMachineScaleSets/* | 仮想マシン スケールセットの作成と管理 |
> | Microsoft.Compute/disks/write | 新しいディスクを作成するか、既存のディスクを更新します。 |
> | Microsoft.Compute/disks/read | ディスクのプロパティを取得します。 |
> | Microsoft.Compute/disks/delete | ディスクを削除します。 |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | アプリケーション ゲートウェイのバックエンド アドレス プールを接続します。 警告不可能です。 |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | ロード バランサーのバックエンド アドレス プールを接続します。 警告不可能です。 |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | ロード バランサーの受信 NAT プールを接続します。 警告不可能です。 |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | ロード バランサーのインバウンド NAT 規則を接続します。 警告不可能です。 |
> | Microsoft.Network/loadBalancers/probes/join/action | ロード バランサーのプローブの使用を許可します。 たとえば、このアクセス許可では、VM スケール セットの healthProbe プロパティでプローブを参照できます。 警告不可能です。 |
> | Microsoft.Network/loadBalancers/read | ロード バランサー定義を取得します。 |
> | Microsoft.Network/locations/* | ネットワークの場所の作成と管理 |
> | Microsoft.Network/networkInterfaces/* | ネットワーク インターフェイスの作成と管理 |
> | Microsoft.Network/networkSecurityGroups/join/action | ネットワーク セキュリティ グループに参加します。 警告不可能です。 |
> | Microsoft.Network/networkSecurityGroups/read | ネットワーク セキュリティ グループの定義を取得します。 |
> | Microsoft.Network/publicIPAddresses/join/action | パブリック IP アドレスに接続します。 警告不可能です。 |
> | Microsoft.Network/publicIPAddresses/read | パブリック IP アドレス定義を取得します。 |
> | Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | Microsoft.Network/virtualNetworks/subnets/join/action | 仮想ネットワークに参加します。 警告不可能です。 |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | バックアップの保護インテントを作成します |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 保護された項目のオブジェクトの詳細を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | バックアップ保護項目を作成します。 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | すべての保護ポリシーを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | 保護ポリシーを作成します。 |
> | Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
> | Microsoft.RecoveryServices/Vaults/write | "コンテナーの作成" 操作では、"コンテナー" 型の Azure リソースを作成します。 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
> | Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>Virtual Machine User Login

ポータルで仮想マシンを表示し、通常のユーザーとしてログインします。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Network/publicIPAddresses/read | パブリック IP アドレス定義を取得します。 |
> | Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | Microsoft.Network/loadBalancers/read | ロード バランサー定義を取得します。 |
> | Microsoft.Network/networkInterfaces/read | ネットワーク インターフェイスの定義を取得します。  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | 仮想マシンに通常のユーザーとしてログインします。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>ネットワーク


### <a name="cdn-endpoint-contributor"></a>CDN Endpoint Contributor

CDN エンドポイントを管理できますが、アクセス権を他のユーザーに付与することはできません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>CDN Endpoint Reader

CDN エンドポイントを表示できますが、変更はできません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>CDN Profile Contributor

CDN プロファイルとそのエンドポイントを管理できますが、アクセス権を他のユーザーに付与することはできません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>CDN Profile Reader

CDN プロファイルとそのエンドポイントを表示できますが、変更はできません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>Classic Network Contributor

従来のネットワークを管理できます。ただし、それらへのアクセスは含まれません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.ClassicNetwork/* | 従来のネットワークの作成と管理 |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>DNS Zone Contributor

Azure DNS の DNS ゾーンとレコード セットを管理できますが、それにアクセスできるユーザーを制御することはできません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Network/dnsZones/* | DNS ゾーンとレコードの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>Network Contributor

ネットワークを管理できます。ただし、それらへのアクセスは含まれません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Network/* | ネットワークの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Traffic Manager Contributor

Traffic Manager プロファイルを管理できますが、それにアクセスできるユーザーを制御することはできません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>ストレージ


### <a name="avere-contributor"></a>Avere 共同作成者

Avere vFXT クラスターを作成および管理できます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Compute/*/read |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft.Compute/disks/* |  |
> | Microsoft.Network/*/read |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | Microsoft.Network/virtualNetworks/subnets/read | 仮想ネットワーク サブネットの定義を取得します。 |
> | Microsoft.Network/virtualNetworks/subnets/join/action | 仮想ネットワークに参加します。 警告不可能です。 |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | ストレージ アカウントや SQL Database などのリソースをサブネットに結合します。 警告不可能です。 |
> | Microsoft.Network/networkSecurityGroups/join/action | ネットワーク セキュリティ グループに参加します。 警告不可能です。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Storage/*/read |  |
> | Microsoft.Storage/storageAccounts/* | ストレージ アカウントの作成と管理 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | Microsoft.Resources/subscriptions/resourceGroups/resources/read | リソース グループのリソースを取得します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | BLOB を削除した結果を返します。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | BLOB または BLOB の一覧を返します。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | BLOB の書き込みの結果を返します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Avere オペレーター

クラスターを管理するために Avere vFXT クラスターによって使用されます

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Compute/virtualMachines/read | 仮想マシンのプロパティを取得します。 |
> | Microsoft.Network/networkInterfaces/read | ネットワーク インターフェイスの定義を取得します。  |
> | Microsoft.Network/networkInterfaces/write | ネットワーク インターフェイスを作成するか、既存のネットワーク インターフェイスを更新します。  |
> | Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | Microsoft.Network/virtualNetworks/subnets/read | 仮想ネットワーク サブネットの定義を取得します。 |
> | Microsoft.Network/virtualNetworks/subnets/join/action | 仮想ネットワークに参加します。 警告不可能です。 |
> | Microsoft.Network/networkSecurityGroups/join/action | ネットワーク セキュリティ グループに参加します。 警告不可能です。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | コンテナーを削除した結果を返します。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | コンテナーの一覧を返します |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | BLOB コンテナーのプット結果を返します |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | BLOB を削除した結果を返します。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | BLOB または BLOB の一覧を返します。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | BLOB の書き込みの結果を返します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>Backup Contributor

バックアップ サービスを管理できますが、資格情報コンテナーの作成や他のユーザーに対するアクセス権の付与を行うことはできません

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | バックアップ管理操作の結果の管理 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Recovery Services コンテナーのバックアップ ファブリック内でのバックアップ コンテナーの作成および管理 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | コンテナーの一覧を更新します。 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | バックアップ ジョブの作成および管理 |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | ジョブをエクスポートします。 |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | バックアップ管理操作の結果の作成および管理 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | バックアップ ポリシーの作成および管理 |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | バックアップできるアイテムの作成および管理 |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | バックアップ アイテムの作成および管理 |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | バックアップ アイテムを保持するコンテナーの作成および管理 |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services の保護された項目と保護されたサーバーの概要を返します。 |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Recovery Services コンテナー内のバックアップに関連する証明書の作成および管理 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | コンテナーに関連する拡張情報の作成および管理 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services コンテナーのアラートを取得します。 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | 登録済み ID の管理 |
> | Microsoft.RecoveryServices/Vaults/usages/* | Recovery Services コンテナーの使用状況の作成および管理 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | 保護された項目に対する操作を検証します |
> | Microsoft.RecoveryServices/Vaults/write | "コンテナーの作成" 操作では、"コンテナー" 型の Azure リソースを作成します。 |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Recovery Services コンテナーに対するバックアップ操作の状態を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | コンテナーに登録されているすべてのバックアップ管理サーバーを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | すべての保護可能なコンテナーを取得します。 |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Recovery Services コンテナーのバックアップの状態を確認します。 |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | 機能を検証します。 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | アラートを解決する。 |
> | Microsoft.RecoveryServices/operations/read | リソース プロバイダーの操作の一覧を返します。 |
> | Microsoft.RecoveryServices/locations/operationStatus/read | 特定の操作の操作の状態を取得します |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | すべてのバックアップ保護の意図を一覧表示します |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>Backup Operator

バックアップ サービスを管理できます (バックアップの削除、資格情報コンテナーの作成、他のユーザーに対するアクセス権の付与を除く)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | 操作の状態を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | 保護コンテナーに対して実行された操作の結果を取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | 保護された項目のバックアップを実行します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 保護された項目に対して実行された操作の結果を取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 保護された項目に対して実行された操作の状態を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 保護された項目のオブジェクトの詳細を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | 保護された項目のインスタント項目回復をプロビジョニングします。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 保護された項目の復旧ポイントを取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | 保護された項目の復旧ポイントを復元します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | 保護された項目のインスタント項目回復を取り消します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | バックアップ保護項目を作成します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | すべての登録済みコンテナーを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | コンテナーの一覧を更新します。 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | バックアップ ジョブの作成および管理 |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | ジョブをエクスポートします。 |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | バックアップ管理操作の結果の作成および管理 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | ポリシー操作の結果を取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | すべての保護ポリシーを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | バックアップできるアイテムの作成および管理 |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | すべての保護された項目の一覧を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | サブスクリプションに属するすべてのコンテナーを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services の保護された項目と保護されたサーバーの概要を返します。 |
> | Microsoft.RecoveryServices/Vaults/certificates/write | "リソース証明書を更新" 操作では、リソース/コンテナー資格情報証明書を更新します。 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services コンテナーのアラートを取得します。 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | "操作結果を取得" 操作を使用すると、非同期で送信された操作の状態と結果を取得できます。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | " コンテナーを取得" 操作を使用すると、リソースの登録済みコンテナーを取得できます。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | "サービス コンテナーを登録" 操作を使用すると、コンテナーを Recovery Services に登録できます。 |
> | Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | 保護された項目に対する操作を検証します |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Recovery Services コンテナーに対するバックアップ操作の状態を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | ポリシー操作の状態を取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | 登録済みコンテナーを作成します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | コンテナー内のワークロードを照会します。 |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | コンテナーに登録されているすべてのバックアップ管理サーバーを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | バックアップの保護インテントを作成します |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | バックアップ保護の意図を取得します |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | すべての保護可能なコンテナーを取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | コンテナー内のすべての項目を取得します。 |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Recovery Services コンテナーのバックアップの状態を確認します。 |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | 機能を検証します。 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | アラートを解決する。 |
> | Microsoft.RecoveryServices/operations/read | リソース プロバイダーの操作の一覧を返します。 |
> | Microsoft.RecoveryServices/locations/operationStatus/read | 特定の操作の操作の状態を取得します |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | すべてのバックアップ保護の意図を一覧表示します |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>Backup Reader

バックアップ サービスを表示できますが、変更を行うことはできません

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | 操作の状態を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | 保護コンテナーに対して実行された操作の結果を取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 保護された項目に対して実行された操作の結果を取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 保護された項目に対して実行された操作の状態を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 保護された項目のオブジェクトの詳細を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 保護された項目の復旧ポイントを取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | すべての登録済みコンテナーを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | ジョブ操作の結果を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | すべてのジョブ オブジェクトを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | ジョブをエクスポートします。 |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Recovery Services コンテナーに対するバックアップ操作の結果を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | ポリシー操作の結果を取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | すべての保護ポリシーを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | すべての保護された項目の一覧を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | サブスクリプションに属するすべてのコンテナーを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services の保護された項目と保護されたサーバーの概要を返します。 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services コンテナーのアラートを取得します。 |
> | Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | "操作結果を取得" 操作を使用すると、非同期で送信された操作の状態と結果を取得できます。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | " コンテナーを取得" 操作を使用すると、リソースの登録済みコンテナーを取得できます。 |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Recovery Services コンテナーのストレージ構成を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Recovery Services コンテナーの構成を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Recovery Services コンテナーに対するバックアップ操作の状態を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | ポリシー操作の状態を取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | コンテナーに登録されているすべてのバックアップ管理サーバーを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | バックアップ保護の意図を取得します |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | コンテナー内のすべての項目を取得します。 |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Recovery Services コンテナーのバックアップの状態を確認します。 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | アラートを解決する。 |
> | Microsoft.RecoveryServices/operations/read | リソース プロバイダーの操作の一覧を返します。 |
> | Microsoft.RecoveryServices/locations/operationStatus/read | 特定の操作の操作の状態を取得します |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | すべてのバックアップ保護の意図を一覧表示します |
> | Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | 機能を検証します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>Classic Storage Account Contributor

従来のストレージ アカウントを管理できますが、アクセスすることはできません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.ClassicStorage/storageAccounts/* | ストレージ アカウントの作成と管理 |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>従来のストレージ アカウント キー オペレーターのサービス ロール

従来のストレージ アカウント キー オペレーターは、従来のストレージ アカウントでのキーの一覧表示と再生成を行うことができます

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | ストレージ アカウントのアクセス キーを一覧表示します。 |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | ストレージ アカウントの既存のアクセス キーを再生成します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Data Box Contributor

Data Box サービスですべてを管理できます (他のユーザーに対するアクセス権の付与を除く)。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | Microsoft.Databox/* |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Data Box 閲覧者

Data Box サービスを管理できます (注文の作成または注文の詳細の編集、および他のユーザーに対するアクセス権の付与を除く)。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | 注文に関連する暗号化されていない資格情報を一覧表示します。 |
> | Microsoft.Databox/locations/availableSkus/action | このメソッドは、使用可能な SKU の一覧を返します。 |
> | Microsoft.Databox/locations/validateInputs/action | このメソッドでは、すべての種類の検証が行われます。 |
> | Microsoft.Databox/locations/regionConfiguration/action | このメソッドでは、リージョンの構成が返されます。 |
> | Microsoft.Databox/locations/validateAddress/action | 配送先住所を検証し、存在する場合には別の住所を指定します。 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Data Lake Analytics Developer

独自のジョブを送信、監視、管理できますが、Data Lake Analytics アカウントを作成または削除することはできません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | DataLakeAnalytics アカウントを削除します。 |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | 他のユーザーによって送信されたジョブを取り消すアクセス許可を付与します。 |
> | Microsoft.DataLakeAnalytics/accounts/Write | DataLakeAnalytics アカウントを作成または更新します。 |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | DataLakeAnalytics アカウントのリンクされた DataLakeStore アカウントを作成または更新します。 |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | DataLakeAnalytics アカウントから DataLakeStore アカウントのリンクを解除します。 |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | DataLakeAnalytics アカウントのリンクされたストレージ アカウントを作成または更新します。 |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | DataLakeAnalytics アカウントからストレージ アカウントをリンク解除します。 |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | ファイアウォール規則を作成または更新します。 |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | ファイアウォール規則を削除します。 |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | コンピューティング ポリシーを作成または更新します。 |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | コンピューティング ポリシーを削除します。 |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>Reader and Data Access

すべてを表示することができますが、ストレージ アカウントや含まれるリソースの削除や作成はできません。 ストレージ アカウント キーへのアクセスを使用して、ストレージ アカウントに含まれるすべてのデータへの読み取り/書き込みアクセスも許可されます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
> | Microsoft.Storage/storageAccounts/ListAccountSas/action | 指定されたストレージ アカウントのアカウント SAS トークンを返します。 |
> | Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>Storage Account Contributor

ストレージ アカウントの管理を許可します。 アカウント キーへのアクセスを提供します。これを使用して、共有キー認証を使用してデータにアクセスすることができます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Insights/diagnosticSettings/* | 分析サーバーの診断の設定の作成、更新、または読み取りを行います |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | ストレージ アカウントや SQL Database などのリソースをサブネットに結合します。 警告不可能です。 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Storage/storageAccounts/* | ストレージ アカウントの作成と管理 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>ストレージ アカウント キー オペレーターのサービス ロール

ストレージ アカウント アクセス キーを一覧表示および再生成できます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | 指定されたストレージ アカウントのアクセス キーを再生成します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>ストレージ BLOB データ共同作成者

Azure Storage コンテナーと BLOB の読み取り、書き込み、削除を行います。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | コンテナーを削除します。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | コンテナーまたはコンテナーの一覧を返します。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | コンテナーのメタデータまたはプロパティを変更します。 |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Blob service 用のユーザー委任キーを返します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | BLOB を削除する |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | BLOB または BLOB の一覧を返します。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action | パス間で BLOB を移動します |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | BLOB に書き込みます。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>ストレージ BLOB データ所有者

Azure Storage Blob コンテナーとデータに対するフル アクセス (POSIX アクセスの制御の割り当てを含む) を提供します。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | コンテナーのフル アクセス許可。 |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Blob service 用のユーザー委任キーを返します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | BLOB のフル アクセス許可。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>ストレージ BLOB データ閲覧者

Azure Storage コンテナーと BLOB の読み取りと一覧表示を行います。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | コンテナーまたはコンテナーの一覧を返します。 |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Blob service 用のユーザー委任キーを返します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | BLOB または BLOB の一覧を返します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>Storage Blob デリゲータ

Azure AD 資格情報で署名されたコンテナーまたは BLOB 用の共有アクセス署名を作成するために使用できるユーザー委任キーを取得します。 詳細については、「[ユーザー委任 SAS を作成する](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)」を参照してください。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Blob service 用のユーザー委任キーを返します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>記憶域ファイル データの SMB 共有の共同作成者

Azure ファイル共有のファイルまたはディレクトリに対する読み取り、書き込み、削除のアクセス権を許可します。 このロールに相当する機能は Windows ファイル サーバーに組み込まれていません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | ファイル/フォルダーまたはファイル/フォルダーの一覧を返します。 |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | ファイルの書き込みまたはフォルダーの作成の結果を返します。 |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | ファイル/フォルダーの削除の結果を返します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>記憶域ファイル データの SMB 共有の管理者特権共同作成者

Azure ファイル共有のファイルまたはディレクトリに対する ACL の読み取り、書き込み、削除、変更を許可します。 このロールは、Windows ファイル サーバーでのファイル共有 ACL の変更に相当します。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | ファイル/フォルダーまたはファイル/フォルダーの一覧を返します。 |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | ファイルの書き込みまたはフォルダーの作成の結果を返します。 |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | ファイル/フォルダーの削除の結果を返します。 |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | ファイル/フォルダーに対するアクセス許可の変更の結果を返します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>記憶域ファイル データの SMB 共有の閲覧者

Azure ファイル共有のファイルまたはディレクトリに対する読み取りアクセスを許可します。 このロールは、Windows ファイル サーバーでのファイル共有 ACL の読み取りに相当します。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | ファイル/フォルダーまたはファイル/フォルダーの一覧を返します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>ストレージ キュー データ共同作成者

Azure Storage キューおよびキュー メッセージの読み取り、書き込み、削除を行います。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | キューを削除します。 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | キューまたはキューの一覧を返します。 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | キューのメタデータまたはプロパティを変更します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | 1 つまたは複数のメッセージをキューから削除します。 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | 1 つまたは複数のメッセージをキューからピークまたは取得します。 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | メッセージをキューに追加します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>ストレージ キュー データのメッセージ プロセッサ

Azure Storage キューからのメッセージのピーク、取得、削除を行います。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | メッセージをピークします。 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | メッセージを取得および削除します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>ストレージ キュー データ メッセージ送信者

Azure Storage キューにメッセージを追加します。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | メッセージをキューに追加します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>ストレージ キュー データ閲覧者

Azure Storage キューおよびキュー メッセージの読み取りと一覧表示を行います。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | キューまたはキューの一覧を返します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | 1 つまたは複数のメッセージをキューからピークまたは取得します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>Web


### <a name="azure-maps-data-reader"></a>Azure Maps データ閲覧者

Azure Maps アカウントからマップ関連データを読み取るためのアクセス権を付与します。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.Maps/accounts/*/read |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Search Service Contributor

Search サービスを管理できます。ただし、それらへのアクセスは含まれません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Search/searchServices/* | 検索サービスの作成と管理 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Web Plan Contributor

Web サイトの Web プランを管理できます。ただし、それらへのアクセスは含まれません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | Microsoft.Web/serverFarms/* | サーバー ファームの作成と管理 |
> | Microsoft.Web/hostingEnvironments/Join/Action | App Service Environment に参加します |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>Website Contributor

Web サイト (Web プランではない) を管理できます。ただし、それらへのアクセスは含まれません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Insights/components/* | Insights コンポーネントの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | Microsoft.Web/certificates/* | Web サイト証明書の作成と管理 |
> | Microsoft.Web/listSitesAssignedToHostName/read | ホスト名に割り当てられたサイトの名前を取得します。 |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | App Service プランのプロパティを取得します。 |
> | Microsoft.Web/sites/* | Web サイトの作成と管理 (サイト作成では、関連付けられた App Service プランに対する書き込みアクセス許可も必要です) |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>Containers


### <a name="acrdelete"></a>AcrDelete

acr の削除

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.ContainerRegistry/registries/artifacts/delete | コンテナー レジストリの成果物を削除します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner

ACR イメージ署名者

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | コンテナー レジストリのコンテンツの信頼メタデータをプッシュ/プルします。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull

acr のプル

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | コンテナー レジストリからイメージをプルまたは取得します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush

acr のプッシュ

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | コンテナー レジストリからイメージをプルまたは取得します。 |
> | Microsoft.ContainerRegistry/registries/push/write | コンテナー レジストリにイメージをプッシュするか書き込みます。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>AcrQuarantineReader

ACR 検査データ閲覧者

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.ContainerRegistry/registries/quarantine/read | コンテナー レジストリから検疫済みのイメージをプルまたは取得します |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

ACR 検査データ作成者

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.ContainerRegistry/registries/quarantine/read | コンテナー レジストリから検疫済みのイメージをプルまたは取得します |
> | Microsoft.ContainerRegistry/registries/quarantine/write | 検疫済みイメージの検疫状態を書き込むか変更します |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Azure Kubernetes Service クラスター管理者ロール

クラスター管理者の資格情報アクションを一覧表示します。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | 管理対象クラスターの clusterAdmin 資格情報を一覧表示します。 |
> | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | 資格情報の一覧の取得を使用し、ロール名を指定してマネージド クラスターのアクセス プロファイルを取得します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Azure Kubernetes Service クラスター ユーザー ロール

クラスター ユーザーの資格情報アクションを一覧表示します。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | 管理対象クラスターの clusterUser 資格情報を一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>データベース


### <a name="cosmos-db-account-reader-role"></a>Cosmos DB アカウントの閲覧者ロール

Cosmos DB アカウントのデータを読み取ることができます。 Azure Cosmos DB アカウントの管理については、「[DocumentDB Account Contributor](#documentdb-account-contributor)」をご覧ください。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.DocumentDB/*/read | 任意のコレクションの読み取り |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | データベース アカウントの読み取り専用キーを読み取ります。 |
> | Microsoft.Insights/MetricDefinitions/read | メトリック定義を読み取ります。 |
> | Microsoft.Insights/Metrics/read | メトリックを読み取ります。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Cosmos DB オペレーター

Azure Cosmos DB アカウントを管理することができます。ただし、アカウント内のデータにはアクセスできません。 アカウント キーと接続文字列へのアクセスは禁止されます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | ストレージ アカウントや SQL Database などのリソースをサブネットに結合します。 警告不可能です。 |
> | **NotActions** |  |
> | Microsoft.DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

Cosmos DB データベースまたはアカウントのコンテナーの復元要求を送信できます

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | バックアップを構成するための要求を送信します |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | 復元要求を送信します |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>DocumentDB Account Contributor

Azure Cosmos DB アカウントを管理できます。 Azure Cosmos DB は以前は DocumentDB と呼ばれていました。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.DocumentDb/databaseAccounts/* | Azure Cosmos DB アカウントの作成と管理 |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | ストレージ アカウントや SQL Database などのリソースをサブネットに結合します。 警告不可能です。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Redis Cache Contributor

Redis Caches を管理できます。ただし、それらへのアクセスは含まれません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Cache/redis/* | Redis キャッシュの作成と管理 |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>SQL DB Contributor

SQL データベースを管理できます。ただし、それらへのアクセスは含まれません。 また、セキュリティ関連のポリシーまたは親 SQL Server を管理することはできません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | SQL データベースの作成と管理 |
> | Microsoft.Sql/servers/read | サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | Microsoft.Insights/metrics/read | メトリックを読み取ります。 |
> | Microsoft.Insights/metricDefinitions/read | メトリック定義を読み取ります。 |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | 監査ポリシーの編集 |
> | Microsoft.Sql/servers/databases/auditingSettings/* | 監査設定の編集 |
> | Microsoft.Sql/servers/databases/auditRecords/read | データベースの BLOB 監査レコードを取得します。 |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | 接続ポリシーの編集 |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | データ マスク ポリシーの編集 |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | セキュリティ警告ポリシーの編集 |
> | Microsoft.Sql/servers/databases/securityMetrics/* | セキュリティ基準の編集 |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>SQL マネージド インスタンス共同作成者

SQL マネージド インスタンスと必要なネットワーク構成を管理することができますが、他のユーザーにアクセス権を付与することはできません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Network/networkSecurityGroups/* |  |
> | Microsoft.Network/routeTables/* |  |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/managedInstances/* |  |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | Microsoft.Network/virtualNetworks/subnets/* |  |
> | Microsoft.Network/virtualNetworks/* |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Insights/metrics/read | メトリックを読み取ります。 |
> | Microsoft.Insights/metricDefinitions/read | メトリック定義を読み取ります。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>SQL Security Manager

SQL サーバーとデータベースのセキュリティ関連のポリシーを管理できます。ただし、それらへのアクセスは管理できません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | ストレージ アカウントや SQL Database などのリソースをサブネットに結合します。 警告不可能です。 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | SQL サーバー監査ポリシーの作成と管理 |
> | Microsoft.Sql/servers/auditingSettings/* | SQL サーバー監査設定の作成と管理 |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | 指定されたサーバーで構成されている拡張サーバー BLOB 監査ポリシーの詳細を取得します。 |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | SQL サーバー データベース監査ポリシーの作成と管理 |
> | Microsoft.Sql/servers/databases/auditingSettings/* | SQL サーバー データベース監査設定の作成と管理 |
> | Microsoft.Sql/servers/databases/auditRecords/read | データベースの BLOB 監査レコードを取得します。 |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | SQL サーバー データベース接続ポリシーの作成と管理 |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | SQL サーバー データベース データ マスク ポリシーの作成と管理 |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | 指定されたデータベースで構成されている拡張 BLOB 監査ポリシーの詳細を取得します。 |
> | Microsoft.Sql/servers/databases/read | データベースの一覧を返すか、指定されたデータベースのプロパティを取得します。 |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/read | データベースのスキーマを取得します。 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | データベースの列を取得します。 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | データベースのテーブルを取得します。 |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | SQL サーバー データベース セキュリティの警告のポリシーの作成と管理 |
> | Microsoft.Sql/servers/databases/securityMetrics/* | SQL サーバー データベース セキュリティ基準の作成と管理 |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/transparentDataEncryption/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。 |
> | Microsoft.Sql/servers/securityAlertPolicies/* | SQL サーバー セキュリティの警告のポリシーの作成と管理 |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>SQL Server Contributor

SQL サーバーとデータベースを管理できます。ただし、それらへのアクセスや、それらのセキュリティ関連ポリシーは管理できません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | SQL サーバーの作成と管理 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | Microsoft.Insights/metrics/read | メトリックを読み取ります。 |
> | Microsoft.Insights/metricDefinitions/read | メトリック定義を読み取ります。 |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | SQL サーバー監査ポリシーの編集 |
> | Microsoft.Sql/servers/auditingSettings/* | SQL サーバー監査設定の編集 |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | SQL サーバー データベース監査ポリシーの編集 |
> | Microsoft.Sql/servers/databases/auditingSettings/* | SQL サーバー データベース監査設定の編集 |
> | Microsoft.Sql/servers/databases/auditRecords/read | データベースの BLOB 監査レコードを取得します。 |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | SQL サーバー データベース接続ポリシーの編集 |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | SQL サーバー データベース データ マスク ポリシーの編集 |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | SQL サーバー データベースのセキュリティ警告ポリシーの編集 |
> | Microsoft.Sql/servers/databases/securityMetrics/* | SQL サーバー データベースのセキュリティ基準の編集 |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | SQL サーバーのセキュリティ警告ポリシーの編集 |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>Analytics


### <a name="azure-event-hubs-data-owner"></a>Azure Event Hubs データ所有者

Azure Event Hubs リソースへのフル アクセスを許可します。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.EventHub/* |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.EventHub/* |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Azure Event Hubs データ受信者

Azure Event Hubs リソースへの受信アクセスを許可します。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.EventHub/*/eventhubs/consumergroups/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.EventHub/*/receive/action |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Azure Event Hubs データ送信者

Azure Event Hubs リソースへの送信アクセスを許可します。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.EventHub/*/eventhubs/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.EventHub/*/send/action |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Data Factory Contributor

データ ファクトリまたデータ ファクトリ内の子リソースを作成し管理します。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.DataFactory/dataFactories/* | Data Factory と Data Factory に含まれる子リソースを作成および管理します。 |
> | Microsoft.DataFactory/factories/* | Data Factory と Data Factory に含まれる子リソースを作成および管理します。 |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | Microsoft.EventGrid/eventSubscriptions/write | eventSubscription を作成または更新します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>Data Purger

分析データを削除することができます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Application Insights からデータを削除します。 |
> | Microsoft.OperationalInsights/workspaces/*/read | Log Analytics のデータの表示 |
> | Microsoft.OperationalInsights/workspaces/purge/action | ワークスペースから指定されたデータを削除します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>HDInsight クラスター オペレーター

HDInsight クラスター構成の読み取りと変更を実行できます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.HDInsight/*/read |  |
> | Microsoft.HDInsight/clusters/getGatewaySettings/action | HDInsight クラスター向けのアプリケーションを取得します |
> | Microsoft.HDInsight/clusters/updateGatewaySettings/action | HDInsight クラスターのゲートウェイ設定を更新します |
> | Microsoft.HDInsight/clusters/configurations/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Resources/deployments/operations/read | デプロイ操作を取得または一覧表示します。 |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>HDInsight ドメイン サービス共同作成者

HDInsight Enterprise セキュリティ パッケージに必要なドメイン サービス関連の操作の読み取り、作成、変更、削除を行うことができます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.AAD/*/read |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Log Analytics 共同作成者

Log Analytics 共同作成者は、すべての監視データを読み取り、監視設定を編集できます。 監視設定の編集には、VM 拡張機能の VM への追加、Azure Storage からログの収集を設定できるようにするためのストレージ アカウント キーの読み取り、Automation アカウントの作成と構成、ソリューションの追加、すべての Azure リソースでの Azure Diagnostics の構成が含まれます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | ストレージ アカウントのアクセス キーを一覧表示します。 |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.HybridCompute/machines/extensions/write | Azure Arc 拡張機能をインストールまたは更新されます |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Insights/diagnosticSettings/* | 分析サーバーの診断の設定の作成、更新、または読み取りを行います |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Log Analytics 閲覧者

Log Analytics Reader は、すべての監視データの表示と検索、およびすべての Azure リソース上の Azure Diagnostics 構成の表示など、監視設定の表示を行うことができます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | 新しいエンジンを使用して検索します。 |
> | Microsoft.OperationalInsights/workspaces/search/action | 検索クエリを実行します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | ワークスペースの共有キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。 |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>ブロックチェーン


### <a name="blockchain-member-node-access-preview"></a>ブロックチェーン メンバー ノードへのアクセス (プレビュー)

ブロックチェーン メンバー ノードにアクセスできるようにします

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | 既存のブロックチェーン メンバーのトランザクション ノードを取得または一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | ブロックチェーン メンバーのトランザクション ノードに接続します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>AI + 機械学習


### <a name="cognitive-services-contributor"></a>Cognitive Services 共同作成者

Cognitive Services のキーの作成、読み取り、更新、削除、管理を行うことができます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | サブスクリプションの機能を取得します。 |
> | Microsoft.Features/providers/features/read | 指定されたリソース プロバイダーのサブスクリプションの機能を取得します。 |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Insights/diagnosticSettings/* | 分析サーバーの診断の設定の作成、更新、または読み取りを行います |
> | Microsoft.Insights/logDefinitions/read | ログ定義を読み取ります。 |
> | Microsoft.Insights/metricdefinitions/read | メトリック定義を読み取ります。 |
> | Microsoft.Insights/metrics/read | メトリックを読み取ります。 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/deployments/operations/read | デプロイ操作を取得または一覧表示します。 |
> | Microsoft.Resources/subscriptions/operationresults/read | サブスクリプション操作の結果を取得します。 |
> | Microsoft.Resources/subscriptions/read | サブスクリプションの一覧を取得します。 |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Cognitive Services データ閲覧者 (プレビュー)

Cognitive Services データを読み取ります。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Cognitive Services ユーザー

Cognitive Services のキーの読み取りおよび一覧表示を行うことができます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | キーを一覧表示します。 |
> | Microsoft.Insights/alertRules/read | クラシック メトリック アラートを読み取ります |
> | Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を読み取ります |
> | Microsoft.Insights/logDefinitions/read | ログ定義を読み取ります。 |
> | Microsoft.Insights/metricdefinitions/read | メトリック定義を読み取ります。 |
> | Microsoft.Insights/metrics/read | メトリックを読み取ります。 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/operations/read | デプロイ操作を取得または一覧表示します。 |
> | Microsoft.Resources/subscriptions/operationresults/read | サブスクリプション操作の結果を取得します。 |
> | Microsoft.Resources/subscriptions/read | サブスクリプションの一覧を取得します。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>複合現実


### <a name="spatial-anchors-account-contributor"></a>Spatial Anchors アカウント共同作成者

アカウントで Spatial Anchors を管理します (削除は含まない)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | 空間アンカーを作成します。 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | 近くにある空間アンカーを検出します。 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | 空間アンカーのプロパティを取得します。 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | 空間アンカーを探します。 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Azure Spatial Anchors サービスの品質を改善するために診断データを送信します。 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | 空間アンカーのプロパティを更新します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>Spatial Anchors アカウント所有者

アカウントで Spatial Anchors を管理します (削除も含む)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | 空間アンカーを作成します。 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | 空間アンカーを削除します。 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | 近くにある空間アンカーを検出します。 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | 空間アンカーのプロパティを取得します。 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | 空間アンカーを探します。 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Azure Spatial Anchors サービスの品質を改善するために診断データを送信します。 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | 空間アンカーのプロパティを更新します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>Spatial Anchors アカウント閲覧者

アカウントで Spatial Anchors のプロパティを検索して読み取ります

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | 近くにある空間アンカーを検出します。 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | 空間アンカーのプロパティを取得します。 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | 空間アンカーを探します。 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Azure Spatial Anchors サービスの品質を改善するために診断データを送信します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>統合


### <a name="api-management-service-contributor"></a>API Management Service Contributor

サービスと API を管理できます

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.ApiManagement/service/* | API Management サービスの作成と管理 |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>API Management Service Operator Role

サービスを管理できますが、API は対象外です

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.ApiManagement/service/*/read | API Management サービス インスタンスの読み取り |
> | Microsoft.ApiManagement/service/backup/action | ユーザーが指定したストレージ アカウント内の指定されたコンテナーに API Management サービスをバックアップします。 |
> | Microsoft.ApiManagement/service/delete | API Management サービス インスタンスを削除します。 |
> | Microsoft.ApiManagement/service/managedeployments/action | SKU/ユニット数を変更し、API Management サービスのリージョン デプロイを追加または削除します。 |
> | Microsoft.ApiManagement/service/read | API Management サービス インスタンスのメタデータの読み取り |
> | Microsoft.ApiManagement/service/restore/action | ユーザーが指定したストレージ アカウント内の指定されたコンテナーからの API Management サービスの復元 |
> | Microsoft.ApiManagement/service/updatecertificate/action | API Management サービスの TLS/SSL 証明書をアップロードします。 |
> | Microsoft.ApiManagement/service/updatehostname/action | API Management サービスのカスタム ドメイン名を設定、更新、または削除します。 |
> | Microsoft.ApiManagement/service/write | API Management サービス インスタンスの作成または更新 |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | ユーザーに関連付けられたキーを取得します |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>API Management Service Reader Role

サービスと API への読み取り専用アクセスです

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.ApiManagement/service/*/read | API Management サービス インスタンスの読み取り |
> | Microsoft.ApiManagement/service/read | API Management サービス インスタンスのメタデータの読み取り |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | ユーザーに関連付けられたキーを取得します |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-owner"></a>App Configuration データ所有者

App Configuration データへのフル アクセスを許可します。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.AppConfiguration/configurationStores/*/read |  |
> | Microsoft.AppConfiguration/configurationStores/*/write |  |
> | Microsoft.AppConfiguration/configurationStores/*/delete |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>App Configuration データ閲覧者

App Configuration データへの読み取りアクセスを許可します。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.AppConfiguration/configurationStores/*/read |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Azure Service Bus データ所有者

Azure Service Bus リソースへのフル アクセスを許可します。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Azure Service Bus データ受信者

Azure Service Bus リソースへの受信アクセスを許可します。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/*/receive/action |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Azure Service Bus データ送信者

Azure Service Bus リソースへの送信アクセスを許可します。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/*/send/action |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Azure Stack Registration Owner

Azure Stack の登録を管理できます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.AzureStack/registrations/products/*/action |  |
> | Microsoft.AzureStack/registrations/products/read | Azure Stack Marketplace の製品のプロパティを取得します |
> | Microsoft.AzureStack/registrations/read | Azure Stack の登録のプロパティを取得します |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription 共同作成者

EventGrid のイベント サブスクリプション操作を管理できます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | グローバル イベント サブスクリプションをトピックの種類ごとに一覧表示します |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | リージョンのイベント サブスクリプションを一覧表示します |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | リージョンのイベント サブスクリプションを topictype ごとに一覧表示します |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription 閲覧者

EventGrid のイベント サブスクリプションを読み取ることができます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.EventGrid/eventSubscriptions/read | eventSubscription を削除します。 |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | グローバル イベント サブスクリプションをトピックの種類ごとに一覧表示します |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | リージョンのイベント サブスクリプションを一覧表示します |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | リージョンのイベント サブスクリプションを topictype ごとに一覧表示します |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Intelligent Systems Account Contributor

Intelligent Systems のアカウントを管理できます。ただし、それらへのアクセスは含まれません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.IntelligentSystems/accounts/* | Intelligent Systems アカウントの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>Logic App Contributor

ロジック アプリを管理できますが、アクセス権を変更することはできません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | ストレージ アカウントのアクセス キーを一覧表示します。 |
> | Microsoft.ClassicStorage/storageAccounts/read | 特定のアカウントのストレージ アカウントを返します。 |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Insights/metricAlerts/* |  |
> | Microsoft.Insights/diagnosticSettings/* | 分析サーバーの診断の設定の作成、更新、または読み取りを行います |
> | Microsoft.Insights/logdefinitions/* | このアクセス許可は、ポータルを使用してアクティビティ ログにアクセスする必要があるユーザーに必要です。 アクティビティ ログのログのカテゴリを一覧表示します。 |
> | Microsoft.Insights/metricDefinitions/* | メトリック定義 (リソースの使用可能なメトリックの種類の一覧) を読み取ります。 |
> | Microsoft.Logic/* | Logic Apps リソースを管理します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/operationresults/read | サブスクリプション操作の結果を取得します。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Storage/storageAccounts/listkeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
> | Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | Microsoft.Web/connectionGateways/* | 接続ゲートウェイを作成および管理します。 |
> | Microsoft.Web/connections/* | 接続を作成および管理します。 |
> | Microsoft.Web/customApis/* | カスタム API を作成および管理します。 |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | App Service プランのプロパティを取得します。 |
> | Microsoft.Web/sites/functions/listSecrets/action | 関数のシークレットを一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>Logic App Operator

ロジック アプリの読み取り、有効化、無効化ができますが、編集または更新はできません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/*/read | Insights のアラート ルールを読み取ります |
> | Microsoft.Insights/metricAlerts/*/read |  |
> | Microsoft.Insights/diagnosticSettings/*/read | Logic Apps の診断設定を取得します |
> | Microsoft.Insights/metricDefinitions/*/read | Logic Apps の利用可能なメトリックを取得します。 |
> | Microsoft.Logic/*/read | Logic Apps リソースを読み取ります。 |
> | Microsoft.Logic/workflows/disable/action | ワークフローを無効にします。 |
> | Microsoft.Logic/workflows/enable/action | ワークフローを有効にします。 |
> | Microsoft.Logic/workflows/validate/action | ワークフローを検証します。 |
> | Microsoft.Resources/deployments/operations/read | デプロイ操作を取得または一覧表示します。 |
> | Microsoft.Resources/subscriptions/operationresults/read | サブスクリプション操作の結果を取得します。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | Microsoft.Web/connectionGateways/*/read | 接続ゲートウェイを読み取ります。 |
> | Microsoft.Web/connections/*/read | 接続を読み取ります。 |
> | Microsoft.Web/customApis/*/read | カスタム API を読み取ります。 |
> | Microsoft.Web/serverFarms/read | App Service プランのプロパティを取得します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>ID


### <a name="managed-identity-contributor"></a>Managed Identity Contributor

ユーザー割り当て ID の作成、読み取り、更新、削除を行います

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/read | 既存のユーザー割り当て ID を取得します。 |
> | Microsoft.ManagedIdentity/userAssignedIdentities/write | 新しいユーザー割り当て ID を作成するか、既存のユーザー割り当て ID に関連付けられているタグを更新します。 |
> | Microsoft.ManagedIdentity/userAssignedIdentities/delete | 既存のユーザー割り当て ID を削除します。 |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>Managed Identity Operator

ユーザー割り当て ID の読み取りと割り当てを行います

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>Security


### <a name="azure-sentinel-contributor"></a>Azure Sentinel 共同作成者

Azure Sentinel 共同作成者

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.SecurityInsights/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | 新しいエンジンを使用して検索します。 |
> | Microsoft.OperationalInsights/workspaces/*/read | Log Analytics のデータの表示 |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* |  |
> | Microsoft.OperationsManagement/solutions/read | 既存の OMS ソリューションを取得します。 |
> | Microsoft.OperationalInsights/workspaces/query/read | ワークスペース内のデータに対してクエリを実行します |
> | Microsoft.OperationalInsights/workspaces/query/*/read |  |
> | Microsoft.OperationalInsights/workspaces/dataSources/read | ワークスペースのデータソースを取得します。 |
> | Microsoft.Insights/workbooks/* |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Azure Sentinel 閲覧者

Azure Sentinel 閲覧者

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.SecurityInsights/*/read |  |
> | Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action | ユーザーの承認とライセンスを確認します |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | 新しいエンジンを使用して検索します。 |
> | Microsoft.OperationalInsights/workspaces/*/read | Log Analytics のデータの表示 |
> | Microsoft.OperationalInsights/workspaces/LinkedServices/read | 指定されたワークスペースのリンクされたサービスを取得します。 |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | 保存された検索クエリを取得します。 |
> | Microsoft.OperationsManagement/solutions/read | 既存の OMS ソリューションを取得します。 |
> | Microsoft.OperationalInsights/workspaces/query/read | ワークスペース内のデータに対してクエリを実行します |
> | Microsoft.OperationalInsights/workspaces/query/*/read |  |
> | Microsoft.OperationalInsights/workspaces/dataSources/read | ワークスペースのデータソースを取得します。 |
> | Microsoft.Insights/workbooks/read | ブックを読み取ります |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Azure Sentinel レスポンダー

Azure Sentinel レスポンダー

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.SecurityInsights/*/read |  |
> | Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action | ユーザーの承認とライセンスを確認します |
> | Microsoft.SecurityInsights/cases/* |  |
> | Microsoft.SecurityInsights/incidents/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | 新しいエンジンを使用して検索します。 |
> | Microsoft.OperationalInsights/workspaces/*/read | Log Analytics のデータの表示 |
> | Microsoft.OperationalInsights/workspaces/dataSources/read | ワークスペースのデータソースを取得します。 |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | 保存された検索クエリを取得します。 |
> | Microsoft.OperationsManagement/solutions/read | 既存の OMS ソリューションを取得します。 |
> | Microsoft.OperationalInsights/workspaces/query/read | ワークスペース内のデータに対してクエリを実行します |
> | Microsoft.OperationalInsights/workspaces/query/*/read |  |
> | Microsoft.OperationalInsights/workspaces/dataSources/read | ワークスペースのデータソースを取得します。 |
> | Microsoft.Insights/workbooks/read | ブックを読み取ります |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Key Vault Contributor

キー コンテナーを管理できますが、アクセスすることはできません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | 論理的に削除された Key Vault を消去します。 |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>セキュリティ管理者

Security Center の表示および更新のアクセス許可。 セキュリティ閲覧者と同じアクセス許可があり、セキュリティ ポリシーの更新、アラートと推奨事項の無視も可能になります。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Authorization/policyAssignments/* | ポリシーの割り当ての作成と管理 |
> | Microsoft.Authorization/policyDefinitions/* | ポリシー定義の作成と管理 |
> | Microsoft.Authorization/policySetDefinitions/* | ポリシー セットの作成と管理 |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Management/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |
> | Microsoft.operationalInsights/workspaces/*/read | Log Analytics のデータの表示 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Security/* | セキュリティ コンポーネントおよびポリシーの作成と管理 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>Security Assessment Contributor

評価を Security Center にプッシュできます

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Security/assessments/write | サブスクリプションで利用可能なセキュリティ評価を作成または更新します |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>セキュリティ マネージャー (レガシ)

これは、レガシ ロールです。 代わりに Security Admin を使用してください。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.ClassicCompute/*/read | 従来の仮想マシンの構成情報の読み取り |
> | Microsoft.ClassicCompute/virtualMachines/*/write | 従来の仮想マシンの構成の書き込み |
> | Microsoft.ClassicNetwork/*/read | 従来のネットワークに関する構成情報の読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Security/* | セキュリティ コンポーネントおよびポリシーの作成と管理 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>セキュリティ閲覧者

Security Center の表示アクセス許可。 推奨事項、警告、セキュリティ ポリシー、セキュリティの状態を閲覧できますが、変更することはできません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.operationalInsights/workspaces/*/read | Log Analytics のデータの表示 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Security/*/read | セキュリティ コンポーネントとポリシーの読み取り |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | Microsoft.Management/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>DevTest Labs User

Azure DevTest Labs で仮想マシンの接続、起動、再起動、シャットダウンができます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Compute/availabilitySets/read | 可用性セットのプロパティを取得します。 |
> | Microsoft.Compute/virtualMachines/*/read | 仮想マシンのプロパティ (VM サイズ、実行時の状態、VM 拡張機能など) の読み取り |
> | Microsoft.Compute/virtualMachines/deallocate/action | 仮想マシンを電源オフにし、コンピューティング リソースを解放します。 |
> | Microsoft.Compute/virtualMachines/read | 仮想マシンのプロパティを取得します。 |
> | Microsoft.Compute/virtualMachines/restart/action | 仮想マシンを再起動します。 |
> | Microsoft.Compute/virtualMachines/start/action | 仮想マシンを起動します。 |
> | Microsoft.DevTestLab/*/read | ラボのプロパティの読み取り |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | ラボ内のランダムに要求可能な仮想マシンを要求します。 |
> | Microsoft.DevTestLab/labs/createEnvironment/action | ラボで仮想マシンを作成します。 |
> | Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action | 現在のユーザーがラボで有効なプロファイルを保有していることを確認します。 |
> | Microsoft.DevTestLab/labs/formulas/delete | 数式を削除します。 |
> | Microsoft.DevTestLab/labs/formulas/read | 数式を読み取ります。 |
> | Microsoft.DevTestLab/labs/formulas/write | 数式を追加または変更します。 |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | ラボ ポリシーを評価します。 |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | 既存の仮想マシンの所有権を取得します。 |
> | Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action | 存在する場合は、該当する開始/停止のスケジュールを一覧表示します。 |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | 仮想マシンの RDP ファイルの内容を表す文字列を取得します。 |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | ロード バランサーのバックエンド アドレス プールを接続します。 警告不可能です。 |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | ロード バランサーのインバウンド NAT 規則を接続します。 警告不可能です。 |
> | Microsoft.Network/networkInterfaces/*/read | ネットワーク インターフェイスのプロパティ (例: そのネットワーク インターフェイスが含まれているすべてのロード バランサー) の読み取り |
> | Microsoft.Network/networkInterfaces/join/action | 仮想マシンをネットワーク インターフェイスに接続します。 警告不可能です。 |
> | Microsoft.Network/networkInterfaces/read | ネットワーク インターフェイスの定義を取得します。  |
> | Microsoft.Network/networkInterfaces/write | ネットワーク インターフェイスを作成するか、既存のネットワーク インターフェイスを更新します。  |
> | Microsoft.Network/publicIPAddresses/*/read | パブリック IP アドレスのプロパティの読み取り |
> | Microsoft.Network/publicIPAddresses/join/action | パブリック IP アドレスに接続します。 警告不可能です。 |
> | Microsoft.Network/publicIPAddresses/read | パブリック IP アドレス定義を取得します。 |
> | Microsoft.Network/virtualNetworks/subnets/join/action | 仮想ネットワークに参加します。 警告不可能です。 |
> | Microsoft.Resources/deployments/operations/read | デプロイ操作を取得または一覧表示します。 |
> | Microsoft.Resources/deployments/read | デプロイを取得または一覧表示します。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Storage/storageAccounts/listKeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | 仮想マシンを更新する際に使用できるサイズを一覧表示します。 |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>Lab Creator

Azure Lab アカウントで管理対象のラボを作成、管理、削除できます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | ラボ アカウントにラボを作成します。 |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | ラボ アカウントの下で構成されたサイズ カテゴリ別のリージョン別の提供状況を取得します。 |
> | Microsoft.LabServices/labAccounts/getPricingAndAvailability/action | ラボ アカウントのサイズ、地域、およびオペレーティング システムの組み合わせの価格と可用性を取得します。 |
> | Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action | このサブスクリプションのコアに関する制限と使用状況を取得します |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, manage, delete your managed labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action",
        "Microsoft.LabServices/labAccounts/getRegionalAvailability/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="monitor"></a>モニター


### <a name="application-insights-component-contributor"></a>Application Insights Component Contributor

Application Insights コンポーネントを管理できます

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | 従来のアラート ルールの作成と管理 |
> | Microsoft.Insights/metricAlerts/* | 新しいアラート ルールの作成と管理 |
> | Microsoft.Insights/components/* | Insights コンポーネントの作成と管理 |
> | Microsoft.Insights/webtests/* | Insights Web テストの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger

Application Insights スナップショット デバッガーで収集されたデバック スナップショットの表示とダウンロードを実行できるアクセス許可をユーザーに与えます。 これらのアクセス許可は、[所有者](#owner)ロールまたは[共同作成者](#contributor)ロールには含まれないことに注意してください。 ユーザーに Application Insights スナップショット デバッガー ロールを与える場合は、そのロールをユーザーに直接付与する必要があります。 このロールは、カスタム ロールに追加されるときに認識されません。 

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>Monitoring Contributor

すべての監視データを読み取り、監視設定を編集できます。 [「Azure Monitor での役割、アクセス許可、およびセキュリティの概要」](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles)も参照してください。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Insights/components/* | Insights コンポーネントの作成と管理 |
> | Microsoft.Insights/DiagnosticSettings/* | 分析サーバーの診断の設定の作成、更新、または読み取りを行います |
> | Microsoft.Insights/eventtypes/* | サブスクリプションのアクティビティ ログのイベント (管理イベント) を一覧表示します。 このアクセス許可は、アクティビティ ログへのプログラムによるアクセスとポータル アクセスの両方に適用されます。 |
> | Microsoft.Insights/LogDefinitions/* | このアクセス許可は、ポータルを使用してアクティビティ ログにアクセスする必要があるユーザーに必要です。 アクティビティ ログのログのカテゴリを一覧表示します。 |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | メトリック定義 (リソースの使用可能なメトリックの種類の一覧) を読み取ります。 |
> | Microsoft.Insights/Metrics/* | リソースのメトリックを読み取ります。 |
> | Microsoft.Insights/Register/Action | Microsoft Insights プロバイダーを登録します。 |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Insights Web テストの作成と管理 |
> | Microsoft.Insights/workbooks/* |  |
> | Microsoft.Insights/privateLinkScopes/* |  |
> | Microsoft.Insights/privateLinkScopeOperationStatuses/* |  |
> | Microsoft.OperationalInsights/workspaces/write | 新しいワークスペースを作成するか、既存のワークスペースの顧客 ID を指定して既存のワークスペースにリンクします。 |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Log Analytics ソリューション パックの読み取り/書き込み/削除を行います。 |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Log Analytics によって保存された検索の読み取り/書き込み/削除を行います。 |
> | Microsoft.OperationalInsights/workspaces/search/action | 検索クエリを実行します。 |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | ワークスペースの共有キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。 |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Log Analytics ストレージ インサイト構成の読み取り/書き込み/削除を行います。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | Microsoft.WorkloadMonitor/monitors/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | Microsoft.AlertsManagement/smartDetectorAlertRules/* |  |
> | Microsoft.AlertsManagement/actionRules/* |  |
> | Microsoft.AlertsManagement/smartGroups/* |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/privateLinkScopes/*",
        "Microsoft.Insights/privateLinkScopeOperationStatuses/*",
        "Microsoft.OperationalInsights/workspaces/write",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.WorkloadMonitor/notificationSettings/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>監視メトリック パブリッシャー

Azure リソースに対するメトリックの公開を有効にします

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Insights/Register/Action | Microsoft Insights プロバイダーを登録します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.Insights/Metrics/Write | メトリックを書き込みます。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>Monitoring Reader

すべての監視データ (メトリック、ログなど) を読み取ることができます。 [「Azure Monitor での役割、アクセス許可、およびセキュリティの概要」](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles)も参照してください。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | Microsoft.OperationalInsights/workspaces/search/action | 検索クエリを実行します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>Workbook Contributor

共有ブックを保存できます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Insights/workbooks/write | ブックを作成または更新します |
> | Microsoft.Insights/workbooks/delete | ブックを削除します |
> | Microsoft.Insights/workbooks/read | ブックを読み取ります |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>Workbook Reader

ブックの読み取りが可能です。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | microsoft.insights/workbooks/read | ブックを読み取ります |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>管理 + ガバナンス


### <a name="automation-job-operator"></a>Automation ジョブ オペレーター

Automation Runbook を使用してジョブを作成および管理します。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker リソースを読み取ります。 |
> | Microsoft.Automation/automationAccounts/jobs/read | Azure Automation ジョブを取得します。 |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Azure Automation ジョブを再開します。 |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Azure Automation ジョブを停止します。 |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Azure Automation ジョブ ストリームを取得します。 |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Azure Automation ジョブを中断します。 |
> | Microsoft.Automation/automationAccounts/jobs/write | Azure Automation ジョブを作成します。 |
> | Microsoft.Automation/automationAccounts/jobs/output/read | ジョブの出力を取得します。 |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>Automation Operator

Automation オペレーターはジョブを開始、停止、中断、再開することができます

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker リソースを読み取ります。 |
> | Microsoft.Automation/automationAccounts/jobs/read | Azure Automation ジョブを取得します。 |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Azure Automation ジョブを再開します。 |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Azure Automation ジョブを停止します。 |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Azure Automation ジョブ ストリームを取得します。 |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Azure Automation ジョブを中断します。 |
> | Microsoft.Automation/automationAccounts/jobs/write | Azure Automation ジョブを作成します。 |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Azure Automation ジョブ スケジュールを取得します。 |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Azure Automation ジョブ スケジュールを作成します。 |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Automation アカウントにリンクされているワークスペースを取得します |
> | Microsoft.Automation/automationAccounts/read | Azure Automation アカウントを取得します。 |
> | Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation Runbook を取得します。 |
> | Microsoft.Automation/automationAccounts/schedules/read | Azure Automation スケジュール資産を取得します。 |
> | Microsoft.Automation/automationAccounts/schedules/write | Azure Automation スケジュール資産を作成または更新します。 |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Automation/automationAccounts/jobs/output/read | ジョブの出力を取得します。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>Automation Runbook オペレーター

Runbook のジョブを作成する方法については、Runbook のプロパティを参照してください。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation Runbook を取得します。 |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Azure Connected Machine のオンボード

Azure Connected Machine をオンボードできます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.HybridCompute/machines/read | Azure Arc マシンを読み取ります |
> | Microsoft.HybridCompute/machines/write | Azure Arc マシンを書き込みます |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | ゲスト構成の割り当てを取得します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Azure Connected Machine のリソース管理者

Azure Connected Machine の読み取り、書き込み、削除、再オンボードを実行できます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.HybridCompute/machines/read | Azure Arc マシンを読み取ります |
> | Microsoft.HybridCompute/machines/write | Azure Arc マシンを書き込みます |
> | Microsoft.HybridCompute/machines/delete | Azure Arc マシンを削除します |
> | Microsoft.HybridCompute/machines/reconnect/action | Azure Arc マシンが再接続されます |
> | Microsoft.HybridCompute/machines/extensions/write | Azure Arc 拡張機能をインストールまたは更新されます |
> | Microsoft.HybridCompute/*/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/reconnect/action",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>Billing Reader

課金データへの読み取りアクセスを許可します

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Billing/*/read | 課金情報の読み取り |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>ブループリント共同作成者

ブループリントの定義を管理できますが、それらを割り当てることはできません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Blueprint/blueprints/* | ブループリントの定義またはブループリント アーティファクトを作成および管理します。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>ブループリント オペレーター

既存の発行済みのブループリントを割り当てることはできますが、ブループリントの新規作成はできません。 これは、ユーザーが割り当てたマネージド ID を使用して割り当てが行われた場合にのみ機能することに注意してください。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Blueprint/blueprintAssignments/* | ブループリント割り当てを作成および管理します。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>Cost Management 共同作成者

コストを表示し、コストの構成 (予算、エクスポートなど) を管理することができます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Consumption/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Billing/billingPeriods/read |  |
> | Microsoft.Resources/subscriptions/read | サブスクリプションの一覧を取得します。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | Microsoft.Advisor/configurations/read | 構成の取得 |
> | Microsoft.Advisor/recommendations/read | 推奨事項を読み取ります。 |
> | Microsoft.Management/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>Cost Management 閲覧者

コストのデータと構成 (予算、エクスポートなど) を表示することができます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Billing/billingPeriods/read |  |
> | Microsoft.Resources/subscriptions/read | サブスクリプションの一覧を取得します。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | Microsoft.Advisor/configurations/read | 構成の取得 |
> | Microsoft.Advisor/recommendations/read | 推奨事項を読み取ります。 |
> | Microsoft.Management/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hierarchy-settings-administrator"></a>Hierarchy Settings Administrator

ユーザーに、階層設定の編集と削除を許可します

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Management/managementGroups/settings/write | 管理グループ階層の設定が作成または更新されます。 |
> | Microsoft.Management/managementGroups/settings/delete | 管理グループ階層の設定が削除されます。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows users to edit and delete Hierarchy Settings",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/350f8d15-c687-4448-8ae1-157740a3936d",
  "name": "350f8d15-c687-4448-8ae1-157740a3936d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/settings/write",
        "Microsoft.Management/managementGroups/settings/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Hierarchy Settings Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Managed Application Contributor Role

マネージド アプリケーション リソースの作成を許可します。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | Microsoft.Solutions/applications/* |  |
> | Microsoft.Solutions/register/action | ソリューションに登録します。 |
> | Microsoft.Resources/subscriptions/resourceGroups/* |  |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>Managed Application Operator Role

マネージド アプリケーション リソースに対する読み取りとアクションの実行が可能です。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | Microsoft.Solutions/applications/read | アプリケーションの一覧を取得します。 |
> | Microsoft.Solutions/*/action |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>Managed Applications 閲覧者

マネージド アプリおよび要求 JIT アクセスでリソースを読み取ることができます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Solutions/jitRequests/* |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>マネージド サービスの登録割り当て削除ロール

マネージド サービスの登録割り当て削除ロールを使用すると、テナント管理ユーザーは、テナントに割り当てられている登録割り当てを削除できます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.ManagedServices/registrationAssignments/read | マネージド サービスの登録割り当ての一覧を取得します。 |
> | Microsoft.ManagedServices/registrationAssignments/delete | マネージド サービスの登録割り当てを削除します。 |
> | Microsoft.ManagedServices/operationStatuses/read | リソースの操作の状態を読み取ります。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>管理グループ共同作成者

管理グループ共同作成者ロール

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Management/managementGroups/delete | 管理グループを削除します。 |
> | Microsoft.Management/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |
> | Microsoft.Management/managementGroups/subscriptions/delete | 管理グループからサブスクリプションの関連付けを解除します。 |
> | Microsoft.Management/managementGroups/subscriptions/write | 既存のサブスクリプションと管理グループを関連付けます。 |
> | Microsoft.Management/managementGroups/write | 管理グループを作成または更新します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>管理グループ閲覧者

管理グループ閲覧者ロール

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Management/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>New Relic APM Account Contributor

New Relic Application Performance Management のアカウントとアプリケーションを管理できます。ただし、それらへのアクセスは含まれません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>Policy Insights データ ライター (プレビュー)

リソース ポリシーに対する読み取りアクセスとリソース コンポーネント ポリシー イベントへの書き込みアクセスを許可します。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/policyassignments/read | ポリシー割り当てに関する情報を取得します。 |
> | Microsoft.Authorization/policydefinitions/read | ポリシー定義に関する情報を取得します。 |
> | Microsoft.Authorization/policysetdefinitions/read | ポリシー セットの定義に関する情報を取得します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.PolicyInsights/checkDataPolicyCompliance/action | 指定されたコンポーネントのコンプライアンス状態をデータ ポリシーと照合します。 |
> | Microsoft.PolicyInsights/policyEvents/logDataEvents/action | リソース コンポーネントのポリシー イベントをログに記録します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>リソース ポリシー共同作成者

リソース ポリシーの作成または変更、サポート チケットの作成、リソースまたは階層の読み取りを行う権限を持つユーザー。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | Microsoft.Authorization/policyassignments/* | ポリシーの割り当ての作成と管理 |
> | Microsoft.Authorization/policydefinitions/* | ポリシー定義の作成と管理 |
> | Microsoft.Authorization/policysetdefinitions/* | ポリシー セットの作成と管理 |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Site Recovery Contributor

資格情報コンテナーの作成とロールの割り当てを除く、Site Recovery サービスを管理できます

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp は、サービスによって使用される内部操作です。 |
> | Microsoft.RecoveryServices/Vaults/certificates/write | "リソース証明書を更新" 操作では、リソース/コンテナー資格情報証明書を更新します。 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | コンテナーに関連する拡張情報の作成および管理 |
> | Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | 登録済み ID の管理 |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | レプリケーションの警告設定の作成または更新 |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | イベントを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | レプリケーション ファブリックの作成と管理 |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | レプリケーション ジョブの作成と管理 |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | レプリケーション ポリシーの作成と管理 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | 復旧計画の作成と管理 |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Recovery Services コンテナーのストレージ構成の作成と管理 |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | "コンテナー トークン" 操作を使用すると、コンテナー レベルのバックエンド操作のコンテナー トークンを取得できます。 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Recovery Services コンテナーのアラートの読み取り |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | Microsoft.RecoveryServices/vaults/replicationOperationStatus/read | コンテナー レプリケーション操作の状態を読み取ります |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Site Recovery Operator

フェールオーバーとフェールバックを実行できますが、その他の Site Recovery 管理操作は実行しません

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp は、サービスによって使用される内部操作です。 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | "操作結果を取得" 操作を使用すると、非同期で送信された操作の状態と結果を取得できます。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | " コンテナーを取得" 操作を使用すると、リソースの登録済みコンテナーを取得できます。 |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | アラート設定を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | イベントを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | ファブリックの一貫性を確認します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | ファブリックを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | ゲートウェイを再関連付けします。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | ファブリックの証明書を更新します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | ネットワークを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | ネットワーク マッピングを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | 保護コンテナーを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 保護可能な項目を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | 復旧ポイントを適用します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | フェールオーバーのコミット。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | 計画されたフェールオーバー。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 保護された項目を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | レプリケーションの復旧ポイントを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | レプリケーションを修復します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | 保護された項目を再保護します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | 保護コンテナーを切り替えます。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | テスト フェールオーバー |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | テスト フェールオーバーのクリーンアップ。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | [フェールオーバー] |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | モビリティ サービスを更新します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 保護コンテナー マッピングを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services プロバイダーを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | プロバイダーを更新します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | ストレージの分類を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | ストレージ分類マッピングを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | vCenter を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | レプリケーション ジョブの作成と管理 |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | ポリシーを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | フェールオーバーのコミットの復旧計画。 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | 計画されたフェールオーバーの復旧計画。 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 復旧計画を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | 再保護の復旧計画。 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | テスト フェールオーバーの復旧計画。 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | テスト フェールオーバーのクリーンアップの復旧計画。 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | フェールオーバーの復旧計画。 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Recovery Services コンテナーのアラートの読み取り |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | "コンテナー トークン" 操作を使用すると、コンテナー レベルのバックエンド操作のコンテナー トークンを取得できます。 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Site Recovery Reader

Site Recovery の状態を表示できますが、その他の管理操作は実行できません

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services コンテナーのアラートを取得します。 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | "操作結果を取得" 操作を使用すると、非同期で送信された操作の状態と結果を取得できます。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | " コンテナーを取得" 操作を使用すると、リソースの登録済みコンテナーを取得できます。 |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | アラート設定を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | イベントを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | ファブリックを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | ネットワークを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | ネットワーク マッピングを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | 保護コンテナーを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 保護可能な項目を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 保護された項目を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | レプリケーションの復旧ポイントを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 保護コンテナー マッピングを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services プロバイダーを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | ストレージの分類を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | ストレージ分類マッピングを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | vCenter を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | ジョブを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | ポリシーを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 復旧計画を読み取ります |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | "コンテナー トークン" 操作を使用すると、コンテナー レベルのバックエンド操作のコンテナー トークンを取得できます。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>Support Request Contributor

Support request を作成して管理できます

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="tag-contributor"></a>タグ共同作成者

エンティティ自体へのアクセスを提供することなく、エンティティのタグを管理できます。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Resources/subscriptions/resourceGroups/resources/read | リソース グループのリソースを取得します。 |
> | Microsoft.Resources/subscriptions/resources/read | サブスクリプションのリソースを取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | Microsoft.Resources/tags/* |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>その他


### <a name="biztalk-contributor"></a>BizTalk Contributor

BizTalk Services を管理できます。ただし、それらへのアクセスは含まれません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.BizTalkServices/BizTalk/* | BizTalk Services の作成と管理 |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Scheduler Job Collections Contributor

スケジューラ ジョブ コレクションを管理できます。ただし、それらへのアクセスは含まれません。

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Scheduler/jobcollections/* | ジョブ コレクションの作成と管理 |
> | Microsoft.Support/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>次のステップ

- [リソース プロバイダーとサービスの対応](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Azure カスタム ロール](custom-roles.md)
- [Azure Security Center におけるアクセス許可](../security-center/security-center-permissions.md)
