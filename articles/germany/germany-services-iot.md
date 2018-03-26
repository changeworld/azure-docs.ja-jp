---
title: "Azure のドイツ IoT サービス |Microsoft ドキュメント"
description: "Azure ドイツの IoT Suite の開始点"
services: germany
cloud: na
documentationcenter: na
author: gitralf
manager: rainerst
ms.assetid: na
ms.service: germany
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2017
ms.author: ralfwi
ms.openlocfilehash: c15c084592b57b074e339cad7ac8c3197375542d
ms.sourcegitcommit: 2c2dfe04a0af718cf058445467fa407ead5f45a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2018
---
# <a name="azure-germany-iot-services"></a>Azure のドイツ IoT サービス

## <a name="iot-suite"></a>IoT Suite
Azure IoT Suite 用のすべての必要なサービス Azure ドイツで利用できます。 

### <a name="variations"></a>バリエーション
Azure IoT Suite Azure ドイツのホーム ページは、グローバル Azure 内のページと異なります。 使用して[http://www.azureiotsuite.de](http://www.azureiotsuite.de)し、指示に従います。 

## <a name="preconfigured-solutions"></a>構成済みのソリューション
次の構成済みソリューションのいずれかで開始する可能性があります。 

### <a name="remote-monitoring"></a>リモート監視
リモートの監視構成済みのソリューションの IoT Suite は、リモートの場所で実行されている複数のコンピューター用のエンド ツー エンド監視ソリューションの実装です。 このソリューションは、主要な Azure サービスを組み合わせることで、ビジネス シナリオの汎用的な実装を実現したものです。 独自の実装の開始点として、ソリューションを使用しを特定のビジネス要件を満たすようにカスタマイズできます。

### <a name="predictive-maintenance"></a>予測的なメンテナンス
IoT Suite の予測的なメンテナンスの構成済みソリューションは、障害が発生する可能性があるポイントを予測するビジネス シナリオに対応したエンド ツー エンド ソリューションです。 この構成済みソリューションを使用すると、メンテナンスの最適化などのアクティビティを先手を打って実行できます。 ソリューションでは、Azure IoT Hub、Stream Analytics では、Machine Learning ワークスペースなど、主要な Azure IoT Suite サービスを結合します。 このワークスペースには、公開されているサンプル データ セットに基づいて航空機エンジンの残存耐用年数 (RUL) を予測するモデルが含まれています。 ソリューションは、完全を計画して、特定のビジネス要件を満たすソリューションを実装するための出発点として IoT ビジネス シナリオを実装します。


## <a name="deploying-the-preconfigured-solutions"></a>構成済みのソリューションの配置

どちらのソリューションは、web サイトを使用して、または PowerShell を使用して、2 つの方法で展開できます。

### <a name="deploy-via-website"></a>Web サイトでの展開します。

指示に従って、[構成済みのソリューションのためのチュートリアル](../iot-suite/iot-suite-getstarted-preconfigured-solutions.md)前述のホーム ページを使用しています。

### <a name="deploy-via-powershell"></a>PowerShell を使用して展開します。

完全なバージョン (Azure リソース マネージャーのテンプレートと Visual Studio を使用) がある、*リモート監視*ソリューションです。 ダウンロード、 [GitHub のリポジトリを Azure IoT-リモートの監視](https://github.com/Azure/azure-iot-remote-monitoring)です。 PowerShell 配置 Azure ドイツのようなその他の環境の準備ができました。 提供、*環境*パラメーター"AzureGermanCloud、"次のように表示されます。

    build.cmd cloud debug AzureGermanCloud

Bing Maps は現在 Azure ドイツでは使用できませんし、したがって、サブスクライブできません自動的にします。 グローバル Azure サービスにサブスクライブして、あるサービスを使用して、この問題を解決できます。 

> [!NOTE]
> Bing マップする方法が説明されている、ここで使用する、ときに、Azure ドイツ環境がままにします。

これを行う方法を次に示します。

1. グローバル Azure ポータルでをクリックして、Bing Maps API を作成**+ 新規**の検索、 **Bing Maps API for Enterprise**、画面の指示に従います。
2. グローバル Azure ポータルからエンタープライズ キーの Bing Maps API を取得します。 
    1. グローバル Azure ポータルに、Bing Maps API for Enterprise がある場合、リソース グループを参照します。
    2. をクリックして**すべて設定** > **キー管理**です。 
    3. 2 つのキーを参照してください: MasterKey と QueryKey です。 QueryKey の値をコピーします。
3. 最新のコードのプルダウン、 [GitHub のリポジトリを Azure IoT-リモートの監視](https://github.com/Azure/azure-iot-remote-monitoring)です。
4. 環境内でコマンドライン配置のガイダンスに従って、クラウドの展開の実行、`/docs/`リポジトリ フォルダーです。 
5. 展開を実行した後、ルート フォルダーで検索、 **. user.config**配置時に作成されたファイルです。 このファイルをテキスト エディターで開きます。 
6. QueryKey をコピーした値を含めるには、次の行を変更します。 `<setting name="MapApiQueryKey" value="" />`
7. 手順 4. を繰り返して、ソリューションを再展開します。
 


## <a name="next-steps"></a>次の手順
補足情報と更新プログラムは、サブスクライブ、 [Azure ドイツ ブログ](https://blogs.msdn.microsoft.com/azuregermany/)です。
