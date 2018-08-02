---
title: Azure Security Center におけるアダプティブ アプリケーション制御 | Microsoft Docs
description: このドキュメントは、Azure VM で実行されるアプリケーションのホワイトリストを作成するために、Azure Security Center でアダプティブ アプリケーション制御を使用する際に役に立ちます。
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2018
ms.author: rkarlin
ms.openlocfilehash: 6dd971ff8cd51435978ab80db006f6494dff8a94
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39389329"
---
# <a name="adaptive-application-controls-in-azure-security-center"></a>Azure Security Center におけるアダプティブ アプリケーション制御
このチュートリアルを使用して、Azure Security Center でアプリケーション制御を構成する方法を説明します。

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Security Center でのアダプティブ アプリケーション制御とは
アダプティブ アプリケーション制御は、Azure 内の VM 上でどのアプリケーションを実行できるかを制御するために役立ちます。これにはさまざまな利点がありますが、たとえばマルウェアに対する VM の保護の強化に役立ちます。 Security Center は、機械学習によって VM で実行されているアプリケーションを分析し、この情報を利用することで、お客様がホワイトリスト登録に関する規則を適用するのを支援します。 この機能によって、アプリケーションのホワイトリストを構成および保守するプロセスが大幅に簡略化され、以下のことができるようになります。

- 悪意のあるアプリケーション (マルウェア対策ソリューションでは見逃される可能性のあるものを含む) の試行をブロックまたは警告する
- ライセンスのあるソフトウェアのみを使用するという組織のセキュリティ ポリシーに準拠する。
- 環境内で使用されることが望ましくないソフトウェアを避ける。
- 古くてサポートされていないアプリが実行されることを避ける。
- 組織で許可されていない特定のソフトウェア ツールが実行されないようにする。
- アプリの使用状況を通じて、機密データへのアクセスを IT 部門が制御できるようにする。

## <a name="how-to-enable-adaptive-application-controls"></a>アダプティブ アプリケーション制御を有効にする方法
アダプティブ アプリケーション制御では、構成済みグループに対する実行が許可される一連のアプリケーションを定義できます。 この機能が使用できるのは、Windows マシンのみです (すべてのバージョン、クラシックまたは Azure Resource Manager)。 Security Center でアプリケーションのホワイトリストを構成するには、以下の手順に従います。

1. **[Security Center]** ダッシュボードを開きます。
2. 左側のウィンドウで、**[高度なクラウド防御]** の **[適応型アプリケーション制御]** を選択します。

    ![防衛](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)

**[適応型アプリケーション制御]** ページが表示されます。

