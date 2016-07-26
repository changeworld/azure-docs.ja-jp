<properties
   pageTitle="Azure セキュリティ センターでのセキュリティの警告の管理と対応 | Microsoft Azure"
   description="このドキュメントは、Azure セキュリティ センターの機能を使用してセキュリティの警告の管理と対応することに役立ちます。"
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/19/2016"
   ms.author="yurid"/>

# Azure セキュリティ センターでのセキュリティの警告の管理と対応
このドキュメントは、Azure セキュリティ センターの機能を使用してセキュリティの警告の管理と対応することに役立ちます。

> [AZURE.NOTE] このドキュメントの情報は、Azure セキュリティ センターのプレビュー リリースに適用されます。

## セキュリティの警告とは何か
Security Center は、真の脅威を検出し、偽陽性を減らすために、Azure のリソースやネットワークのほか、接続されているパートナー ソリューション (ファイアウォールやエンドポイント保護ソリューションなど) から、自動的にログ データを収集、分析、統合します。Security Center には、優先順位の付いたセキュリティの警告の一覧が表示されます。また、すぐに問題を調査する必要がある情報や、攻撃を受けたものを修復する方法についての推奨事項も表示されます。

> [AZURE.NOTE] Security Center の検出機能に関する詳細については、「[Azure Security Center の検出機能](security-center-detection-capabilities.md)」を参照してください。

Microsoft のセキュリティの研究者は、コンシューマー向け製品、エンタープライズ向け製品、およびオンライン サービスで発見された新しい攻撃パターンや傾向を含め、世界中で新たに生じる脅威を分析し続けています。その結果、セキュリティ センターは、新しい脆弱性とエクスプロイトが発見されたときに検出アルゴリズムを更新できます。これにより、お客様は進化する脅威に対応し続けることができます。たとえば、セキュリティ センターは次の種類の脅威を検出できます。

- **ネットワーク データに対するブルート フォース攻撃の検出**: アプリケーションの典型的なネットワーク トラフィック パターンを理解する機械学習モデルを使用します。正当なユーザーではない、悪意のあるアクターによって実行されているアクセス試行をより効果的に検出できるようになります。
- **エンドポイント データに対するブルート フォース攻撃の検出**: コンピューターのログの分析に基づいて検出されます。これにより、失敗した試行と成功した試行を区別できるようになります。
- **悪意のある IP アドレスと通信している VM**: ネットワーク トラフィックを Microsoft のグローバル脅威情報と比較し、セキュリティが侵害され、コマンド アンド コントロール (C & C) サーバーと通信しているコンピューター (またはその反対) を発見します。
- **セキュリティが侵害された VM**: コンピューターのログと他の信号との相関関係の動作分析に基づいて、コンピューターのセキュリティ侵害とエクスプロイトが原因である可能性が高い異常なイベントを特定します。

## セキュリティの警告の管理

現在の警告は、**[セキュリティの警告]** タイルで確認できます。各警告の詳細については以下の手順に従います。

1. [セキュリティ センター] ダッシュボードには **[セキュリティの警告]** タイルが表示されます。

    ![Azure セキュリティ センターの [セキュリティの警告] タイル](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-new.png)

2.  タイルをクリックすると、次のように、警告についての詳細が示される **[セキュリティの警告]** ブレードが開きます。

    ![Azure セキュリティ センターの [セキュリティの警告] ブレード](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-new.png)

このブレードの下部に、各警告の詳細が表示されます。警告を並べ替えるには、並べ替えに使用する列をクリックします。各列の定義を以下に示します。

- **[アラート]**: 警告の短い説明。
- **[カウント]**: 特定の日に検出された、特定の種類の全警告の一覧。
- **[検出元]**: 警告をトリガーしたサービス。
- **[日付]**: イベントが発生した日付。
- **[状態]**: その警告の現在の状態。ここに表示される状態は 2 種類です。
    - **[アクティブ]**: セキュリティの警告が検出されました。
    - **[破棄済み]**: セキュリティの警告はユーザーによって破棄されました。通常、この状態は、調査済みであり、軽減されたか、実際の攻撃とは見なされなかった警告に使用されます。

- **[重要度]**: 重大度。高、中、低で示します。

日付、状態と重要度に基づいて警告をフィルター処理することができます。警告のフィルター処理は、セキュリティの警告の表示範囲を限定する必要がある場合に便利です。たとえば、システム内の潜在的な違反を調査するために、過去 24 時間以内に発生したセキュリティの警告を確認することができます。

1. **[セキュリティの警告]** ブレードの **[フィルター]** をクリックします。**[フィルター]** ブレードが開いたら、確認する日付、状態、重要度の値を選択します。

	![Azure セキュリティ センターの警告のフィルター](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-new.png)

2. 	セキュリティの警告の調査後に、警告がご自分の環境に当てはまらない誤検出であることや、特定のリソースで期待される動作であることがわかる場合があります。どの場合でも、セキュリティの警告が当てはまらないと判断した場合、その警告を無視し、ビューから除外することができます。セキュリティの警告を無視するには 2 つの方法があります。警告を右クリックして **[無視]** を選択するか、項目の上にマウスを合わせ、右側に表示されている 3 つの点をクリックして、**[無視]** を選択します。**[フィルター]** をクリックして **[無視]** を選択すると、無視したセキュリティの警告を表示できます。

	![Azure セキュリティ センターの警告のフィルター](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-new.png)

