---
title: Azure Stack のログおよびデータの処理 | Microsoft Docs
description: Azure Stack で情報を収集する方法について説明します。
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2019
ms.author: PatAltimore
ms.reviewer: chengwei
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 7beb9dc31e8987d69c2cf9cf64be36caf0e0e308
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2019
ms.locfileid: "56319021"
---
# <a name="azure-stack-log-and-customer-data-handling"></a>Azure Stack のログおよび顧客データの処理 
*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*  

Microsoft は、Azure Stack に関係する個人データのプロセッサーまたはサブプロセッサーである範囲において、2018 年 5 月 25 日、すべてのお客様に対して、(a) [オンライン サービス条件](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551078818&sdata=cpWsfZTBHpqEFr50DWQOryq342U8shgeFgMXVPQz5ug%3D&reserved=0)の "データ保護条件" セクションの "個人データの処理: GDPR" 条項、および (b) [オンライン サービス条件](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551088813&sdata=bv1CBiaCnYmjiv6S0dFCbWEd4fNCkPBjBwgylNa%2FNt0%3D&reserved=0)の特則 4 の EU 一般データ保護規則の条件に対するコミットメントを有効にします。 

Azure Stack がお客様のデータセンター内に常駐している間、Microsoft は、[診断](azure-stack-diagnostics.md)、[テレメトリ](azure-stack-telemetry.md)、および[課金](azure-stack-usage-reporting.md)を通じて Microsoft と共有されるデータのみを制御します。  

## <a name="data-access-controls"></a>データ アクセスの制御 
特定のサポート ケースを調査するために割り当てられた Microsoft の従業員には、暗号化されたデータを読み取り専用でアクセスする権限が付与されます。 また、Microsoft の従業員は、データを必要に応じて削除できるツールを利用できます。 顧客データへのアクセスは、すべて監査および記録されます。  

データ アクセスの制御:
1.  データは、ケースの終了後、最大でも 90 日間しか保持されません。
2.  お客様は、その 90 日の期間中、いつでもデータを削除することを選択できます。
3.  Microsoft の従業員は、サポート問題を解決するために、 ケースバイケースなおかつ必要な場合にのみ、データにアクセスできます。 
4.  Microsoft が顧客データを OEM パートナーと共有する必要がある場合は、必ずお客様の同意を得る必要があります。  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>お客様が持つデータ主体の要求 (DSR) コントロール
前述したように、Microsoft は、お客様の要求に応じたオンデマンドでのデータ削除をサポートしています。 お客様は、データが完全に消去されるより前に、お客様が選んだ任意の時点でサポート エンジニアに特定のケースのすべてのログを削除してもらうことを要求できます。  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>Microsoft は、データの削除時にお客様に通知するか
自動データ削除アクションを実行する場合 (ケースが終了した 90 日後)、Microsoft は、データの削除に関して事前にお客様に連絡をとって通知することはありません。 

オンデマンド データ削除アクションを実行する場合、Microsoft サポート エンジニアは、データの削除をオンデマンドで開始できるツールにアクセスし、データの削除が完了したら、その旨を電話でお客様に確認します。

## <a name="diagnostic-data"></a>診断データ
サポート プロセスの一環として、Azure Stack Operator は、トラブルシューティングを円滑に行うため、Azure Stack のサポート チームおよびエンジニアリング チームと[診断ログを共有](azure-stack-diagnostics.md)できます。

Microsoft は、お客様が、要求された診断ログ ファイルを収集してアップロードするためのツールおよびスクリプトを提供します。 収集されたログ ファイルは、HTTPS で保護された暗号化接続を通じて Microsoft に転送されます。 HTTPS では、ネットワーク データが暗号化されるため、転送時に暗号化のためのパスワードは不要です。 受信されたログは、サポート ケースが終了して 90 日後に自動削除されるまで、暗号化された状態で保管されます。

## <a name="telemetry-data"></a>テレメトリ データ
[Azure Stack のテレメトリ](azure-stack-telemetry.md)では、接続ユーザー エクスペリエンスを介してシステム データが自動的に Microsoft にアップロードされます。 テレメトリの機能やプライバシー設定は、Azure Stack Operator の権限でいつでもカスタマイズできます。

Microsoft が意図して機微なデータ (クレジット カード番号、ユーザー名とパスワード、メール アドレスなど、秘匿性の高い情報) を収集することはありません。 意図せず機密情報を受け取ったと Microsoft が判断した場合には、削除します。 

## <a name="billing-data"></a>課金データ
[Azure Stack の課金](azure-stack-usage-reporting.md)では、グローバルな Azure の課金および使用パイプラインが活用されます。このため、Microsoft のコンプライアンス ガイドラインに合致しています。

Azure Stack Operator は、課金のための使用状況情報を Azure に転送するように Azure Stack を構成できます。 マルチノードの Azure Stack を利用しており、従量課金モデルを選択した場合は、この構成が必須となります。 使用状況のレポートは、テレメトリとは別に管理されます。マルチノードをご利用の場合でも、容量モデルを選択したユーザーや Azure Stack Development Kit ユーザーについては、使用状況の報告は不要です。 そのような場合は、[登録スクリプト](azure-stack-usage-reporting.md)を使って、使用状況のレポートを無効にすることができます。


## <a name="next-steps"></a>次の手順 
[Azure Stack のセキュリティについて詳しく学習する](azure-stack-security-foundations.md) 
