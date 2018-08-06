---
title: 'Azure Security Center チュートリアル: セキュリティ ポリシーの定義と評価 | Microsoft Docs'
description: 'Azure Security Center チュートリアル: セキュリティ ポリシーの定義と評価'
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: terrylan
ms.openlocfilehash: 15c69bce87ede96eb3a7bc0bada4e4f6a6669abb
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358610"
---
# <a name="tutorial-define-and-assess-security-policies"></a>チュートリアル: セキュリティ ポリシーの定義と評価
Security Center は、ワークロードの望ましい構成を定義するセキュリティ ポリシーを使用して、会社や規制のセキュリティ要件に確実に準拠できるようにします。 Azure サブスクリプションでポリシーを定義し、それらをワークロードの種類またはデータの機密度に合わせて調整すると、計算、アプリケーション、ネットワーク、データとストレージ、ID とアクセスの各リソースについてのセキュリティ推奨事項を Security Center から得ることができます。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * セキュリティ ポリシーを構成する
> * リソースのセキュリティを評価する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)を作成してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルで説明されている機能を実行するには、Security Center の Standard 価格レベルを使用する必要があります。 Security Center Standard は最初の 60 日間、無料でお試しいただけます。 Standard にアップグレードする方法については、[Azure サブスクリプションでの Security Center Standard の利用開始](security-center-get-started.md)に関するクイックスタートを参照してください。

## <a name="configure-security-policy"></a>セキュリティ ポリシーを構成する
Security Center では、Azure サブスクリプションごとに自動で既定のセキュリティ ポリシーが作成されます。 セキュリティ ポリシーは、そのサブスクリプションのセキュリティ要件に従ってオンまたはオフにできる推奨事項で構成されます。 既定のセキュリティ ポリシーを変更するには、そのサブスクリプションの所有者、共同作成者、またはセキュリティ管理者である必要があります。

1. Security Center のメイン メニューで、**[セキュリティ ポリシー]** を選択します。
2. 使用するサブスクリプションを選択します。

  ![セキュリティ ポリシー](./media/tutorial-security-policy/tutorial-security-policy-fig1.png)  

3. **[ポリシー コンポーネント]** で、**[セキュリティ ポリシー]** を選択します。
4. 監視したい各セキュリティ構成について、**[オン]** を選択します。 Security Center は環境の構成を継続的に評価し、脆弱性が存在する場合にセキュリティの推奨事項を生成します。 セキュリティ構成が非推奨の場合や適切でない場合は、**[オフ]** を選択してください。 たとえば、開発/テスト環境では、運用環境と同じレベルのセキュリティが必要でない場合があります。 環境に適用できるポリシーを選択した後、**[保存]** をクリックします。

  ![セキュリティ構成](./media/tutorial-security-policy/tutorial-security-policy-fig6.png)  

Security Center がこれらのポリシーを処理し、推奨事項を生成するまで待機します。 システムの更新や OS 構成など、一部の構成は最大で 12 時間かかる可能性がありますが、ネットワーク セキュリティ グループと暗号化の構成はほぼ瞬時に評価できます。 Security Center ダッシュボードに推奨事項が表示されたら、次の手順に進むことができます。