![controls](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

**[Groups of VMs]\(VM のグループ\)** セクションには、次の 3 つのタブがあります。

* **[構成済み]**: アプリケーション制御で構成された VM が含まれているグループの一覧。
* **[推奨]**: アプリケーション制御が推奨されるグループの一覧。 Security Center では機械学習が使用され、VM が常に同じアプリケーションを実行しているかどうかに基づいて、アプリケーション制御の候補となる VM が特定されます。
* **[推奨なし]**: アプリケーション制御の推奨がない VM が含まれているグループの一覧。 たとえば、実行されるアプリケーションが常に変化していて、一定の状態にならない VM などです。

> [!NOTE]
> Security Center は、財産的価値のあるクラスタリング アルゴリズムを使用して VM のグループを作成します。これにより、同様の VM には、最適な推奨アプリケーション制御ポリシーが確実に適用されます。
>
>

### <a name="configure-a-new-application-control-policy"></a>新しいアプリケーション制御ポリシーの構成
1. アプリケーション制御の推奨があるグループの一覧を表示するには、**[推奨]** タブをクリックします。

  ![推奨](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

  一覧には次の項目が含まれています。

  - **[名前]**: サブスクリプションとグループの名前。
  - **[VM]**: グループ内の仮想マシンの数。
  - **[状態]**: 推奨の状態。ほとんどの場合はオープン
  - **[重大度]**: 推奨の重大度レベル

2. **[アプリケーションの制御に関する規則の作成]** オプションを開くには、グループを選択します。

  ![アプリケーション制御規則](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

3. **[VM の選択]** で、推奨されている VM の一覧を確認し、アプリケーション制御を適用しないすべての項目をオフにします。 今度は、次の 2 つの一覧が表示されます。

  - **[Recommended applications]\(推奨アプリケーション\)**: このグループ内の VM でよく使用される (Security Center によるアプリケーション制御規則に推奨される) アプリケーションの一覧。
  - **[その他のアプリケーション]**: このグループ内の VM での使用頻度が低いか、または "利用可能" (下記参照) として知られていることから、規則を適用する前に確認することが推奨されるアプリケーションの一覧。

4. それぞれの一覧のアプリケーションを確認し、適用したくないアプリケーションについてはチェック ボックスをオフにします。 各一覧には次の項目が含まれています。

  - **[名前]**: アプリケーションの証明書情報またはその完全アプリケーション パス
  - **[ファイルの種類]**: アプリケーションのファイルの種類。 これは、EXE、スクリプト、MSI のいずれかになります。
  - **[利用可能]**: 警告アイコンは、アプリケーションがアプリケーション ホワイトリストをバイパスするために攻撃者によって使用されうることを示します。 これらのアプリケーションは、承認前に確認することをお勧めします。
  - **[ユーザー]**: アプリケーションの実行を許可することが推奨されるユーザー

5. 選択を終了したら、**[作成]** を選択します。


> [!NOTE]
> - VM のグループごとにベースラインを作成し、固有の推奨事項を提示するために、Security Center では最低 2 週間分のデータが必要となります。 Security Center Standard レベルを初めてご利用になる場合、最初は一連の VM が *[推奨なし]* タブに表示されます。
> - Security Center からのアダプティブ アプリケーション制御では、GPO またはローカル セキュリティ ポリシーによって既に AppLocker ポリシーが有効になっている VM はサポートされていません。
> -  セキュリティのベスト プラクティスとして、Security Center ではホワイトリストに登録する必要があるアプリケーションの発行元規則の作成が常に試行されます。アプリケーションに発行元情報がない (つまり署名されていない) 場合にのみ、特定の EXE の完全パスに対するパス規則が作成されます。
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>アプリケーション制御で構成されたグループの編集および監視

1. アプリケーション制御で構成されたグループを編集および監視するには、**[適応型アプリケーション制御]** ページに戻り、**[Groups of VMs]\(VM のグループ\)** の **[構成済み]** を選択します。

  ![グループ](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

  一覧には次の項目が含まれています。

  - **[名前]**: サブスクリプションとグループの名前。
  - **[VM]**: グループ内の仮想マシンの数。
  - **[モード]**: 監査モードでは、ホワイトリストに登録されていないアプリケーションの実行が試行されると、ログに記録されます。強制では、ホワイトリストに登録されていないアプリケーションの実行が許可されません
  - **[問題]**: 現在のすべての違反

2. **[Edit application control policy]\(アプリケーション制御ポリシーの編集\)** ページで変更を行うには、グループを選択します。

  ![保護](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. **[保護モード]** では、次のいずれかを選択できます。

  - **[監査]**: このモードでは、アプリケーション制御ソリューションによって規則が強制されず、保護されている VM のアクティビティの監査だけが行われます。 このオプションをお勧めするのは、ターゲット VM でアプリの実行をブロックする前に、まず全体的な動作を観察したい場合です。
  - **[強制]**: このモードでは、アプリケーション制御ソリューションによって規則が強制され、実行を許可されていないアプリケーションがブロックされます。

  既に説明したように、既定では新しいアプリケーション制御ポリシーは常に "*監査*" モードで構成されます。 **[Policy extension]\(ポリシー拡張\)** で、ホワイトリストに登録する独自のアプリケーション パスを追加することができます。 これらのパスを追加すると、既に設定されている規則に加えて、これらのアプリケーション用の適切な規則が Security Center によって作成されます。

  **[最近の問題]** セクションには、現在のすべての違反の一覧が表示されます。

  ![発行](./media/security-center-adaptive-application/security-center-adaptive-application-fig7.png)

  たとえば、次のような種類です。
  - **[問題]**: ログに記録されたすべての違反。次のような違反があります。

      - **[ViolationsBlocked]**: ソリューションで強制モードが有効になっていて、ホワイトリストに登録されていないアプリケーションの実行が試行された場合。
      - **[ViolationsAudited]**: ソリューションで監査モードが有効になっていて、ホワイトリストに登録されていないアプリケーションが実行された場合。

 - **[VM の数]**: この種類の問題がある仮想マシンの数。

  各行をクリックすると、[[Azure の活動ログ]](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ページにリダイレクトされます。このページでは、この種類の違反があるすべての VM について情報を確認できます。 各行の末尾にある 3 つの点をクリックすると、その特定の項目を削除できます。 **[Configured virtual machines]\(構成済み仮想マシン\)** セクションには、これらの規則が適用される VM の一覧が表示されます。

  ![構成済み仮想マシン](./media/security-center-adaptive-application/security-center-adaptive-application-fig8.png)

  **[発行元のホワイトリスト登録に関する規則]** には、次の項目が表示されます。

  - **[規則]**: 各アプリケーションで検出された証明書情報に基づいて作成された発行元規則の対象となるアプリケーション
  - **[ファイルの種類]**: 特定の発行元規則によってカバーされるファイルの種類。 これは、EXE、スクリプト、MSI のいずれかになります。
  - **[ユーザー]**: 各アプリケーションの実行が許可されたユーザーの数

  詳細については、[AppLocker の発行元規則の概要](https://docs.microsoft.com/windows/device-security/applocker/understanding-the-publisher-rule-condition-in-applocker)に関するページを参照してください。

  ![ホワイトリスト登録に関する規則](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

  各行の末尾にある 3 つの点をクリックすると、その特定の規則を削除したり、許可されたユーザーを編集したりすることができます。

  **[パスのホワイトリスト登録に関する規則]** セクションには、デジタル証明書で署名されていないものの、まだホワイトリスト登録に関する規則で許可されている完全なアプリケーション パス (特定のファイルの種類を含む) の一覧が表示されます。

  > [!NOTE]
  > セキュリティのベスト プラクティスとして、Security Center ではホワイトリストに登録する必要がある EXE の発行元規則の作成が既定で常に試行されます。EXE に発行元情報がない (つまり署名されていない) 場合にのみ、特定の EXE の完全パスに対するパス規則が作成されます。

  ![パスのホワイトリスト登録に関する規則](./media/security-center-adaptive-application/security-center-adaptive-application-fig10.png)

  一覧には、以下が含まれています。
  - **[名前]**: 実行可能ファイルの完全パス
  - **[ファイルの種類]**: 特定のパス規則によってカバーされるファイルの種類。 これは、EXE、スクリプト、MSI のいずれかになります。
  - **[ユーザー]**: 各アプリケーションの実行が許可されたユーザーの数

  各行の末尾にある 3 つの点をクリックすると、その特定の規則を削除したり、許可されたユーザーを編集したりすることができます。

4. **[適応型アプリケーション制御]** ページで変更を加えた後、**[保存]** ボタンをクリックします。 変更を適用しない場合は、**[破棄]** をクリックします。

### <a name="not-recommended-list"></a>非推奨の一覧

Security Center では、安定した一連のアプリケーションが実行されている仮想マシンについてのみ、アプリケーション ホワイトリストに登録することを推奨しています。 関連する VM 上のアプリケーションが常に異なる場合、推奨は作成されません。

![推奨](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

一覧には、以下が含まれています。
- **[名前]**: サブスクリプションとグループの名前。
- **[VM]**: グループ内の仮想マシンの数。

## <a name="next-steps"></a>次の手順
このドキュメントでは、Azure VM で実行されるアプリケーションのホワイトリストを作成するために、Azure Security Center でアダプティブ アプリケーション制御を使用する方法について説明しました。 Azure セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティの警告の管理と対応](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 Security Center でアラートを管理し、セキュリティ インシデントに対応する方法を説明します。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center のセキュリティ アラートの概要](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 さまざまな種類のセキュリティ アラートについて説明します。
* [Azure Security Center トラブルシューティング ガイド](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 Security Center における一般的な問題のトラブルシューティング方法について説明します。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)。 このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
