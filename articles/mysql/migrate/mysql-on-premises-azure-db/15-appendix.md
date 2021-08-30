---
title: 'オンプレミスの MySQL を Azure Database for MySQL に移行する: サンプル アプリケーション'
description: この移行ガイド用に作成した追加のドキュメントをダウンロードし、構成方法を学習します。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 27f4ded8e1378ccceeb11409e9c393fbc46c92aa
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113084554"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-sample-applications"></a>オンプレミスの MySQL を Azure Database for MySQL に移行する: サンプル アプリケーション

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="overview"></a>概要

この記事では、エンドツーエンドの MySQL 移行ガイドを使用してサンプル アプリケーションをデプロイし、使用可能なサーバー パラメーターを確認する方法について説明します。

## <a name="environment-setup"></a>環境のセットアップ

この移行ガイド用に作成した[追加のドキュメントをダウンロード](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1%20Appendix%20A.pdf)し、サンプルの[会議デモ アプリケーション](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/sample-app)のガイドの移行手順を実行するための環境を構成する方法を確認します。

## <a name="azure-resource-manager-arm-templates"></a>Azure Resource Manager (ARM) テンプレート

### <a name="secure"></a>セキュリティで保護

ARM テンプレートを使用すると、プライベート エンドポイントを持つすべてのリソースがデプロイされます。 ARM テンプレートを使用すると、インターネットから PaaS サービスへのアクセスが実質的に削除されます。

[セキュリティで保護された ARM テンプレート](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/arm-templates/ExampleWithMigration)

### <a name="non-secure"></a>セキュリティ保護なし

ARM テンプレートでは、インターネットからすべてのリソースを利用できる標準デプロイを使用して、リソースがデプロイされます。

[セキュリティで保護されていない ARM テンプレート](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/arm-templates/ExampleWithMigrationSecure)

## <a name="default-server-parameters-mysql-55-and-azure-database-for-mysql"></a>既定のサーバー パラメーター MySQL 5.5 と Azure Database for MySQL

[MySQL 5.5 と Azure Database for MySQL の既定のサーバー パラメーターの完全な一覧](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1%20Appendix%20C.pdf)については、GitHub リポジトリを参照してください。