## <a name="assess-security-of-resources"></a>リソースのセキュリティを評価する
1. 有効にされたセキュリティ ポリシーに従って、Security Center は必要に応じて一連のセキュリティ推奨事項を提供します。 最初に、仮想マシンとコンピューターの推奨事項を確認することをお勧めします。 Security Center ダッシュボードで、**[概要]**、**[計算とアプリ]** の順にクリックします。

  ![コンピューティング](./media/tutorial-security-policy/tutorial-security-policy-fig2.png)

  赤色の (優先度の高い) 推奨事項を優先して、それぞれの推奨事項を確認します。 これらの推奨事項には、Security Center から直接解決策を実装できるものもあります ([エンドポイント保護の問題](https://docs.microsoft.com/azure/security-center/security-center-install-endpoint-protection)など)。 その他の推奨事項では、解決策を適用するガイドラインのみが示されます (ディスクの暗号化がないことに関する推奨事項など)。

2. 関連するコンピューティングの推奨事項にすべて対応したら、次のワークロード (ネットワーク) に移ります。 Security Center ダッシュボードで、**[概要]**、**[ネットワーク]** の順にクリックします。

  ![ネットワーク](./media/tutorial-security-policy/tutorial-security-policy-fig3.png)

  ネットワークの推奨事項のページには、ネットワーク構成、インターネット接続エンドポイント、ネットワーク トポロジに関するセキュリティの問題の一覧があります。 **[計算とアプリ]** の場合と同様に、ネットワークの推奨事項には統合された解決策を示すものと示さないものがあります。

3. 関連するネットワークの推奨事項にすべて対応したら、次のワークロード (ストレージおよびデータ) に移ります。 Security Center ダッシュボードで、**[概要]**、**[データ & ストレージ]** の順にクリックします。

  ![データ リソース](./media/tutorial-security-policy/tutorial-security-policy-fig4.png)

  **[データ リソース]** ページには、Azure SQL サーバーおよびデータベースの監査、SQL データベースの暗号化、Azure ストレージ アカウントの暗号化の有効化に関する推奨事項が表示されます。 これらのワークロードがない場合、推奨事項は表示されません。 **[計算とアプリ]** の場合と同様に、データとストレージの推奨事項には統合された解決策を示すものと示さないものがあります。

4. 関連するデータとストレージの推奨事項にすべて対応したら、次のワークロード (ID およびアクセス) に移ります。 Security Center ダッシュボードで、**[概要]**、**[ID およびアクセス]** の順にクリックします。

  ![ID およびアクセス](./media/tutorial-security-policy/tutorial-security-policy-fig5.png)

  **[ID およびアクセス]** ページには、次のような推奨事項が表示されます。

   - サブスクリプションで特権アカウントの MFA を有効にする
   - 書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する
   - 外部の特権アカウントをサブスクリプションから削除する

## <a name="clean-up-resources"></a>リソースのクリーンアップ
このコレクションの他のクイックスタートとチュートリアルは、このクイックスタートに基づいています。 引き続き次のクイックスタートとチュートリアルを行う予定の場合、Standard レベルの実行を継続して、自動プロビジョニングを有効のままにしてください。 続行しないまたは Free レベルに戻したい場合:

1. Security Center のメイン メニューに戻り、**[セキュリティ ポリシー]** を選択します。
2. Free に戻したいサブスクリプションまたはポリシーを選択します。 **[セキュリティ ポリシー]** が開きます。
3. **[ポリシー コンポーネント]** で、**[価格レベル]** を選択します。
4. **[Free]** を選択して、Standard レベルから Free レベルにサブスクリプションを変更します。
5. **[保存]** を選択します。

自動プロビジョニングを無効にする場合:

1. Security Center のメイン メニューに戻り、**[セキュリティ ポリシー]** を選択します。
2. 自動プロビジョニングを無効にするサブスクリプションを選択します。
3. **[セキュリティ ポリシー - データ収集]** で、**[オンボード]** の **[オフ]** を選択して、自動プロビジョニングを無効にします。
4. **[保存]** を選択します。

>[!NOTE]
> 自動プロビジョニングを無効しても、Microsoft Monitoring Agent がプロビジョニングされている Azure VM からエージェントは削除されません。 自動プロビジョニングを無効にすると、リソースのセキュリティの監視が制限されます。
>

## <a name="next-steps"></a>次の手順
このチュートリアルでは、Security Center での基本的なポリシー定義とワークロードのセキュリティ評価について説明しました。例:

> [!div class="checklist"]
> * 会社や規制のセキュリティ要件に確実に準拠できるようにするセキュリティ ポリシーの構成
> * コンピューティング、ネットワーク、SQL およびストレージ、アプリケーションの各リソースに関するセキュリティ評価

次のチュートリアルに進み、Security Center を使用してリソースを保護する方法について学習してください。

> [!div class="nextstepaction"]
> [リソースの保護](tutorial-protect-resources.md)
