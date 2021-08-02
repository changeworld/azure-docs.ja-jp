---
title: 'オンプレミスの MySQL から Azure Database for MySQL への移行ガイド: 付録'
description: この移行ガイド用に作成した追加のドキュメントをダウンロードし、構成方法を学習します。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/11/2021
ms.openlocfilehash: feb02ded8e597f38c941b96eb6027061180a0177
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112083002"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-appendix"></a>オンプレミスの MySQL から Azure Database for MySQL への移行ガイド: 付録

## <a name="prerequisites"></a>前提条件

[まとめ](14-summary.md)

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