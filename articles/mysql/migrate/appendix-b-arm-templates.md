---
title: 'オンプレミスの MySQL から Azure Database for MySQL への移行ガイドの付録 B: ARM テンプレート'
description: このテンプレートでは、プライベート エンドポイントを使用してすべてのリソースがデプロイされます。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/26/2021
ms.openlocfilehash: 07e689b8458650ca39a0aa949504dd762621bac1
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110656904"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-appendix-b-arm-templates"></a>オンプレミスの MySQL から Azure Database for MySQL への移行ガイドの付録 B: ARM テンプレート

### <a name="secure"></a>セキュリティで保護

このテンプレートでは、プライベート エンドポイントを使用してすべてのリソースがデプロイされます。 これにより、インターネットから PaaS サービスへのアクセスが実質的に削除されます。

[ARM テンプレート ](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/arm-templates/ExampleWithMigration)

### <a name="non-secure"></a>セキュリティ保護なし

このテンプレートでは、インターネットからすべてのリソースを利用できる標準デプロイを使用して、リソースがデプロイされます。

[ARM テンプレート ](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/arm-templates/ExampleWithMigrationSecure)