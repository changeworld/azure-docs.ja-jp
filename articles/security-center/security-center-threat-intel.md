---
title: Azure Security Center の脅威インテリジェンス | Microsoft Docs
description: Azure Security Center の脅威インテリジェンス機能を使用して、VM とコンピューターの潜在的な脅威を特定する方法について説明します。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2018
ms.author: terrylan
ms.openlocfilehash: 8f1c6aa2e691a11e8920db8ca8bfdef5b8eb61b9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434192"
---
# <a name="threat-intelligence-in-azure-security-center"></a>Azure Security Center の脅威インテリジェンス
この記事は、Azure Security Center の脅威インテリジェンスを使用して、セキュリティ関連の問題に対処する際に役立ちます。

## <a name="what-is-threat-intelligence"></a>脅威インテリジェンスとは
IT 管理者は、Security Center で利用可能な脅威インテリジェンス オプションを使用することで、環境に対するセキュリティの脅威を特定することができます。 たとえば、ある特定のコンピューターがボットネットの一部であるかどうかを特定できます。 コンピューターをひそかにコマンド アンド コントロール サーバーに接続するマルウェアが攻撃者によって不正にインストールされた場合、そのコンピューターはボットネットのノードになる可能性があります。 また、脅威インテリジェンスでは、闇サイトなどのアンダーグラウンド通信チャネルに由来する潜在的な脅威を特定することもできます。

Security Center では、この脅威インテリジェンスを構築するために、Microsoft 内部の複数のソースから取得したデータが使用されています。 Security Center は、このデータを使用して、環境に対する潜在的な脅威を特定します。 **[脅威インテリジェンス]** ウィンドウは、3 つの主要なオプションで構成されています。

- Detected threat types
- 脅威の発生元
- 脅威インテリジェンス マップ


## <a name="when-should-you-use-threat-intelligence"></a>脅威インテリジェンスを使用する状況
[セキュリティ インシデント対応プロセス](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response)の手順の 1 つは、侵害されたシステムの重大度を特定することです。 このフェーズでは、次のタスクを実行する必要があります。

- 攻撃の性質を特定する。
- 攻撃の発生ポイントを特定する。
- 攻撃の目的を特定する。 その攻撃が、特定の情報を取得するために自分の組織に向けられたものか、それともランダムなものかを特定します。
- 侵害されたシステムを特定する。
- アクセスされたファイルを特定して、そのファイルの機密度を判定する。

これらのタスクには、Security Center の脅威インテリジェンス情報を活用できます。

## <a name="access-the-threat-intelligence"></a>脅威インテリジェンスへのアクセス
現在の環境の脅威インテリジェンスを視覚化するには、まず、情報が存在するワークスペースを選択する必要があります。 ワークスペースが複数ない場合、ワークスペース セレクターは表示されず、**[脅威インテリジェンス]** ダッシュボードに直接移動します。 ダッシュボードにアクセスするには:

1. **[Security Center]** ダッシュボードを開きます。

1. 左側のウィンドウの **[Threat Protection] (脅威からの保護)** で、**[脅威インテリジェンス]** を選択します。 マップが開きます。

    ![脅威インテリジェンス マップ](./media/security-center-threat-intel/security-center-threat-intel.png)

1. マップの上部で、**[View classic threat intelligence] (従来の脅威インテリジェンスを表示する)** を選択します。 **[脅威インテリジェンス]** ダッシュボードが開きます。

    ![[脅威インテリジェンス] ダッシュボード](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > 右端の列に **[アップグレード プラン]** と表示されている場合、このワークスペースでは無料のサブスクリプションが使用されています。 この機能を使用するには、Standard にアップグレードしてください。 右端の列に **[REQUIRES UPDATE]\(更新が必要\)** と表示されている場合は、この機能を使用するために [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) を更新してください。 料金プランの詳細については、「Azure Security Center の価格」を参照してください。
    >
1. 調査するワークスペースが複数ある場合は、**[悪意のある IP]** 列に従って調査に優先順位を付けます。 ここには、このワークスペースに含まれている悪意のある IP の現在の数が表示されます。 使用するワークスペースを選択すると、**[脅威インテリジェンス]** ダッシュボードが表示されます。

    ![脅威インテリジェンス情報](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

1. このダッシュボードは、4 つのタイルに分かれています。

    a.  **[脅威の種類]**。 選択したワークスペースで検出された脅威の種類が示されます。

    b.  **[発信元の国]**。 ソースの場所に基づいてトラフィックの量を集計します。

    c.  **[脅威の場所]**。 使用中の環境と通信する、世界の現在の場所を特定するために役立ちます。 表示されているマップでは、オレンジ色 (受信) と赤 (送信) の矢印でトラフィックの方向を示します。 これらの矢印のいずれかを選択すると、脅威の種類とトラフィックの方向が表示されます。

    d.  **[脅威の詳細]**。 マップで選択した脅威に関する詳細が表示されます。

選択するオプション タイルにかかわらず、表示されるダッシュボードはログ検索クエリに基づいています。 唯一の違いは、クエリの種類と結果です。

### <a name="threat-types"></a>脅威の種類
**[脅威の種類]** タイルを選択すると、**[ログ検索]** ダッシュボードが開きます。 左側にフィルター オプション、右側にクエリ結果が表示されます。

![ログ検索](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

クエリ結果には、脅威が名前ごとに表示されます。 左側のウィンドウを使用して、フィルター処理する属性を選択できます。 たとえば、マシンに現在接続されている脅威のみを表示するには、**[SESSIONSTATE]\(セッションの状態\)** で **[接続済み]** > **[適用]** の順に選択します。

![セッションの状態](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

Azure VM の場合、エージェントを通じて送信されるネットワーク データだけが **[脅威インテリジェンス]** ダッシュボードに表示されます。 次のデータ型は、脅威インテリジェンスでも使用されます。

- CEF データ (種類 = CommonSecurityLog)
- WireData (種類 = WireData)
- IIS ログ (種類 = W3CIISLog)
- Windows ファイアウォール (種類 = WindowsFirewall)
- DNS イベント (種類 = DnsEvents)


## <a name="see-also"></a>関連項目
この記事では、Security Center の脅威インテリジェンスを使用して、疑わしいアクティビティの特定を支援する方法について説明しました。 Security Center の詳細については、次の記事を参照してください。

* [Azure Security Center でのセキュリティ アラートの管理と対応](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 Security Center でアラートを管理し、セキュリティ インシデントに対応する方法を説明します。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center のセキュリティ アラートの概要](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 さまざまな種類のセキュリティ アラートについて説明します。
* [Azure Security Center トラブルシューティング ガイド](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 Security Center における一般的な問題のトラブルシューティング方法について説明します。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)。 このサービスの使用に関してよく寄せられる質問とその回答を紹介しています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
