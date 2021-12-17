---
title: Azure Germany IoT サービス | Microsoft Docs
description: この記事は、Azure Germany 向け Azure IoT Suite の出発点となります。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 16259a816413bcf7f57475b12f46c9410cb3ca44
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/26/2021
ms.locfileid: "117029222"
---
# <a name="azure-germany-iot-services"></a>Azure Germany IoT サービス

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="iot-solution-accelerators"></a>IoT ソリューション アクセラレータ
Azure IoT Suite に必要なサービスはすべて、Azure Germany で利用できます。 

### <a name="variations"></a>バリエーション
Azure Germany での Azure IoT Suite のホーム ページは、グローバル Azure のページとは異なります。

## <a name="solution-accelerators"></a>ソリューション アクセラレータ
次のソリューション アクセラレータのいずれかを使って開始する必要がある場合があります。 

### <a name="remote-monitoring"></a>リモート監視
リモート監視のソリューション アクセラレータは、リモートの場所で実行されている複数のコンピューターを対象としたエンドツーエンドの監視ソリューションの実装です。 このソリューションは、主要な Azure サービスを組み合わせることで、ビジネス シナリオの汎用的な実装を実現したものです。 このソリューションを独自の実装の開始点として使用し、固有のビジネス要件を満たすようにカスタマイズすることができます。

### <a name="predictive-maintenance"></a>予測メンテナンス
予測メンテナンスのソリューション アクセラレータは、障害が発生する可能性があるポイントを予測するビジネス シナリオに対応したエンド ツー エンド ソリューションです。 このソリューションを使用すると、メンテナンスの最適化などのアクティビティを先を見越して実行できます。 このソリューションは、Azure IoT Hub、Stream Analytics、Machine Learning ワークスペースなどの主要な Azure IoT Suite サービスを組み合わせたものです。 このワークスペースには、公開されているサンプル データ セットに基づいて航空機エンジンの残存耐用年数 (RUL) を予測するモデルが含まれています。 このソリューションでは、固有のビジネス要件を満たすソリューションを計画および実装するための開始点として使用できる、IoT ビジネス シナリオが完全に実装されています。


## <a name="deploying-the-solution-accelerator"></a>ソリューション アクセラレータのデプロイ

ソリューションはどちらも、Web サイト経由または PowerShell 経由の 2 つの方法でデプロイできます。

### <a name="deploy-via-website"></a>Web サイト経由のデプロイ

前に述べたホーム ページを使用して、[構成済みのソリューションについてのチュートリアル](/previous-versions/azure/iot-accelerators/about-iot-accelerators)の手順に従います。

### <a name="deploy-via-powershell"></a>PowerShell 経由でのデプロイ

*リモート監視* のソリューション用に完全なバージョン (Azure Resource Manager テンプレートと Visual Studio を使用) があります。 [GitHub の Azure-IoT-Remote-Monitoring リポジトリ](https://github.com/Azure/azure-iot-remote-monitoring)からダウンロードします。 PowerShell のデプロイは、Azure Germany などの他の環境向けの準備ができています。 *環境* パラメーターに "AzureGermanCloud" を指定するため、次のようになります。

```powershell
build.cmd cloud debug AzureGermanCloud
```

現在、Bing Maps を Azure Germany で使用することはできないため、自動的にサブスクライブすることはできません。 グローバル Azure のサービスをサブスクライブし、そこでサービスを使用することで、この問題を解決できます。 

> [!NOTE]
> ここで説明された方法で Bing Maps を使用すると、Azure Germany 環境から移動します。

これを行うには、次の手順を行います。

1. **[+ 新規]** をクリックし、**Bing Maps API for Enterprise** を検索して、画面の指示に従うことで、グローバル Azure portal で Bing Maps API を作成します。
2. グローバル Azure portal から Bing Maps API for Enterprise キーを取得します。 
    1. グローバル Azure portal で、Bing Maps API for Enterprise が含まれるリソース グループを参照します。
    2. **[すべての設定]**  >  **[キーの管理]** の順にクリックします。 
    3. MasterKey と QueryKey という 2 つのキーが表示されます。 QueryKey の値をコピーします。
3. [GitHub の Azure-IoT-Remote-Monitoring リポジトリ](https://github.com/Azure/azure-iot-remote-monitoring)から最新のコードをプルダウンします。
4. `/docs/` リポジトリ フォルダーにあるコマンドライン デプロイ ガイダンスに従って、ご利用の環境でクラウドのデプロイを実行します。 
5. デプロイを実行したら、ルート フォルダーでデプロイ時に作成された **.user.config** ファイルを探します。 このファイルをテキスト エディターで開きます。 
6. QueryKey 用にコピーした値を含めて、次の行を変更します。`<setting name="MapApiQueryKey" value="" />`
7. 手順 4 を繰り返して、ソリューションを再デプロイします。
 


## <a name="next-steps"></a>次のステップ
補足情報と更新情報については、[Azure Germany のブログ](/archive/blogs/azuregermany/)を参照してください。