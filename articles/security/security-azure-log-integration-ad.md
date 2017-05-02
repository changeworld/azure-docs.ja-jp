---
title: "Active Directory 監査ログとの Azure ログ統合 (AZLOG) | Microsoft Docs"
description: "Azure ログ統合サービスをインストールし、Azure 監査ログを統合する方法について説明します"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 03/29/2017
ms.author: barclayn
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: f7c2f702a4ff2af8bb7487d49f5c6f9bf5199a49
ms.lasthandoff: 04/14/2017


---

#<a name="integrate-azure-active-directory-audit-logs"></a>Azure Active Directory と監査ログの統合

Azure Active Directory 監査イベントを利用すると、Azure Active Directory で発生した特権アクションを識別できます。 [Azure Active Directory 監査レポートのイベント](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md)を確認することで、追跡できるイベントのタイプを確認できます。

>[!NOTE]
この記事の手順を実行する前に、「[はじめに](security-azure-log-integration-get-started.md)」の記事の手順を正常に完了している必要があります。

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Azure Active Directory 監査ログを統合する手順

1. コマンド プロンプトを開き、cd により、現在のディレクトリを **C:\Program Files\Microsoft Azure Log Integration** に変更します。
2. 次のコマンドを実行します。

 ``azlog createazureid``
 
 このコマンドは Azure ログインを要求します。 このコマンドは、管理者、共同管理者、または所有者としてログインしている、Azure サブスクリプションをホストする Azure AD テナントに Azure Active Directory サービス プリンシパルを作成します。 単なる Guest ユーザーとして Azure AD テナントにログインしている場合、コマンドは失敗します。 Azure への認証は、Azure Active Directory (AD) によって行われます。 Azlog 統合のサービス プリンシパルを作成すると、Azure AD の ID が作成され、Azure サブスクリプションからの読み取りアクセス許可が付与されます。
 
3. tenantID を提供するコマンドを実行します。 コマンドを実行するには、テナント管理者ロールのメンバーである必要があります。

``Azlog.exe authorizedirectoryreader tenantId``

例

``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

次のフォルダーを調べて、Azure Active Directory 監査ログの JSON ファイルが作成されていることを確認します。

* **C:\Users\azlog\AzureActiveDirectoryJson**
* **C:\Users\azlog\AzureActiveDirectoryJsonLD**

標準的な SIEM ファイル フォワーダー コネクタで、SIEM インスタンスにデータをパイプ処理する適切なフォルダーをポイントします。 使用している SIEM 製品に基づいて、フィールド マッピングが必要になる可能性があります。

[Azure ログ統合に関する MSDN フォーラム](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration)を通して、コミュニティの支援を受けることができます。 このフォーラムでは AzLog コミュニティに、Azure ログ統合を最大限に活用する方法についての質問、回答、ヒント、およびコツによる相互サポート機能を提供しています。 さらに、Azure ログ統合チームがこのフォーラムを監視しており、可能なときにはいつでも支援を提供します。 

[サポート要求](../azure-supportability/how-to-create-azure-support-request.md)を出すこともできます。 これを行うには、サポートを依頼しようとしているサービスとして **[ログ統合]** を選択します。

## <a name="next-steps"></a>次のステップ
Azure ログの統合の詳細については、次のドキュメントを参照してください。

* [Azure ログ用の Microsoft Azure ログ統合](https://www.microsoft.com/download/details.aspx?id=53324) – Azure ログ統合の詳細情報、システム要件、およびインストール手順のダウンロード センター。
* [Azure ログ統合の概要](security-azure-log-integration-overview.md) – このドキュメントでは、Azure ログ統合と、その主な機能およびしくみについて紹介します。
* [パートナーの構成手順](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – このブログ投稿では、Splunk、HP ArcSight、IBM QRadar などのパートナー ソリューションを使用できるように、Azure ログ統合を構成する方法について説明します。
* [Azure ログ統合のよく寄せられる質問 (FAQ)](security-azure-log-integration-faq.md) – この FAQ は、Azure ログ統合について寄せられる質問とその回答です。
* [Azure ログ統合への Security Center の警告の統合](../security-center/security-center-integrating-alerts-with-log-integration.md) – このドキュメントでは、Azure Security Center の警告を、Azure 診断および Azure 監査ログによって収集された仮想マシンのセキュリティ イベントとともに、ログ分析または SIEM ソリューションと同期させる方法について説明します。
* [Azure 診断および Azure 監査ログの新機能](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – このブログ投稿では、Azure 監査ログと、Azure リソースの操作の洞察を得るのに役立つその他の機能を紹介します。

