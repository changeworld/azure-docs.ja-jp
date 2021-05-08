---
title: Azure Spring Cloud でのアプリとデプロイ
description: このトピックでは、Azure Spring Cloud でのアプリケーションとデプロイの違いについて説明します。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 5473daedc8a7ad5a3b6ddffc65234160d4b3019d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104877626"
---
# <a name="app-and-deployment-in-azure-spring-cloud"></a>Azure Spring Cloud でのアプリとデプロイ

**この記事の適用対象:** ✔️ Java ✔️ C#

**アプリ** と **デプロイ** は、Azure Spring Cloud のリソース モデルにおける 2 つの重要な概念です。 Azure Spring Cloud では、*アプリ* は、1 つのビジネス アプリまたは 1 つのマイクロサービスを抽象化したものです。  *アプリ* としてデプロイされたコードまたはバイナリの 1 つのバージョンが *デプロイ* で実行されます。  アプリは、次に示すように、"*Azure Spring Cloud サービス インスタンス*" (単に "*サービス インスタンス*" と呼ばれることもあります) で実行されます。

 ![アプリとデプロイ](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

1 つの Azure サブスクリプション内に複数のサービス インスタンスを含めることができますが、ビジネス アプリまたはマイクロサービスを構成するすべてのアプリが 1 つのサービス インスタンス内にある場合は Azure Spring Cloud サービスを使用するのが最も簡単です。

Azure Spring Cloud の Standard レベルでは、1 つのアプリに 1 つの運用環境デプロイと 1 つのステージング デプロイが割り当てられ、簡単にブルー/グリーン デプロイを行うことができます。

## <a name="app"></a>アプリ
以下の機能とプロパティは、アプリ レベルで定義されています。

| 特徴 | 説明 |
|:--|:----------------|
| パブリック</br>エンドポイント | アプリにアクセスする URL |
| Custom</br>Domain | カスタム ドメインをセキュリティで保護する CNAME レコード |
| サービス</br>バインド | その他の Azure サービスとの既定の接続 |
| マネージド</br>ID | アプリで Azure Active Directory によるマネージド ID を使用すると、他の Azure AD で保護されたリソース (Azure Key Vault など) に簡単にアクセスできます |
| 永続的</br>ストレージ | アプリの再起動後もデータを保持できるようにする設定 |

## <a name="deployment"></a>デプロイ

以下の機能とプロパティはデプロイ レベルで定義され、運用環境デプロイとステージング デプロイのスワップ時に交換されます。

| 特徴 | 説明 |
|:--|:----------------|
| CPU | アプリ インスタンスあたりの仮想コアの数 |
| メモリ | アプリ インスタンスあたりのメモリの Gb 数|
| インスタンス</br>Count | 手動または自動で設定されるアプリ インスタンスの数 |
| Auto-Scale | 事前に定義されたルールとスケジュールに基づいてインスタンス数を自動的にスケーリングします |
| JVM</br>Options | JVM オプションの設定  |
| 環境</br>変数 | 環境変数の設定 |
| ランタイム</br>Version | Java 8 または Java 11|

## <a name="restrictions"></a>制限

* **1 つのアプリに 1 つの運用環境デプロイを割り当てる必要がある**:運用環境デプロイを削除しようとすると、API によってブロックされます。 削除する前にステージング デプロイにスワップする必要があります。
* **1 つのアプリに最大 2 つのデプロイを割り当てることができる**:3 つ以上のデプロイを作成しようとすると、API によってブロックされます。 新しいバイナリは、既存の運用環境デプロイまたはステージング デプロイのいずれかにデプロイします。
* **Basic レベルではデプロイ管理を使用できない**:ブルー/グリーン デプロイ機能を使用する場合は、Standard レベルを使用します。

## <a name="see-also"></a>関連項目
* [Azure Spring Cloud でステージング環境を設定する](spring-cloud-howto-staging-environment.md)
