---
title: Azure Security Center の Advanced Data Security for IaaS | Microsoft Docs
description: " Azure Security Center で Advanced Data Security for IaaS を有効にする方法について説明します。 "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: memildin
ms.openlocfilehash: a2970ea3f5ad360deaedd7efc82154cd3bc50337
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617103"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Azure Virtual Machines の SQL Server 向け Advanced Data Security (プレビュー)
Azure Virtual Machines の SQL サーバー向け Advanced Data Security は、高度な SQL セキュリティ機能のための統合パッケージです。 このプレビュー機能には、データベースの潜在的な脆弱性を識別し、軽減する機能や、データベースに対する脅威を示す異常な行動を検出する機能が含まれています。 

Azure VM SQL サーバー向けのこのセキュリティ オファリングは、[Azure SQL Database Advanced Data Security パッケージ](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)で使用されているのと同じ基礎技術に基づいています。


## <a name="overview"></a>概要

Advanced Data Security では、脆弱性の評価と Advanced Threat Protection から構成される一連の高度な SQL セキュリティ機能が提供されます。

* [脆弱性評価](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)では、データベースの潜在的な脆弱性を検出、追跡、修復できるサービスを簡単に構成できます。 セキュリティの状態を伝え、セキュリティの問題を解決してデータベースのセキュリティを強化するための手順が含まれます。
* [Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) では、SQL サーバーにアクセスしたり、SQL サーバーを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティが検出されます。 データベースでの不審なアクティビティを継続的に監視し、異常なデータベース アクセス パターンに対してアクション指向のセキュリティ通知を提供します。 このようなアラートからは、不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨されるアクションが提示されます。

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Azure VM の SQL 向け Advanced Data Security を使ってみる

Azure VM の SQL 向け Advanced Data Security (パブリック プレビュー) を使ってみるには、以下の手順に従います。

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Azure VM の SQL 向け Advanced Data Security を設定する

サブスクリプション/ワークスペース レベルで SQL Server on Virtual Machines の Advanced Data Security を有効にします。
 
1. Security Center のサイドバーで、 **[価格と設定]** ページを開きます。

1. Azure VM 上の SQL の Advanced Data Security を有効にするサブスクリプションまたはワークスペースを選択します。

