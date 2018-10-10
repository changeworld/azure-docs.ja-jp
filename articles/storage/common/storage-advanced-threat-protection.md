---
title: Azure Storage で脅威を監視する
description: アカウント アクティビティの異常を検出し、害を及ぼす可能性のあるアカウントへのアクセス試行を通知するように、Azure Storage Advanced Threat Protection を構成します。
services: storage
author: rmatchoro
ms.service: storage
ms.topic: article
ms.date: 09/24/2018
ms.author: ronmat
ms.manager: shaik
ms.openlocfilehash: 8b2ca2d5d6418d68cab847df80fc437e468249ed
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995647"
---
# <a name="azure-storage-advanced-threat-protection"></a>Azure Storage Advanced Threat Protection

Azure Storage Advanced Threat Protection は、アカウント アクティビティの異常を検出し、害を及ぼす可能性のあるアカウントへのアクセス試行を通知します。 この保護層により、セキュリティの専門家でなくても、セキュリティ監視システムを管理しなくても、脅威に対処することができます。

アクティビティで異常が発生したときにトリガーするセキュリティ アラートを定義することで、脅威が明るみに出ます。 これらのアラートが [Azure Security Center](https://azure.microsoft.com/services/security-center/) と統合されます。これには、不審なアクティビティの詳細と、脅威の調査や修復方法に関する推奨事項が含まれます。 

> [!NOTE]
> Azure Storage Advanced Threat Protection は現在、Blob service でのみ使用できます。 セキュリティ アラートは、Azure Security Center と統合され、サブスクリプション管理者に電子メールで送信されます。

Azure Storage Advanced Threat Protection は、脅威の検出のため、Blob service に対する読み取り要求、書き込み要求、削除要求の診断ログを取り込みます。 Advanced Threat Protection からのアラートを調査するには、Blob service のすべてのレベルのログを有効にするように[診断ログを構成する](storage-monitor-storage-account.md#configure-logging)必要があります。

## <a name="set-up-advanced-threat-protection-in-the-portal"></a>ポータルで Advanced Threat Protection を設定する

1. Azure Portal ([https://portal.azure.com](https://portal.azure.com/)) を開きます。

2. 保護する Azure Storage アカウントの構成ページに移動します。 **[設定]** ページで、**[Advanced Threat Protection]** を選びます。

3. **[Advanced Threat Protection]** 構成ブレードで次のようにします。
    * *Advanced Threat Protection* を **[オン]** にします。
    * **[保存]** をクリックして、新しいまたは更新された Azure Storage ポリシーを保存します。

![Azure Storage Advanced Threat Protection をオンにする](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

## <a name="explore-anomalies"></a>異常を調査する

ストレージ アクティビティの異常が発生すると、疑わしいセキュリティ イベントに関する情報を含む通知が電子メールで送信されます。 イベントの詳細には、次のものが含まれます。

* 異常の種類
* ストレージ アカウント名
* ストレージの種類
* イベント時間

電子メールには、潜在的な脅威の考えられる原因と調査や緩和のための推奨されるアクションについての詳細も含まれます。

![Azure Storage Advanced Threat Protection のアラート メール](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Azure Security Center の [[セキュリティ アラート]](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts) タイルから、現在のセキュリティ アラートを確認して管理することができます。 特定のアラートをクリックすると、詳細な情報と、現在の脅威を調査し、今後の脅威に対処するためのアクションが表示されます。

![Azure Storage Advanced Threat Protection のアラート メール](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>保護アラート

アラートは、ストレージ アカウントへの通常とは異なる、害を及ぼす可能性のあるアクセス試行やストレージ アカウントの悪用が発生すると、生成されます。 これらのイベントにより、次のアラートがトリガーされる場合があります。

* **Access from unusual location (通常とは異なる場所からのアクセス)**: このアラートは、ストレージ アカウントへのアクセス パターンに変化が生じたときにトリガーされます。 たとえば、だれかが通常とは異なる地理的な場所からストレージ アカウントにアクセスした場合などです。 このアラートによって、正当なアクション (新しいアプリケーションや開発者によるメンテナンス操作) が検出されることがあります。 別のケースでは、このアラートによって悪意のあるアクション (元従業員、外部の攻撃者など) が検出されます。

* **Unusual data extraction (通常とは異なるデータの抽出)**: このアラートは、ストレージ アカウントからのデータ抽出パターンに変化が生じたときにトリガーされます。 たとえば、だれかがストレージ アカウント内の通常とは異なる量のデータにアクセスしている場合などです。 このアラートで正当なアクション (メンテナンス アクティビティ) が検出されることがあります。 それ以外の場合は、このアラートによって悪意のあるアクション (データの流出/侵害、データの不正転送) が検出されます。

* **Unusual anonymous access (通常とは異なる匿名アクセス)**: このアラートは、ストレージ アカウントへのアクセス パターンに変化が生じたときにトリガーされます。 たとえば、だれかがストレージ アカウントに匿名でアクセスした場合などです。 このアラートでパブリック読み取りアクセスを使用した正当なアクセスが検出されることがあります。 それ以外の場合は、このアラートによって、パブリック読み取りアクセスを悪用したコンテナーとその BLOB への不正アクセスが検出されます。

* **Unexpected delete (予期しない削除):** このアラートは、ストレージ アカウントの履歴分析に基づいて、ストレージ アカウントで 1 つ以上の予期しない削除操作が発生したときにトリガーされます。 たとえば、だれかが新しいアプリケーションを使用して新しい IP アドレスから *DeleteBlob* 操作を実行した場合などです。 このアラートで正当なアクション (管理者が出張中に別のブラウザーを使用) が検出されることがあります。 それ以外の場合は、このアラートによって、悪意のあるアクション (攻撃者がデータを削除) が検出されます。 
 
* **Access permission change (アクセス許可の変更):** このアラートは、ストレージ アカウントへのアクセス許可の予期しない変更が発生した場合にトリガーされます。 たとえば、だれかが新しいアプリケーションを使用して新しい IP アドレスからストレージ アカウントへのアクセス許可を変更した場合などです。 このアラートで正当なアクション (管理者が出張中に別のブラウザーを使用) が検出されることがあります。 それ以外の場合は、このアラートによって、悪意のあるアクション (攻撃者がアクセスを獲得したアカウントの特権を増やすなど) が検出されます。 

* **Upload Azure Cloud Service package (Azure クラウド サービス パッケージのアップロード):** このアラートは、ストレージ アカウントへの Azure クラウド サービス パッケージ (*.cspkg* ファイル) の予期しないアップロードが発生した場合にトリガーされます。 たとえば、新しい IP アドレスから *.cspkg* ファイルがアップロードされた場合などです。 このアラートで正当なアクションが検出されることがあります。 それ以外の場合は、このアラートによって、悪意のあるアクション (悪意のあるサービスをデプロイするための準備として、クラウド サービス パッケージがアップロードされたなど) が検出されます。    
   

## <a name="next-steps"></a>次の手順

* [Azure ストレージ アカウントのログ](/rest/api/storageservices/About-Storage-Analytics-Logging)について学習する

* [Azure Security Center](../../security-center/security-center-intro.md) の詳細について参照してください