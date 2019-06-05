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
ms.openlocfilehash: 1d7ab8008e8fbdb5f851f158d14f62bdea803f11
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001696"
---
# <a name="security-attributes-for-azure-app-service"></a>Azure App Service のセキュリティ属性

この記事では、Azure App Service に組み込まれている一般的なセキュリティ属性について説明します。

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、ユーザーが管理するキーによるサーバー側暗号化、およびその他の暗号化機能) | はい | Web サイト ファイルのコンテンツは、Azure Storage に格納されます。これにより、コンテンツは保存時に自動的に暗号化されます。 「[保存データに対する Azure Storage 暗号化](../storage/common/storage-service-encryption.md)」を参照してください。<br><br>ユーザーが提供するシークレットは保存時に暗号化されます。 シークレットは、App Service 構成データベースに格納されている間、保存時に暗号化されます。<br><br>ローカルに接続されたディスクは、必要に応じて、Web サイトによって一時ストレージとして使用できます (D:\local および %TMP%)。 ローカルに接続されたディスクでは、保存時の暗号化は行われません。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | ユーザーは、インバウンド トラフィックで HTTPS を必要とし、使用するように Web サイトを構成できます。 ブログ記事の「[How to make an Azure App Service HTTPS only (Azure App Service を HTTPS のみにする方法)](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/)」を参照してください。 |
| 暗号化キーの処理 (CMK や BYOK など)| はい | ユーザーは、アプリケーション シークレットを Key Vault に格納し、それらを実行時に取得することを選択できます。 「[App Service と Azure Functions の Key Vault 参照を使用する (プレビュー)](app-service-key-vault-references.md)」を参照してください。|
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | App Service を構成するための管理呼び出しは、HTTPS 経由の [Azure Resource Manager](../azure-resource-manager/index.yml) 呼び出しで行われます。 |

## <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい | 現在、App Service のプレビューで利用可能です。 「[Azure App Service のアクセス制限](app-service-ip-restrictions.md)」を参照してください。 |
| VNet インジェクションのサポート| はい | App Service 環境は App Service のプライベート実装であり、ユーザーの仮想ネットワークに挿入される単一のユーザー専用です。 「[App Service 環境の概要](environment/intro.md)」を参照してください。 |
| ネットワークの分離とファイアウォールのサポート| はい | App Service のパブリック マルチテナント バリエーションでは、ユーザーは、許可されたインバウンド トラフィックをロック ダウンするようにネットワーク ACL (IP 制限) を構成できます。  「[Azure App Service のアクセス制限](app-service-ip-restrictions.md)」を参照してください。  App Service 環境は仮想ネットワークに直接デプロイされるため、NSG で保護することができます。 |
| 強制トンネリングのサポート| はい | ユーザーの強制トンネルが構成された仮想ネットワークに App Service 環境をデプロイできます。 ユーザーは、「[強制トンネリングを使用した App Service 環境の構成](environment/forced-tunnel-support.md)」の指示に従う必要があります。 |

## <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | App Service は、Application Insights をサポートする言語用の Application Insights と統合されます (完全な .NET Framework、.NET Core、Java および Node.JS)。  「[Azure App Service のパフォーマンスの監視](../azure-monitor/app/azure-web-apps.md)」を参照してください。 App Service では、アプリケーション メトリックを Azure Monitor に送信することもできます。 「[Azure App Service のアプリの監視](web-sites-monitor.md)」を参照してください。 |

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 認証| はい | ユーザーは、[Azure Active Directory (Azure AD)](../active-directory/index.md) および他の OAuth と互換性のある ID プロバイダーと自動的に統合されるアプリケーションを App Service 上でビルドできます (「[Azure App Service での認証および認可](overview-authentication-authorization.md)」を参照してください)。 App Service 資産へのアクセス管理では、Azure AD で認証されたプリンシパルと Azure Resource Manager の RBAC ロールの組み合わせによって、すべてのアクセスが制御されます。 |
| 承認| はい | App Service 資産へのアクセス管理では、Azure AD で認証されたプリンシパルと Azure Resource Manager の RBAC ロールの組み合わせによって、すべてのアクセスが制御されます。  |


## <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | App Service オブジェクトで実行されるすべての管理操作は、[Azure Resource Manager](../azure-resource-manager/index.yml) 経由で実行されます。 これらの操作の履歴ログはポータルと CLI の両方で入手できます (「[Azure Resource Manager のリソース プロバイダー操作](../role-based-access-control/resource-provider-operations.md#microsoftweb)」と「[az monitor activity-log](/cli/azure/monitor/activity-log)」を参照してください)。 |
| データ プレーンのログ記録と監査 | いいえ | App Service のデータ プレーンは、ユーザーのデプロイされたWeb サイトのコンテンツを含む、リモート ファイル共有です。  リモート ファイル共有の監査はありません。 |

## <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | 管理操作では、App Service 構成の状態を Azure Resource Manager テンプレートとしてエクスポートし、経時的にバージョン管理できます。 ランタイム操作では、ユーザーは App Service のデプロイ スロット機能を使用して、複数の異なるライブ バージョンのアプリケーションを維持できます。 | 

