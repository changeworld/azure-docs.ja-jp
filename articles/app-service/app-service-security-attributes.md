---
title: Azure App Service のセキュリティ属性
description: Azure App Service を評価するためのセキュリティ属性のチェックリスト
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: e38e1479c1e094f691e8f22f0a48f9342cae5cf3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475083"
---
# <a name="security-attributes-for-azure-app-service"></a>Azure App Service のセキュリティ属性

この記事では、Azure App Service に組み込まれている一般的なセキュリティ属性について説明します。

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能) | はい | Web サイト ファイルのコンテンツは、Azure Storage に格納されます。これにより、コンテンツは保存時に自動的に暗号化されます。 「[保存データに対する Azure Storage 暗号化](../storage/common/storage-service-encryption.md)」を参照してください。<br><br>お客様が提供するシークレットは保存時に暗号化されます。 シークレットは、App Service 構成データベースに格納されている間、保存時に暗号化されます。<br><br>ローカルに接続されたディスクは、必要に応じて、Web サイトによって一時ストレージとして使用できます (D:\local および %TMP%)。 ローカルに接続されたディスクでは、保存時の暗号化は行われません。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | お客様は、インバウンド トラフィックで HTTPS を必要とし、使用するように Web サイトを構成することができます。 ブログ記事の「[How to make an Azure App Service HTTPS only (Azure App Service を HTTPS のみにする方法)](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/)」を参照してください。 |
| 暗号化キーの処理 (CMK や BYOK など)| はい | お客様はアプリケーション シークレットを Key Vault に格納し、それらを実行時に取得することを選択できます。 「[App Service と Azure Functions の Key Vault 参照を使用する (プレビュー)](app-service-key-vault-references.md)」を参照してください。|
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | App Service を構成するための管理呼び出しは、HTTPS 経由の [Azure Resource Manager](../azure-resource-manager/index.yml) 呼び出しで行われます。 |

## <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい | 現在、App Service のプレビューで利用可能です。 [Azure App Service のアクセス制限](app-service-ip-restrictions.md)に関するページを参照してください。 |
| VNet インジェクションのサポート| はい | App Service Environment は、お客様の仮想ネットワークに挿入される 1 人のお客様専用の App Service のプライベート実装です。 「[App Service 環境の概要](environment/intro.md)」を参照してください。 |
| ネットワークの分離とファイアウォールのサポート| はい | App Service のパブリック マルチテナント バリエーションでは、お客様は、許可されたインバウンド トラフィックをロック ダウンするようにネットワーク ACL (IP 制限) を構成することができます。  [Azure App Service のアクセス制限](app-service-ip-restrictions.md)に関するページを参照してください。  App Service Environment は仮想ネットワークに直接デプロイされるため、NSG で保護することができます。 |
| 強制トンネリングのサポート| はい | App Service Environment は、強制トンネルが構成された、お客様の仮想ネットワークにデプロイすることができます。 お客様は、「[強制トンネリングを使用した App Service Environment の構成](environment/forced-tunnel-support.md)」の指示に従う必要があります。 |

## <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | App Service は、Application Insights をサポートする言語用の Application Insights と統合されます (完全な .NET Framework、.NET Core、Java および Node.JS)。  「[Azure App Service のパフォーマンスの監視](../azure-monitor/app/azure-web-apps.md)」を参照してください。 App Service では、アプリケーション メトリックを Azure Monitor に送信することもできます。 「[Azure App Service のアプリの監視](web-sites-monitor.md)」を参照してください。 |

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | お客様は、[Azure Active Directory (Azure AD)](../active-directory/index.yml) および他の OAuth と互換性のある ID プロバイダーと自動的に統合される、App Service にアプリケーションをビルドすることができます (「[Azure App Service での認証および許可](overview-authentication-authorization.md)」を参照)。 App Service 資産へのアクセス管理では、Azure AD で認証されたプリンシパルと Azure Resource Manager の RBAC ロールの組み合わせによって、すべてのアクセスが制御されます。 |
| Authorization| はい | App Service 資産へのアクセス管理では、Azure AD で認証されたプリンシパルと Azure Resource Manager の RBAC ロールの組み合わせによって、すべてのアクセスが制御されます。  |


## <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | App Service オブジェクトで実行されるすべての管理操作は、[Azure Resource Manager](../azure-resource-manager/index.yml) 経由で実行されます。 これらの操作の履歴ログはポータルと CLI の両方を使用して入手できます (「[Azure Resource Manager リソース プロバイダー操作](../role-based-access-control/resource-provider-operations.md#microsoftweb)」と「[az monitor activity-log](/cli/azure/monitor/activity-log)」を参照)。 |
| データ プレーンのログ記録と監査 | いいえ | App Service のデータ プレーンは、ユーザーのデプロイされたWeb サイトのコンテンツを含む、リモート ファイル共有です。  リモート ファイル共有の監査はありません。 |

## <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | 管理操作では、App Service 構成の状態を Azure Resource Manager テンプレートとしてエクスポートし、経時的にバージョン管理できます。 ランタイム操作では、ユーザーは App Service のデプロイ スロット機能を使用して、複数の異なるライブ バージョンのアプリケーションを維持できます。 | 