### セキュリティの警告への対応
セキュリティの警告を選択して、警告を発生させたイベントの詳細を確認します。必要に応じて、攻撃を受けたものを修復するために必要な手順を確認します。セキュリティの警告は種類と日付別に分類されています。セキュリティの警告をクリックすると、分類された警告の一覧が示されたブレードが開きます。

![Azure セキュリティ センターのセキュリティの警告への対応](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig7.png)

この場合、トリガーされた警告が、リモート デスクトップ プロトコル (RDP) の疑わしいアクティビティを参照します。1 列目には、攻撃を受けたリソースが表示されます。2 列目には、攻撃が検出された時刻が表示されます。3 列目には、警告の状態が表示されます。4 列目には、攻撃の重大度が表示されます。この情報を確認し、攻撃を受けたリソースをクリックすると、新しいブレードが開きます。

![Azure セキュリティ センターのセキュリティの警告に対処する方法の推奨事項](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig8-1.png)

このブレードの **[アラート]** フィールドで、このイベントについての詳しい情報を確認できます。詳しい情報を確認することで、セキュリティの警告を引き起こした原因、ターゲット リソース、(場合によっては) 発生元の IP アドレス、解決のための推奨事項を知ることができます。Windows セキュリティ イベント ログによっては IP アドレスが含まれていないため、発生元の IP アドレスは空 (利用不可) である場合もあります。

> [AZURE.NOTE] セキュリティ センターから提案される修復方法は、セキュリティの警告によって変わります。場合によっては、他の Azure 機能を使用して推奨される修復方法を実行する必要があります。このような攻撃に対する修復の例として、[ネットワーク ACL](../virtual-network/virtual-networks-acl.md) または[ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md) ルールを使用してこの攻撃の発信元の IP アドレスをブラックリストに指定することが挙げられます。

## 種類別のセキュリティの警告
RDP の疑わしいアクティビティの警告にアクセスするのと同じ手順で、他の種類の警告にもアクセスできます。Security Center の警告として表示される他の警告の例は、次のとおりです。

**SQL インジェクションの可能性** SQL インジェクションとは、後で SQL Server のインスタンスに渡して解析と実行の対象とする文字列に、悪意のあるコードが挿入される攻撃です。SQL ステートメントを構成するすべてのプロシージャにおいて、挿入に対する脆弱性を確認する必要があります。SQL Server は、受け取った有効な構文のクエリをすべて実行してしまうからです。

![SQL injection alert](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig9.png)

この警告では、攻撃を受けたリソース、検出の日時、攻撃の状態を特定する情報が表示され、さらに詳しい調査手順へのリンクも表示されます。

**疑わしい送信トラフィックの検出**

ネットワーク デバイスは、他の種類のシステムとほぼ同じように検出、プロファイリングされることがあります。通常、攻撃者は初めにポート スキャンまたはポート スイープを行います。次の例では、VM からの疑わしい SSH トラフィックが存在しており、外部リソースに対する SSH ブルート フォース攻撃またはポート スイープ攻撃が行われている可能性があります。

![Suspicious outgoing traffic alert](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig-10.png)

この警告では、攻撃の開始に使用されたリソース、セキュリティが侵害されたコンピューター、検出の日時、使用されたプロトコルとポートを特定できる情報が表示されます。このブレードでは、問題を軽減するために使用できる修復手順の一覧も表示されます。

**悪意のあるコンピューターとのネットワーク通信**
 
Azure Security Center は、悪意のある IP アドレス (多くの場合、コマンド アンド コントロール センター) と通信している、セキュリティが侵害されたコンピューターを、Microsoft 脅威インテリジェンス フィードを利用して検出できます。この例では、Azure Security Center によって、Pony Loader マルウェア ([Fareit](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=PWS:Win32/Fareit.AF) とも呼ばれる) を使用した通信が行われたことが検出されました。

![Network communication with a malicious machine alert](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig-11.png)

この警告では、この攻撃の開始に使用されたリソース、攻撃されたリソース、被害を受けた IP、攻撃者の IP、検出の日時を特定できる情報が表示されます。

> [AZURE.NOTE] ライブ IP アドレスは、プライバシー保護の目的でこのスクリーンショットからは削除しています。


## 関連項目

このドキュメントでは、セキュリティ センターでのセキュリティ ポリシーの構成方法について説明しました。セキュリティ センターの詳細については、次を参照してください。

- [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md)
- [Azure セキュリティ センターでのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)
- 「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」-- Azure リソースの正常性を監視する方法について説明しています。
- 「[Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md)」-- パートナー ソリューションの正常性状態を監視する方法について説明しています。
- 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」-- このサービスの使用に関してよく寄せられる質問が記載されています。
- [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

<!---HONumber=AcomDC_0720_2016-->