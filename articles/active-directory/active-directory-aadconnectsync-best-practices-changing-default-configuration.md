<properties
	pageTitle="既定の構成の変更するためのベスト プラクティス"
	description="Azure AD Connect Sync の既定の構成を変更するためのベスト プラクティスを提供します。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect Sync: 既定の構成の変更するためのベスト プラクティス

Azure AD Connect で作成される構成は、オンプレミス Active Directory を Azure AD と同期する大部分の環境で同じように機能します。<br> ただし、場合によっては、特定のニーズや要件を満たすために構成にいくつか変更を適用する必要があります。

Azure AD 構成への変更の適用はサポートされますが、Azure AD はアプライアンスにできるだけ近くなければならないため、適用の際はご注意ください。

想定される動作の一覧は次のとおりです。

- Azure AD Connect を新しいバージョンにアップグレードすると、ほとんどの設定が既定の状態にリセットされます。
- アップグレードを適用した後は、“標準” の同期規則の変更は失われます。
- 削除された “標準" の同期規則は、新しいバージョンへのアップグレード中に再作成されます。
- 新しいバージョンへのアップグレードが適用されているときは、作成したカスタムの同期規則は変更されません。



既定の構成を変更する必要がある場合は、以下の手順を実行します。

- “標準" の同期規則の属性のフローを変更する必要がある場合は、変更しません。代わりに、必要な属性のフローを含む、優先順位の高い (小さい数値の) 新しい同期規則を作成します。
- 同期規則エディターを使用して、カスタムの同期規則をエクスポートします。これにより、障害復旧シナリオの場合に、簡単に再作成をするために使用できる PowerShell スクリプトが提供されます。
- "標準" の同期ルールのスコープまたは結合設定を変更する必要がある場合は、これを文書化し、新しいバージョンの Azure AD Sync にアップグレードした後に変更を再適用します。



**他の重要な注意事項:**

- 属性ベースのフィルターとパスワード同期を構成した場合は、パスワード同期のスコープ内に、Azure AD に同期されているオブジェクトのみがあることをご確認ください。 





## その他のリソース

* [Azure AD Connect Sync: 同期オプションのカスタマイズ](active-directory-aadconnectsync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)
 
<!--Image references-->

<!---HONumber=August15_HO6-->