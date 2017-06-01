---
title: "Azure Active Directory B2C: カスタム ポリシーの使用に関する開発者向けのノート | Microsoft Docs"
description: "カスタム ポリシーで B2C を構成および管理する開発者向けのノート"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 05/05/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 72447a234c5889f36b10b828d28775fc5995c4d4
ms.contentlocale: ja-jp
ms.lasthandoff: 05/09/2017


---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Azure Active Directory B2C カスタム ポリシー パブリック プレビューのリリース ノート
**カスタム ポリシー**機能セットは、すべての Azure Active Directory B2C (Azure AD B2C) のユーザーがパブリック プレビューで評価できるようになりました。  この機能セットは、最も複雑な ID ソリューションを構築する上級 ID 開発者を対象としています。  

現在、この機能セットを使用するために、開発者は XML ファイルを編集して直接 Identity Experience Framework (IEF) を構成する必要があります。  この構成の方法は、強力で複雑です。  IEF を使用する上級 ID 開発者は、チュートリアルを実行したり参考ドキュメントを確認したりすることで学習に時間をかけることを計画する必要があります。 

## <a name="features-included-in-this-public-preview"></a>このパブリック プレビューに含まれている機能
パブリック プレビューで導入された新機能を使用すると、開発者は次のタスクを実行できます。
1. カスタム ポリシーを使用して、カスタム認証のユーザー体験を作成およびアップロードする
* 要求プロバイダー間のやり取りとしてユーザー体験を順を追って記述する
* ユーザー体験の条件分岐を定義する
2. カスタム認証のユーザー体験で REST API 対応サービスを統合する
3. OpenIDConnect 標準に準拠している ID プロバイダーとのフェデレーションを追加する
4. SAML 2.0 プロトコルに準拠している ID プロバイダーとのフェデレーションを追加する



## <a name="terms-of-the-public-preview"></a>パブリック プレビューの使用条件
1. 新機能の使用は、評価目的のみで推奨されています
2. 新機能は、運用環境での使用を目的としていません
3. サービス レベル アグリーメント (SLA) は新機能には適用されません。 サポート要求は、通常のサポート チャネルを通じて提出することができます
5. 一般公開の期日は確定されていません
6. Microsoft の裁量や何らかの理由により、Microsoft は、Azure AD B2C 製品が顧客 ID およびアクセス管理 (CIAM) プラットフォームとしての役割を果たすための取り決めの範囲を超えるシナリオおよびユーザー体験を停止、拒否、および制限する場合があります

## <a name="responsibilities-for-custom-policy-feature-set-developers"></a>カスタム ポリシー機能セット開発者の責任
手動のポリシー構成で付与される Azure AD B2C の基になるプラットフォームへのアクセス レベルは低くなるため、独自の完全にカスタマイズ可能な信頼フレームワークが作成されます。  カスタム ID プロバイダー、信頼関係、外部サービスとの統合、ステップバイステップのワークフローのほぼ無限の順列により、それらを使用する上級開発者に対する要求はより厳しくなります。
パブリック プレビューを十分に活用するために、カスタム ポリシー機能セットを利用する開発者には、次の点に従うことをお勧めします。
* Identity Experience Engine (IEE) とキー/シークレット管理の構成言語について理解を深める
* シナリオおよびカスタム統合の主導権を得る
* シナリオの体系的なテストを実行する
* ソフトウェアの開発とステージングのベスト プラクティスに従い、少なくとも 1 つの開発/テスト環境と 1 つの運用環境を用意する
* 統合対象の ID プロバイダーおよびサービスの新しい開発に関して最新情報を取得したり (たとえば、シークレットの変更やサービスの定期/不定期の変更を追跡するなど)、アクティブな監視を設定して運用環境の応答時間を監視したりする
* 連絡先のメール アドレスを最新に保ち、Microsoft のライブ サイト チームのメールに対応できるようにしておく
* Microsoft ライブ サイト チームからのアドバイスがあった場合に、すぐに対処する 


>[!NOTE]
>これらの機能は、最終的には Azure AD の組み込みポリシーに含まれ、すべての開発者にとって利用しやすくなる予定です。

## <a name="next-steps"></a>次のステップ
[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)

