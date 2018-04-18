---
title: Microsoft Advanced Threat Analytics を Azure Security Center に接続する | Microsoft Docs
description: Azure Security Center と Microsoft Advanced Threat Analytics を統合する方法について説明します。
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: ''
ms.assetid: 5d80bf91-16c3-40b3-82fc-e0805e6708db
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: yurid
ms.openlocfilehash: fb32fc55288229fb1d09dce98b6092d38c10abf2
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="connecting-microsoft-advanced-threat-analytics-to-azure-security-center"></a>Microsoft Advanced Threat Analytics を Azure Security Center に接続する
このドキュメントは、Microsoft Advanced Threat Analytics と Azure Security Center 間の統合を構成する場合に役立ちます。

## <a name="why-add-advanced-threat-analytics-data"></a>Advanced Threat Analytics データを追加する理由
[Advanced Threat Analytics (ATA)](https://docs.microsoft.com/advanced-threat-analytics/what-is-ata) は、疑わしいユーザー動作の検出に役立つオンプレミス プラットフォームです。 接続すると、ATA で検出された疑わしいアクションを Security Center で確認できるようになります。 この統合で、ハイブリッド クラウド ワークロードに関連するすべてのセキュリティ アラートの表示、関連付け、および調査を Security Center で実行できるようになります。 

## <a name="how-do-i-configure-this-integration"></a>この統合を構成する方法
ATA が既にインストールされ、オンプレミスで適切に動作しているとして、次の手順に従ってこの統合を構成してください。

1. ATA Center にログオンし、ATA ポータルにアクセスします。
2. 左側のウィンドウで **[Syslog サーバー]** をクリックします。

    ![Syslog サーバー](./media/security-center-ata-integration/security-center-ata-integration-fig1.png)

3. **[Syslog サーバー エンドポイント]** フィールドに「127.0.0.7」(このアドレスにする必要があります) と入力し、ポートに 5114 (推奨) と入力します。 このポート番号が推奨されますが、任意の一意のポートでも使用できます。 その他のすべてのオプションをそのままにして **[保存]** をクリックします。
4. 左側のウィンドウの **[通知]** をクリックし、次の図のようにすべての Syslog 通知を有効にします (推奨)。

    ![通知](./media/security-center-ata-integration/security-center-ata-integration-fig2.png)

5. **[Save]** をクリックします。
6. **[Security Center]** ダッシュボードを開きます。
7. 左側のウィンドウで **[セキュリティ ソリューション]** をクリックします。
8. **[Advanced Threat Analytics]** で **[追加]** をクリックします。

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig3.png)
    
9. 最後の手順に進み、**[エージェントのダウンロード]** をクリックします。

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig4.png)

10. **[Azure 以外のコンピューターの新規追加]** ページでワークスペースを選択します。

    ![Azure 以外](./media/security-center-ata-integration/security-center-ata-integration-fig5.png)

11. **[ダイレクト エージェント]** ページで適切な Windows エージェントをダウンロードし、**[ワークスペース ID]** と **[プライマリ キー]** をメモします。

    ![直接エージェント](./media/security-center-ata-integration/security-center-ata-integration-fig6.png)

12. ATA Center でこのエージェントをインストールします。 インストール中に **[Connect the agent to Azure Log Analytics]\(Azure Log Analytics にエージェントを接続する\)** オプションをオンにして、指示に従って*ワークスペース ID* と*プライマリ キー*を入力します。


インストールを完了し、統合が完了すると、ATA から Security Center に送信された新しいアラートを **[検索]** 結果で確認できるようになります。 ソリューションは **[接続済みソリューション]** の **[セキュリティ ソリューション]** に表示されます。 

## <a name="next-steps"></a>次の手順
このドキュメントでは、Microsoft ATA を Security Center に接続する方法について説明しました。 Security Center の詳細については、次の記事を参照してください。

* [Azure Active Directory Identity Protection を Azure Security Center に接続する](security-center-aadip-integration.md)
* [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md) -- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md) 」-- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」 -- Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」 -- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」 -- パートナー ソリューションの正常性状態を監視する方法について説明しています。
- 「[Azure Security Center のデータ セキュリティ](security-center-data-security.md)」 - Security Center でデータがどのように管理および保護されているかを説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティに関する最新のニュースと情報を入手できます。