1. **SQL servers on VM (プレビュー)**  のオプションを [有効] に切り替えます。 

    (展開するには、スクリーンショットをクリックしてください)

    [![Windows Admin Center に表示される Security Center のレコメンデーションとアラート](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    選択したワークスペースまたは選択したサブスクリプションの既定のワークスペースに接続されているすべての SQL Server で、SQL Server の Advanced Data Security が有効になります。

    >[!NOTE]
    > このソリューションは、SQL Server の最初の再起動後に完全にアクティブになります。 

新しいワークスペースを作成するには、[Log Analytics ワークスペースの作成](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)に関する記事の手順に従います。

SQL Server のホストをワークスペースに接続するには、「[Windows コンピューターを Azure Monitor に接続する](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)」の手順に従います。


## <a name="set-up-email-notification-for-security-alerts"></a>セキュリティ アラートのメール通知を設定する 

Security Center アラートの生成時にメール通知を受け取る受信者の一覧を設定できます。 メールには、Azure Security Center におけるアラートの直接リンクと関連するすべての詳細が含まれます。 

1. **[Security Center]**  >  **[Pricing & settings]\(価格と設定\)** に移動し、適切なサブスクリプションをクリックします。

    ![サブスクリプション設定](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. [設定] メニューから、 **[メール通知]** をクリックします。 
1. **[メール アドレス]** テキスト ボックスに、通知を受け取るメール アドレスを入力します。 メール アドレスをコンマ (,) で区切ることで複数のメール アドレスを入力できます。  たとえば、「admin1@mycompany.com,admin2@mycompany.com,admin3@mycompany.com」のようにします。

    ![電子メールの設定](./media/security-center-advanced-iaas-data/email-settings.png)

1. **[メール通知]** 設定で、次のオプションを設定します。
  
    * **重要度 - 高についてアラートの電子メール通知を送信する**:すべてのアラートに対してメールを送信する代わりに、重要度が高のアラートに対してのみ送信します。
    * **サブスクリプションの所有者にも電子メール通知を送信する**:サブスクリプションの所有者にも通知を送信します。

1. **[メール通知]** 画面の一番上にある **[保存]** をクリックします。

  > [!NOTE]
  > ウィンドウを閉じる前に必ず **[保存]** をクリックしてください。クリックしない場合、新しい**メール通知**設定が保存されません。

## <a name="explore-vulnerability-assessment-reports"></a>脆弱性評価レポートについて調べる

脆弱性評価ダッシュボードには、すべてのデータベースを対象とする評価結果の概要が表示されます。 SQL Server バージョンによるデータベースのディストリビューション、失格となったデータベースと合格となったデータベースの比較概要、リスク ディストリビューションによるチェック不合格の全体的まとめを確認できます。

脆弱性評価の結果は、Security Center から直接表示することができます。

1. Security Center のサイドバーから、[リソース セキュリティの検疫] の **[データとストレージ]** を選択します。

1. レコメンデーションとして、 **[VM 内の SQL データベースの脆弱性を修復する必要があります (プレビュー)]** を選択します。 詳細については、「[Security Center の推奨事項](security-center-recommendations.md)」を参照してください。 

    [![**VM 内の SQL データベースの脆弱性を修復する必要があります (プレビュー)** レコメンデーション](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    このレコメンデーションの詳細ビューが表示されます。

    [![**VM 内の SQL データベースの脆弱性を修復する必要があります (プレビュー)** レコメンデーションの詳細ビュー](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. ドリルダウンして詳細を表示するには:

    * スキャンされたリソース (データベース) の概要と、テスト済みのセキュリティ チェックの一覧を表示するには、目的のサーバーをクリックします。
    [![SQL サーバーごとにグループ化された脆弱性](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * 特定の SQL データベースごとにグループ化された脆弱性の概要を表示するには、目的のデータベースをクリックします。
    [![SQL サーバーごとにグループ化された脆弱性](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    各ビューでは、セキュリティ チェックが**重要度**順に表示されます。 特定のセキュリティ チェックをクリックすると詳細ウィンドウが表示され、**説明**、**修復方法**、およびその他の関連情報 (**影響**や**ベンチマーク**など) が表示されます。

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Azure VM の SQL サーバー向け Advanced Threat Protection のアラート
普段は見られない、潜在的に有害な SQL Server へのアクセスが試行されると、あるいは SQL Server の悪用が試行されると、アラートが生成されます。 これらのイベントにより、次のアラートがトリガーされる場合があります。

### <a name="anomalous-access-pattern-alerts-preview"></a>異常なアクセス パターンのアラート (プレビュー)

* **通常とは異なる場所からのアクセス:** このアラートは、だれかが通常とは異なる地理的な場所から SQL サーバーにログオンしたことで SQL Server へのアクセス パターンに変化が生じたときにトリガーされます。 考えられる原因は次のとおりです。
    * 攻撃者または悪意のある元社員が SQL Server にアクセスしました。
    * 正当なユーザーが新しい場所から SQL Server にアクセスしました。
* **Access from a potentially harmful application (潜在的に有害なアプリケーションからのアクセス)** :このアラートは、データベースにアクセスするために潜在的に有害なアプリケーションが使用されたときにトリガーされます。 考えられる原因は次のとおりです。
    * 一般的な攻撃ツールで攻撃者が SQL を侵害しようとしています。
    * 正当な侵入テストが行われています。
* **Access from unfamiliar principal (通常とは異なるプリンシパルからのアクセス)** :このアラートは、だれかが通常とは異なるプリンシパル (SQL ユーザー) を使用して SQL サーバーにログオンしたことで SQL サーバーへのアクセス パターンに変化が生じたときにトリガーされます。 考えられる原因は次のとおりです。
    * 攻撃者または悪意のある元社員が SQL Server にアクセスしました。 
    * 正当なユーザーが新しいプリンシパルで SQL Server にアクセスしました。
* **Brute force SQL credentials (SQL 資格情報に対するブルート フォース攻撃)** :このアラートは、異なる資格情報でログインに失敗した回数が異常に多いときにトリガーされます。 考えられる原因は次のとおりです。
    * ブルート フォースで攻撃者が SQL を侵害しようとしています。
    * 正当な侵入テストが行われています。

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>潜在的な SQL インジェクション (SQL Server 2019 でサポートされています)

* **Vulnerability to SQL Injection (SQL インジェクションにつながる脆弱性)** :このアラートは、アプリケーションがデータベースにエラーのある SQL ステートメントを生成したときにトリガーされます。 このアラートは、SQL インジェクション攻撃に対する脆弱性が存在する可能性を示すものです。 考えられる原因は次のとおりです。
    * アプリケーション コードの欠陥により、エラーのある SQL 文が作成される
    * アプリケーション コードまたはストアド プロシージャが、SQL インジェクションに悪用される可能性があるエラーのある SQL ステートメントを作成するときにユーザー入力をサニタイズしない
* **Potential SQL injection (SQL インジェクションの可能性)** :このアラートは、SQL インジェクションに対する特定されたアプリケーションの脆弱性に対してアクティブな悪用が発生したときにトリガーされます。 これは、攻撃者が脆弱なアプリケーション コードまたはストアド プロシージャを使用して悪意のある SQL 文を挿入しようとしていることを意味します。


### <a name="unsafe-command-supported-in-sql-server-2019"></a>Unsafe コマンド (SQL Server 2019 でサポートされています)

* **安全でない可能性のあるアクション**:このアラートは、高い特権を持ち、安全でない可能性のあるコマンドが実行されたときにトリガーされます。 考えられる原因は次のとおりです。
    * セキュリティ体制を強化するために無効にすることが推奨されるコマンドが有効になっている。
    * 攻撃者が SQL アクセスを悪用しようとしているか、特権を昇格しようとしている。   


## <a name="explore-and-investigate-security-alerts"></a>セキュリティ通知について調査する

データ セキュリティのアラートは、Security Center の [アラート] ページ、リソースの [セキュリティ] タブ、またはアラート メールの直接リンクで使用できます。

1. アラートを表示するには、次の手順に従います。

    * Security Center で、サイドバーの **[セキュリティ アラート]** をクリックし、アラートを選択します。
    * リソース スコープで、関連するリソース ページを開き、サイドバーで **[セキュリティ]** をクリックします。 

1. アラートは自己完結型であり、それぞれに詳細な修復の手順と調査情報が含まれています。 他の Azure Security Center と Azure Sentinel の機能を使用してさらに詳しく調べることができます。

    * 詳しく調べるには SQL Server の監査機能を有効にします。 Azure Sentinel ユーザーの場合は、Windows セキュリティ ログのイベントから Sentinel に SQL 監査ログをアップロードして、豊富な調査機能を利用できます。
    * セキュリティ体制を強化するには、各アラートに示されているホスト マシンに関する Security Center の推奨事項を使用します。 これにより、将来の攻撃のリスクが軽減されます。 


## <a name="next-steps"></a>次のステップ

関連資料については、次の記事をご覧ください。

- [推奨事項を修復する方法](security-center-remediate-recommendations.md)