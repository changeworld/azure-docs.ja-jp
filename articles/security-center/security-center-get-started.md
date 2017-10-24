---
title: "Azure Security Center クイック スタート ガイド | Microsoft Docs"
description: "この記事では、Azure Security Center のセキュリティ監視およびポリシー管理コンポーネントと、次のステップへの展開を説明することで、この Azure Security Center をすぐに開始できるようにします。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: c28f92af96f31d1c386cf072f83fc142b9a7f588
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-quick-start-guide"></a>Azure Security Center クイック スタート ガイド
この記事では、Azure Security Center のセキュリティ監視およびポリシー管理コンポーネントについて説明することで、この Azure Security Center をすぐに開始できるようにします。

## <a name="prerequisites"></a>前提条件
セキュリティ センターを使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。

Security Center の Free レベルは、すべての Azure サブスクリプションで自動的に有効にされ、セキュリティ ポリシー、継続的なセキュリティ評価、および Azure リソースを保護するための実践的なセキュリティに関する推奨事項を提供します。

Security Center には [Azure ポータル](https://azure.microsoft.com/features/azure-portal/)からアクセスします。 Azure Portal の詳細については、[ポータルに関するドキュメント](https://azure.microsoft.com/documentation/services/azure-portal/)をご覧ください。

## <a name="permissions"></a>アクセス許可
Security Center では、リソースが属するサブスクリプションまたはリソース グループの所有者、共同作業者、閲覧者のいずれかのロールが割り当てられているときにリソースに関連した情報のみが表示されます。 Security Center のロールと許可されているアクションの詳細については、「[Permissions in Azure Security Center (Azure Security Center のアクセス許可)](security-center-permissions.md)」を参照してください。

## <a name="data-collection"></a>データ収集
Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure 仮想マシン (VM) と非 Azure コンピューターからデータを収集します。 データは、Microsoft Monitoring Agent を使用して収集されます。Microsoft Monitoring Agent は、セキュリティ関連のさまざまな構成とイベント ログをマシンから読み取り、分析のためにデータをワークスペースにコピーします。 Security Center は、サポートされている既存の Azure VM と新しく作成される VM すべてに対して Microsoft Monitoring Agent をプロビジョニングします。 データ収集のしくみの詳細については、[データ収集の有効化](security-center-enable-data-collection.md)に関するページを参照してください。

自動プロビジョニングを使用することを強くお勧めします。自動プロビジョニングは、Security Center の Standard レベルのサブスクリプションでは必須です。 自動プロビジョニングを無効にすると、リソースのセキュリティの監視が制限されます。

Free 価格レベルと Standard 価格レベルの詳細については、[Security Center の価格](security-center-pricing.md)に関するページを参照してください。

次の手順では、Security Center のコンポーネントにアクセスする方法と、そのコンポーネントの使用方法について説明します。

> [!NOTE]
> この記事では、サンプル デプロイを使用してサービスについて紹介します。 この記事はステップ バイ ステップ ガイドではありません。
>
>

## <a name="access-security-center"></a>セキュリティ センターへのアクセス
セキュリティ センターにアクセスするには、ポータルで次の手順を実行します。

1. **[Microsoft Azure]** メニューの **[セキュリティ センター]** を選択します。

   ![Azure メニュー][1]
2. Security Center に初めてアクセスする場合は、**[ようこそ]** ブレードが開きます。 **[Security Center の起動]** を選択して、**Security Center** を開きます。
   ![[ようこそ] 画面][10]
3. [ようこそ] ブレードから Security Center を起動するか、Microsoft Azure のメニューから Security Center を選択すると、**Security Center** が開きます。 後で **[セキュリティ センター]** ブレードにアクセスしやすいように、右上にある **[ダッシュボードにブレードをピン留め]** オプションを選択します。
   ![Pin blade to dashboard option][2]

## <a name="use-security-center"></a>セキュリティ センターの使用
Azure サブスクリプションとリソース グループのセキュリティ ポリシーを構成できます。 サブスクリプションのセキュリティ ポリシー を構成してみましょう。

1. Security Center のメイン メニューで、**[セキュリティ ポリシー]** を選択します。
2. **[Security Center - Security policy]\(Security Center - セキュリティ ポリシー\)** で、サブスクリプションを選択します。
3. **[Security policy - Data Collection ]\(セキュリティ ポリシー - データ収集\)** で**自動プロビジョニング**を有効にします。 Security Center は、サポートされている既存の Azure VM と新しく作成される VM すべてに対して Microsoft Monitoring Agent をプロビジョニングします。

    ![セキュリティ ポリシー][12]

4. **[セキュリティ ポリシー]** ブレードで、**[セキュリティ ポリシー]** ポリシー コンポーネントを選択します。

     ![セキュリティ ポリシー][11]

5. **[推奨事項を表示:]** で、セキュリティ ポリシーの一部として表示する推奨事項をオンにします。 次に例を示します。

   * **[システムの更新プログラム]** を **[オン]** に設定すると、サポートされているすべての VM で不足している OS の更新プログラムがスキャンされます。
   * **[OS の脆弱性]** を **[オン]** に設定すると、サポートされているすべての VM がスキャンされ、攻撃に対する VM の脆弱性を高める可能性がある OS の構成が特定されます。

6. [ **保存**] を選択します。

### <a name="view-recommendations"></a>推奨事項の表示
1. **[セキュリティ センター]** ブレードに戻り、**[推奨事項]** タイルを選択します。 セキュリティ センターは、Azure リソースのセキュリティの状態を定期的に分析します。 Security Center では、潜在的なセキュリティの脆弱性が特定されると、推奨事項が **[推奨事項]** ブレードに表示されます。
   ![Azure Security Center の推奨事項][5]
2. **[推奨事項]** ブレードで推奨事項を選択すると、詳細な情報を表示したり、問題を解決するためのアクションを実行したりできます。

### <a name="view-the-security-state-of-your-resources"></a>リソースのセキュリティの状態を表示する
1. **[セキュリティ センター]** ブレードに戻ります。 ダッシュボードの **[防止策]** セクションには、VM、ネットワーク、データ、アプリケーションのセキュリティ状態インジケーターが表示されます。
2. **[Compute (コンピューティング)]** を選択して、詳細を表示します。 **[Compute (コンピューティング)]** ブレードが開き、3 つのタブが表示されます。

  - **[概要]** - 監視と VM の推奨事項が表示されます。
  - **[VM およびコンピューター]** - すべての VM とコンピューターに関する現在のセキュリティ状態が一覧表示されます。
  - **[クラウド サービス]** - Security Center で監視されている Web ロールと worker ロールの一覧が表示されます。

    ![コンピューティングのセキュリティ正常性][6]

3. **[概要]** タブの推奨事項を選択して、詳細情報を表示したり、必要な制御を構成するためのアクションを実行したりします。
4. **[VM およびコンピューター]** タブで、リソースを選択して詳細を表示します。

### <a name="view-security-alerts"></a>セキュリティの警告の表示
1. **[セキュリティ センター]** ブレードに戻り、**[セキュリティ通知]** タイルを選択します。 **[セキュリティ通知]** ブレードが開き、警告の一覧が表示されます。 こうした警告は、Security Center によるセキュリティ ログとネットワーク アクティビティの分析によって生成されます。 これには、統合されているパートナー ソリューションの警告も含まれます。
   ![Security alerts in Azure Security Center][7]

2. アラートを選択すると詳細な情報が表示されます。 この例では、**[Modified system binary discovered in dump filter]\(ダンプ ファイルにおけるシステム バイナリの改変の検出\)** を選択します。 これによりブレードが開き、警告の詳細情報が表示されます。
   ![Azure Security Center のセキュリティ警告の詳細][8]

### <a name="view-the-health-of-your-partner-solutions"></a>パートナー ソリューションの正常性の表示
1. **[セキュリティ センター]** ブレードに戻ります。 **[セキュリティ ソリューション]** タイルでは、Azure サブスクリプションと統合されたパートナー ソリューションの正常性状態をひとめで監視できます。
2. **[セキュリティ ソリューション]** タイルを選択します。 ブレードが開き、Security Center に接続されているパートナー ソリューションの一覧が表示されます。
   ![[パートナー ソリューション]][9]
3. パートナー ソリューションを選択します。 ブレードが開き、パートナー ソリューションとそのソリューションの関連リソースの状態が表示されます。 **[ソリューション コンソール]** を選択して、このソリューションのパートナー管理エクスペリエンスを開きます。

   ![パートナー ソリューション][13]

## <a name="next-steps"></a>次のステップ
この記事では、Security Center のセキュリティ監視とポリシー管理のコンポーネントについて説明しました。 Security Center について理解したら、次の手順をお試しください。

* Azure サブスクリプションのセキュリティ ポリシーを構成するには、[Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md)に関するページを参照してください。
* Azure リソースを保護するのに役立つ Security Center の推奨事項を使用するには、「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」を参照してください。
* 現在のセキュリティ アラートを確認して管理するには、「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」を参照してください。
- パートナーと統合してセキュリティを全体的に向上させる方法を学習するには、[パートナーとソリューションの統合](security-center-partner-integration.md)に関するページを参照してください。
- Security Center でデータがどのように管理および保護されているかを学習するには、「[Azure Security Center のデータ セキュリティ](security-center-data-security.md)」を参照してください。
* Security Center の [Standard レベル](security-center-pricing.md)で利用できる[高度な脅威検出機能](security-center-detection-capabilities.md)について確認します。 Standard レベルは、最初の 60 日間は無料です。
* Security Center に関する質問がある場合は、[Azure Security Center の FAQ](security-center-faq.md) をご覧ください。

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png
[11]: ./media/security-center-get-started/show-recommendations-for.png
[12]: ./media/security-center-get-started/automatic-provisioning.png
[13]: ./media/security-center-get-started/partner-solutions-detail.png
