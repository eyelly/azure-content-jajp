<properties
	pageTitle="Azure リソース マネージャーのポリシー | Microsoft Azure"
	description="Azure リソース マネージャーのポリシーを使用し、サブスクリプション、リソース グループまたは個々のリソースなどのさまざまなスコープの違反を防ぐ方法について説明します。"
	services="azure-resource-manager"
	documentationCenter="na"
	authors="ravbhatnagar"
	manager="ryjones"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="10/06/2015"
	ms.author="gauravbh;tomfitz"/>

# ポリシーを使用したリソース管理とアクセス制御

Azure リソース マネージャーで、カスタム ポリシーを使用してアクセスを制御できるようになりました。ポリシーとは、希望のスコープで回避できる 1 つ以上の違反です。この場合のスコープとは、サブスクリプション、リソース グループまたは個々のリソースになります。

ポリシーとは既定の許可システムです。ポリシーはポリシー定義で定義され、ポリシー割り当てによって適用されます。ポリシー割り当ては、ポリシーを適用するスコープを制御できるようにします。

この記事では、ポリシーの作成で使用する、ポリシー定義言語の基本構造について説明します。その後、これらのポリシーをさまざまなスコープに適用する方法と、最後には REST API を使用してこれを実現する方法の例をいくつか紹介します。近々に PowerShell もサポート対象となります。

## 一般的なシナリオ

よくあるシナリオの 1 つは、チャージバック目的で部門別のタグが必要性な場合です。適切なコスト センターが関連付けられている場合にのみ、組織で操作を許可し、それ以外の場合は要求を拒否したい場合があります。これにより、実行された操作を適切なコスト センターから請求できるようになります。

別のよくあるシナリオに、組織が、リソースの作成場所を制御したい場合があります。または、特定の種類のリソースのみのプロビジョニングを許可することによって、リソースへのアクセスを制御したい場合があります。

同様に、組織がサービス カタログを制御したい場合や、リソースに希望の名前付け規則を適用したい場合があります。

これらのシナリオは、ポリシーを使用して次に示すように簡単に実現できます。

## ポリシー定義の構造

ポリシー定義は JSON を使用して作成します。これは、条件が満たされた場合に何が発生するかを伝えるアクションおよび効果を定義する 1 つ以上の条件および論理演算子で構成されます。

ポリシーには、基本的に次が含まれます。

**条件および論理演算子:** 一連の論理演算子を使用して操作できる条件のセットが含まれています。

**効果:** これには条件が満たされた場合の拒否または監査のいずれかの効果を示します。監査の効果は、警告イベント サービス ログを出力します。たとえば、管理者は、誰かがサイズの大きな VM を作成した場合に監査を実行するポリシーを作成し、後でログを確認することができます。

    {
      "if" : {
        <condition> | <logical operator>
      },
      "then" : {
        "effect" : "deny | audit"
      }
    }


## 論理演算子

サポートされている論理演算子とその構文を次に示します。

| 演算子名 | 構文 |
| :------------- | :------------- |
| Not | "not" : {&lt;condition&gt;} |
| 論理積 | "allOf" : [ {&lt;condition1&gt;},{&lt;condition2&gt;}] |
| または | "anyOf" : [ {&lt;condition1&gt;},{&lt;condition2&gt;}] |


## 条件

サポートされている条件とその構文を次に示します。

| 条件名 | 構文 |
| :------------- | :------------- |
| 等しい | "equals" : "&lt;value&gt;" |
| LIKE | "like" : "&lt;value&gt;" |
| 指定値を含む | "contains" : "&lt;value&gt;"|
| [ | "in" : [ "&lt;value1&gt;","&lt;value2&gt;" ]|
| ContainsKey | "containsKey" : "&lt;keyName&gt;" |


## フィールドおよびソース

条件は、フィールドおよびソースを使用して構成します。次のフィールドおよびソースがサポートされています。

フィールド: **name**、**kind**、**type**、**location**、**tags**、**tags.***。

ソース: **action**

## ポリシー定義の例

ここでは、上で挙げたシナリオを実現するポリシーを定義する方法を見てをみましょう。

### チャージバック:、部門別のタグが必要

次のポリシーは、"costCenter" キーを含むタグがないすべての要求を拒否します。

    {
      "if": {
        "not" : {
          "field" : "tags",
          "containsKey" : "costCenter"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }


### 地理的準拠: リソースの場所を確認

次では、場所が北ヨーロッパや西ヨーロッパでない、すべての要求を拒否するポリシーの例を示しています。

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["north europe" , "west europe"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### サービスのキュレーション: サービス カタログを選択

次では、ソースの使用例を示しています。種類が Microsoft.Resources/*、Microsoft.Compute/*、Microsoft.Storage/*、Microsoft.Network/* のサービスのアクションのみが許可されることを示しています。その他は拒否されます。

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "source" : "action",
              "like" : "Microsoft.Resources/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Compute/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Storage/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### 命名規則

次では、"like" の条件でサポートされているワイルドカードの使用例を示しています。この条件は、名前が示しているパターンと一致する場合 (namePrefix*nameSuffix)、要求を拒否するよう示しています。

    {
      "if" : {
        "not" : {
          "field" : "name",
          "like" : "namePrefix*nameSuffix"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

## ポリシーの割り当て

ポリシーは、サブスクリプション、リソース グループおよび個々のリソースなどのさまざまなスコープを適用できます。ポリシーは、すべての子リソースが継承します。したがって、リソース グループに適用されたポリシーは、そのリソース グループのすべてのリソースに適用されます。

## ポリシーの作成

ここでは、REST API を使用して、ポリシーを作成する方法を詳細に説明します。

### REST API を使用したポリシー定義の作成

[ポリシー定義用の REST API](https://msdn.microsoft.com/library/azure/mt588471.aspx) を使用して、ポリシーを作成できます。REST API を使用すると、ポリシー定義の作成と削除、既存の定義に関する情報の取得を実行できます。

新しいポリシーを作成するには、次を実行します。

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

要求の本文は次のようになります。

    {
      "properties":{
        "policyType":"Custom",
        "description":"Test Policy",
        "policyRule":{
          "if" : {
            "not" : {
              "field" : "tags",
              "containsKey" : "costCenter"
            }
          },
          "then" : {
            "effect" : "deny"
          }
        }
      },
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
      "type":"Microsoft.Authorization/policyDefinitions",
      "name":"testdefinition"
    }


ポリシー定義は、前述の例のいずれかとして定義できます。api-version には、*2015-10-01-preview* を使用します。例と詳細については、[ポリシー定義用 REST API](https://msdn.microsoft.com/library/azure/mt588471.aspx) のページを参照してください。

## ポリシーの適用

### REST API を使用したポリシーの割り当て

[ポリシーの割り当て用 REST API](https://msdn.microsoft.com/library/azure/mt588466.aspx) で、目的のスコープに対してポリシー定義を適用します。REST API を使用すると、ポリシー割り当ての作成と削除、既存の割り当てに関する情報の取得を実行できます。

新しいポリシー割り当てを作成するには、次を実行します。

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

ポリシー割り当ての名前は、{policy-assignment} です。api-version には、*2015-10-01-preview* を使用します。

要求の本文は次のようになります。

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyAssignments/VMPolicyAssignment",
      "type":"Microsoft.Authorization/policyAssignments",
      "name":"VMPolicyAssignment"
    }

例と詳細については、[ポリシー割り当用 REST API](https://msdn.microsoft.com/library/azure/mt588466.aspx) のページを参照してください。

<!---HONumber=Oct15_HO2-->