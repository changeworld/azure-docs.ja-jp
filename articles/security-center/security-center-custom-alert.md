---
title: Azure Security Center のカスタム アラート ルール | Microsoft Docs
description: このドキュメントは、Azure Security Center でカスタム アラート ルールを作成する際に役立ちます。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: f335d8c4-0234-4304-b386-6f1ecda07833
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: terrylan
ms.openlocfilehash: 326b07a18b07f15850e0283ad51a05b42c1ef0ab
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341315"
---
# <a name="custom-alert-rules-in-azure-security-center-preview"></a>Azure Security Center のカスタム アラート ルール (プレビュー)
このドキュメントは、Azure Security Center でカスタム アラート ルールを作成する際に役立ちます。

## <a name="what-are-custom-alert-rules-in-security-center"></a>Security Center のカスタム アラート ルールとは

Security Center には、事前定義された一連の[セキュリティ アラート](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)が用意されています。これらは、脅威または疑わしいアクティビティが発生した際にトリガーされます。 一部のシナリオでは、環境の特定のニーズに対応するために、カスタム アラートの作成が必要になる場合があります。

Security Center のカスタム アラート ルールを使用すると、環境から既に収集されているデータに基づいて、新しいセキュリティ アラートを定義できます。 クエリを作成し、これらのクエリの結果をカスタム ルールの条件として使用できます。ルールは、この条件が満たされたときに実行されます。 カスタム クエリの作成には、コンピューターのセキュリティ イベント、パートナーのセキュリティ ソリューション ログ、または API を使用して取り込まれたデータを使用できます。

> [!NOTE]
> カスタム アラートは、Security Center の[調査機能](security-center-investigation.md)ではサポートされていません。
>
>

## <a name="how-to-create-a-custom-alert-rule-in-security-center"></a>Security Center でカスタム アラート ルールを作成する方法

**[セキュリティ センター]** ダッシュボードを開き、次の手順に従ってカスタム アラート ルールを作成します。

1.  左側のウィンドウの **[検出]** で、**[Custom alert rules (Preview)]\(カスタム アラート ルール (プレビュー)\)** をクリックします。
2.  **[Security Center – Custom alert rules (Preview)]\(Security Center – カスタム アラート ルール (プレビュー)\)** ページで、**[新しいカスタム アラート ルール]** をクリックします。

    ![カスタム アラート](./media/security-center-custom-alert/security-center-custom-alert-fig1.png)

3.  次のオプションを含む [カスタムのアラート ルールの作成] ページが表示されます。

    ![[作成]](./media/security-center-custom-alert/security-center-custom-alert-fig2.png)

4.  **[名前]** フィールドに、このカスタム ルールの名前を入力します。
5.  **[説明]** フィールドに、このルールの目的を示す簡単な説明を入力します。
6.  **[重大度]** フィールドで、必要性に応じた重大度レベル (高、中、低) を選択します。
7.  **[サブスクリプション]** フィールドで、このルールを適用できるサブスクリプションを選択します。
8.  **[ワークスペース]** フィールドで、このルールで監視するワークスペースを選択し、**[検索クエリ]** フィールドに、結果の取得に使用するクエリを入力します。

    > [!NOTE]
    > カスタム アラートの格納先として選択するワークスペースに対する書き込みアクセス許可が必要です。
    >
    >

    このクエリの結果によってアラートがトリガーされます。 有効なクエリを入力すると、このフィールドの右隅に緑色のチェック マークが表示されることに注意してください。

    ![クエリ](./media/security-center-custom-alert/security-center-custom-alert-fig3.png)

10. **[期間]** フィールドで、上記のクエリが実行される期間を選択します。 選択する期間に応じて、このフィールドの下部にある検索結果が変わることに注意してください。

    ![期間](./media/security-center-custom-alert/security-center-custom-alert-fig4.png)

11. **[評価]** フィールドで、このルールを評価して実行する頻度を選択します。
12. **[結果の数]** フィールドで、演算子を選択します ([次の値より大きい] または [次の値より小さい])。
13. **[しきい値]** フィールドに、先ほど選択した演算子の参照として使用される数を入力します。
14. このルールの次のアラートが Security Center によって送信されるまでの待ち時間を設定する場合は、**[アラートを表示しない]** オプションを有効にします。
15. **[OK]** をクリックして終了します。

新しいアラート ルールの作成が完了すると、カスタム アラート ルールの一覧にそれが表示されます。 そのルールの条件が満たされると、新しいアラートがトリガーされます。これは **[セキュリティ アラート]** ダッシュボードで確認できます。

![アラート:](./media/security-center-custom-alert/security-center-custom-alert-fig5.png)

ルールの作成時に指定したパラメーター (検索クエリやしきい値など) がこのカスタム ルールのアラートに表示されることに注意してください。

## <a name="see-also"></a>関連項目
このドキュメントでは、Azure Security Center でカスタム アラート ルールを作成する方法について説明しました。 Azure セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティの警告の管理と対応](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 Security Center でアラートを管理し、セキュリティ インシデントに対応する方法を説明します。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center のセキュリティ アラートの概要](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 さまざまな種類のセキュリティ アラートについて説明します。
* [Azure Security Center トラブルシューティング ガイド](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 Security Center における一般的な問題のトラブルシューティング方法について説明します。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)。 このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
