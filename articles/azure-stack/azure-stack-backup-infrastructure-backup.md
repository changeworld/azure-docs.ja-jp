---
title: インフラストラクチャ バックアップ サービスを使用した Azure Stack のバックアップとデータの回復 | Microsoft Docs
description: インフラストラクチャ バックアップ サービスを使用して、構成およびサービス データをバックアップおよび復元できます。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
mss.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 4cb8ffe218ef1cd64b93201eddbbd09bb16026db
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50087391"
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>インフラストラクチャ バックアップ サービスを使用した Azure Stack のバックアップとデータの回復

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

インフラストラクチャ バックアップ サービスを使用して、構成およびサービス データをバックアップおよび復元できます。 Azure Stack の各インストールには、サービスのインスタンスが含まれています。 Azure Stack Cloud を再デプロイし、ID、セキュリティ、Azure Resource Manager データを復元するには、サービスによって作成されたバックアップを使用できます。

クラウドを実稼働に移行する準備ができたら、バックアップを有効にすることができます。 テストと検証を長期間実行する予定の場合は、バックアップを有効にしないでください。

バックアップ サービスを有効にする前に、[要件が満たされている](#verify-requirements-for-the-infrastructure-backup-service)ことを確認してください。

> [!Note]  
> インフラストラクチャ バックアップ サービスには、ユーザー データとアプリケーションは含まれません。 <!-- See the following articles for instructions on backing up and restore [App Services](https://aka.ms/azure-stack-app-service), [SQL](https://aka.ms/azure-stack-ms-sql), and [MySQL](https://aka.ms/azure-stack-mysql) resource providers and associated user data. -->

## <a name="the-infrastructure-backup-service"></a>インフラストラクチャ バックアップ サービス

このサービスには以下の機能が含まれています。

| 機能                                            | 説明                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| バックアップ インフラストラクチャ サービス                     | Azure Stack でインフラストラクチャ サービスのサブセット全体のバックアップを調整します。 災害が発生した場合、再デプロイの一環としてデータを復元できます。 |
| エクスポートされるバックアップ データの圧縮と暗号化 | バックアップ データは、システムによって圧縮および暗号化されてから、管理者が提供する外部ストレージの場所にエクスポートされます。                |
| バックアップ ジョブの監視                              | システムから、バックアップ ジョブが失敗した場合と修復の手順が通知されます。                                                                                                |
| 管理エクスペリエンスのバックアップ                       | バックアップ RP はバックアップの有効化をサポートしています。                                                                                                                         |
| クラウドの復旧                                     | 致命的なデータ損失が発生した場合、バックアップを使用して、デプロイの一環として Azure Stack のコア情報を復元できます。                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>インフラストラクチャ バックアップ サービスの要件を確認する

- **保存場所**  
  7 個のバックアップを保存できる、Azure Stack からアクセス可能なファイル共有が必要です。 各バックアップは約 10 GB です。 共有には 140 GB のバックアップを保存できる必要があります。 Azure Stack インフラストラクチャ バックアップ サービスの保存場所の選択の詳細については、「[Backup Controller requirements](azure-stack-backup-reference.md#backup-controller-requirements)」(バックアップ コントローラーの要件) を参照してください。
- **資格情報**  
  ドメイン ユーザー アカウントと資格情報が必要です。たとえば、Azure Stack 管理者の資格情報を使用できます。
- **暗号化キー**  
  バックアップ ファイルはこのキーを使用して暗号化されます。 このキーは安全な場所に保存してください。 初めて事前共有キーを設定する場合、または今後キーを交換する場合には、このインターフェイスでキーを表示することはできません。 事前共有キーの生成手順の詳細については、「[PowerShell で Azure Stack のバックアップを有効にする](azure-stack-backup-enable-backup-powershell.md)」のスクリプトを参照してください。

## <a name="next-steps"></a>次の手順

- [管理ポータルで Azure Stack のバックアップを有効にする](azure-stack-backup-enable-backup-console.md)方法を学びます。
- [PowerShell で Azure Stack のバックアップを有効にする](azure-stack-backup-enable-backup-powershell.md)方法を学びます。
- [Azure Stack のバックアップ](azure-stack-backup-back-up-azure-stack.md )方法を学びます。
- [致命的なデータの損失からの復旧](azure-stack-backup-recover-data.md)方法を学びます。
