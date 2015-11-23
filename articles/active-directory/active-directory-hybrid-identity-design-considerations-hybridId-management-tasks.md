<properties
	pageTitle="Azure Active Directory ハイブリッド ID の設計上の考慮事項 - ハイブリッド ID 管理タスクの決定 | Microsoft Azure"
	description="条件を利用してアクセスを制御する Azure Active Directory は、ユーザーの認証時、アプリケーションにアクセスを与える前に、選択された特定の条件を確認します。条件が満たされていれば、ユーザーは承認され、アプリケーションにアクセスできます。"
	documentationCenter=""
	services="active-directory"
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="11/11/2015"
	ms.author="femila"/>

# ハイブリッド ID ライフサイクルの計画を立てる 

ID はエンタープライズ モビリティとアプリケーション アクセスの戦略基盤の 1 つです。モバイル デバイスにサインオンする場合でも、SaaS アプリにサインオンする場合でも、ID があらゆるアクセスを得るための鍵となります。ID 管理ソリューションとは、概して、ID リポジトリ間の統一と同期であり、その中にリソースのプロビジョニングというプロセスの自動化と集中化が含まれます。ID ソリューションはオンプレミスとクラウドにわたり ID を一元化するものでなければならず、また、何らかの形式の ID フェデレーションを利用し、認証を一元化し、外部のユーザーや企業と安全に共有し、共同作業するものです。リソースは、オペレーティング システムやアプリケーションから組織内のユーザーや関連組織まで多岐にわたります。組織構造を変更し、プロビジョニングのポリシーと手続を調整できます。

ID ソリューションを調整し、ユーザーにセルフサービス機能を与え、生産性を上げてもらうことも重要です。ユーザーがアクセスするすべてのリソースでシングル サインオンが可能であり、あらゆるレベルの管理者が標準化された手続でユーザーの資格情報を管理できれば、ID ソリューションはより堅牢なものになります。プロビジョニング管理ソリューションの幅によっては、一部の管理レベルを減らしたり、なくしたりできます。さらに、さまざまな組織間で、手動または自動で、管理機能を安全に分配できます。たとえば、あるドメイン管理者は自分のドメインにある人とリソースにのみサービスを提供できます。このユーザーには管理タスクとプロビジョニング タスクが許可されますが、ワークフローの作成など、構成タスクは許可されません。


## ハイブリッド ID 管理タスクを決定する
組織内で管理タスクを配分することで、管理の精度と効果が上がり、組織の作業負荷のバランスが良くなります。堅牢な ID 管理システムは、次のような軸を基に定義されます。

 ![](./media/hybrid-id-design-considerations/Identity_management_considerations.png)


ハイブリッド ID 管理タスクを定義するには、ハイブリッド ID を導入する組織の本質的特性を理解する必要があります。ID ソースとして利用されている現在のリポジトリを理解することが重要です。以上の中心的要素を知ることで、基礎的な要件が与えられ、それに基づいてさらに細かく質問に答えることで、ID ソリューションの設計が効率的に導き出されます。

要件を定義するとき、少なくとも次の質問に答える必要があります。

- プロビジョニングの選択肢: 
 - そのハイブリッド ID ソリューションはアカウント アクセス管理とプロビジョニングの堅牢なシステムをサポートしますか。
 - ユーザー、グループ、パスワードの管理方法はどのようなものになりますか。
 - ID ライフサイクル管理は応答性の高いものになりますか。 
      - パスワード更新時のアカウントの停止期間はどのくらいになりますか。
      
- ライセンスの管理:
 - そのハイブリッド ID ソリューションはライセンス管理を処理しますか。
     - 処理する場合、どのような機能を利用できますか。
- そのソリューションはグループ基準のライセンス管理を処理しますか。 
      - 処理する場合、セキュリティ グループをそれに割り当てることができますか。 
       - 処理する場合、クラウド ディレクトリはグループのすべてのメンバーにライセンスを自動的に割り当てますか。 
        - その後、ユーザーがグループに追加されるか、グループから削除された場合、どのような動作が行われますか。ライセンスは適宜、自動的に割り当てられるか、削除されますか。 

- 他のサード パーティの ID プロバイダーと統合する:
- このハイブリッド ソリューションをサード パーティの ID プロバイダーと統合し、シングル サインオンを実装できますか。
- さまざまな ID プロバイダーを密着した ID システムに統合できますか。
- 統合できる場合、それは何という ID プロバイダーであり、どのような機能を利用できますか。

## 同期管理
ID マネージャーの目標の 1 つは、あらゆる ID プロバイダーを同期させることです。権限のあるマスター ID プロバイダーに基づき、データを同期します。同期管理モデルのあるハイブリッド ID シナリオでは、オンプレミス サーバーですべてのユーザーとデバイスの ID を管理し、アカウントと、必要に応じて、パスワードをクラウドに同期します。ユーザーはクラウドと同じパスワードをオンプレミスで入力します。サインイン時にパスワードが ID ソリューションにより検証されます。このモデルではディレクトリ同期ツールが使用されます。
 
![](./media/hybrid-id-design-considerations/Directory_synchronization.png) ハイブリッド ID ソリューションの同期を適切に設計するには、次の質問に答えます。 • ハイブリッド ID ソリューションに利用できる同期ソリューションは何ですか。 • どのようなシングル サインオン機能が利用できますか。 • B2B と B2C の間の ID フェデレーションにはどのような選択肢がありますか。

## 次のステップ
[ハイブリッド ID 管理の導入戦略の決定](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)


## 関連項目
[設計上の考慮事項の概要](active-directory-hybrid-identity-design-considerations-directory-overview.md)

<!---HONumber=Nov15_HO3-->