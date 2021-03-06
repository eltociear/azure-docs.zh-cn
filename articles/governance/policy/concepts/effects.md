---
title: 了解效果的工作原理
description: Azure Policy 定义具有各种效果，可确定管理和报告合规性的方式。
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 0330cb5c732921efda3627dec92e486657097d82
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422458"
---
# <a name="understand-azure-policy-effects"></a>了解 Azure Policy 效果

Azure Policy 中的每个策略定义都有单一效果。 该效果确定了在评估匹配的策略规则时发生的情况。 如果这些效果适用于新资源、更新的资源或现有资源，则它们的行为会有所不同。

策略定义目前支持以下效果：

- [Append](#append)
- [审核](#audit)
- [审核不存在](#auditifnotexists)
- [否认](#deny)
- [部署不存在](#deployifnotexists)
- [禁用](#disabled)
- [强制实施 OPA 约束](#enforceopaconstraint)（预览）
- [执行重新策略](#enforceregopolicy)（预览）
- [修改](#modify)

## <a name="order-of-evaluation"></a>计算顺序

Azure Policy 首先评估通过 Azure 资源管理器创建或更新资源的请求。 Azure Policy 创建应用于资源的所有分配列表，然后根据每个定义评估资源。 Azure Policy 在将请求转交给相应的资源提供程序之前处理多个效果。 这样做可以防止资源提供程序在资源不符合 Azure Policy 的设计治理控制时进行不必要的处理。

- 首先检查**已禁用**以确定是否应评估策略规则。
- 然后评估 **Append** 和 **Modify**。 由于上述任一效果可能会改变请求，因此所做的更改可能会阻止 audit 或 deny 效果的触发。
- 然后评估****“拒绝”。 通过在“审核”之前评估“拒绝”，可以防止两次记录不需要的资源。
- 然后在请求传输到资源提供程序之前评估**审核**。

资源提供程序返回成功代码后，将会评估 **AuditIfNotExists** 和 **DeployIfNotExists** 以确定是否需要其他合规性日志记录或操作。

对于 **EnforceOPAConstraint** 或 **EnforceRegoPolicy** 效果，目前没有任何评估顺序。

## <a name="disabled"></a>已禁用

对于测试情况以及在策略定义已参数化效果时，此效果很有用。 借助这种灵活性可以禁用单个分配，而无需禁用该策略的所有分配。

Disabled 效果的替代效果是针对策略分配设置的 **enforcementMode**。
如果 **enforcementMode** 设置为 _Disabled_，则仍会评估资源。 不会发生日志记录（例如活动日志）和策略效果。 有关详细信息，请参阅[策略分配 - 强制模式](./assignment-structure.md#enforcement-mode)。

## <a name="append"></a>附加

附加用于在创建或更新期间向请求的资源添加其他字段。 一个常见的示例是为存储资源指定允许的 IP。

> [!IMPORTANT]
> Append 旨在与非标记属性配合使用。 尽管 Append 可以在创建或更新请求期间将标记添加到资源，但我们建议对标记改用 [Modify](#modify) 效果。

### <a name="append-evaluation"></a>“附加”评估

在创建或更新资源期间，会在资源提供程序处理请求之前进行“附加”评估。 当满足策略规则的 **if** 条件时，“附加”会向资源添加字段。 如果“附加”效果使用其他值替代原始请求中的值，则它会充当拒绝效果并拒绝该请求。 要将新值追加到现有数组，请使用别名的 ***\*** 版本。

当使用附加效果的策略定义作为评估周期的一部分运行时，它不会更改已存在的资源。 相反，它会将符合 **** if 条件的任意资源标记为不符合。

### <a name="append-properties"></a>“附加”属性

附加效果只有“详细信息”**** 数组，它是必需的。 因为****“详细信息”是一个数组，它可能需要单个或多个字段/值**** 对。 请参阅[定义结构](definition-structure.md#fields)，获取可接受的字段列表。

### <a name="append-examples"></a>“附加”示例

示例 1：[使用具有数组](definition-structure.md#aliases)**值**的非**字段\*****/值**对在存储帐户上设置 IP 规则。 当非 **+\*别名**是数组时，效果将**值**追加为整个数组。 如果数组已存在，该冲突会导致拒绝事件发生。

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

示例 2：使用具有数组**值**的 **+\*** [别名](definition-structure.md#aliases)的单个**字段/值**对在存储帐户上设置 IP 规则。 通过使用 ***\*别名**，效果将**该值**追加到可能预先存在的数组。 如果该数组尚不存在，系统会创建该数组。

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="modify"></a>修改

Modify 用于在创建或更新期间在资源中添加、更新或删除标记。 一个常见的示例是在 costCenter 等资源中更新标记。 除非目标资源是资源组，否则 Modify 策略的 `mode` 应始终设置为 _Indexed_。 可以使用[修正任务](../how-to/remediate-resources.md)来修正现有的不合规资源。 单个 Modify 规则可以包含任意数量的操作。

> [!IMPORTANT]
> Modify 目前只能与标记配合使用。 如果你正在管理标记，我们建议使用 Modify 而不要使用 Append，因为 Modify 提供更多的操作类型，且能够修正现有的资源。 但是，如果无法创建托管标识，则我们建议使用 Append。

### <a name="modify-evaluation"></a>Modify 评估

Modify 在创建或更新资源期间，在资源提供程序处理请求之前进行评估。 当满足策略规则的 **if** 条件时，Modify 会在资源中添加或更新标记。

当使用 Modify 效果的策略定义作为评估周期的一部分运行时，它不会更改已存在的资源。 相反，它会将符合 **** if 条件的任意资源标记为不符合。

### <a name="modify-properties"></a>Modify 属性

Modify 效果 **details** 属性包含用于定义修正任务所需的权限的所有子属性，以及用于添加、更新或删除标记值的**操作**。

- **roleDefinitionIds** [必选]
  - 此属性必须包含与可通过订阅访问的基于角色的访问控制角色 ID 匹配的字符串数组。 有关详细信息，请参阅[修正 - 配置策略定义](../how-to/remediate-resources.md#configure-policy-definition)。
  - 定义的角色必须包含授予[参与者](../../../role-based-access-control/built-in-roles.md#contributor)角色的所有操作。
- **operations** [必需]
  - 要对匹配的资源完成的所有标记操作的数组。
  - 属性：
    - **operation** [必需]
      - 定义要对匹配的资源执行的操作。 选项为：_addOrReplace_、_Add_、_Remove_。 _Add_ 的行为类似于 [Append](#append) 效果。
    - **field** [必需]
      - 要添加、替换或删除的标记。 标记名称必须遵守适用于其他[字段](./definition-structure.md#fields)的相同命名约定。
    - **value**（可选）
      - 要为标记设置的值。
      - 如果 **operation** 为 _addOrReplace_ 或 _Add_，则此属性是必需的。

### <a name="modify-operations"></a>Modify 操作

使用 **operations** 属性数组可在单个策略定义中以不同的方式更改多个标记。 每个操作由 **operation**、**field** 和 **value** 属性组成。 operation 确定修正任务对标记执行的操作，field 确定要更改的标记，value 定义该标记的新设置。 下面是进行标记更改的示例：

- 将 `environment` 标记设置为“Test”，即使它已存在并使用不同的值。
- 删除标记 `TempResource`。
- 将 `Dept` 标记设置为在策略分配中配置的策略参数 _ DeptName_。

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

**operation** 属性具有以下选项：

|Operation |说明 |
|-|-|
|addOrReplace |将定义的标记和值添加到资源，即使该标记已存在并使用不同的值。 |
|添加 |将定义的标记和值添加到资源。 |
|删除 |从资源中删除定义的标记。 |

### <a name="modify-examples"></a>Modify 示例

示例 1：添加`environment`标记并将现有`environment`标记替换为"测试"：

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

示例 2：删除`env`标记并添加`environment`标记或将现有`environment`标记替换为参数化值：

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```

## <a name="deny"></a>拒绝

“拒绝”用于通过策略定义防止与定义的标准不匹配的资源请求，并使请求失败。

### <a name="deny-evaluation"></a>“拒绝”评估

创建或更新匹配的资源时，“拒绝”会在发送给资源提供程序之前阻止请求。 该请求返回为 `403 (Forbidden)`。 在门户中，可以将 Forbidden（禁止）视为策略分配阻止的部署状态。

在评估现有资源期间，与“拒绝”策略定义匹配的资源将标记为不合规。

### <a name="deny-properties"></a>“拒绝”属性

“拒绝”效果没有任何可用于策略定义的 **then** 条件的其他属性。

### <a name="deny-example"></a>“拒绝”示例

示例：使用“拒绝”效果。

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>审核

“审核”用于评估不合规资源时在活动日志中创建警告事件，但不会停止请求。

### <a name="audit-evaluation"></a>“审核”评估

“审核”是创建或更新资源期间由 Azure Policy 检查的最后一个效果。 然后，Azure Policy 将资源发送到资源提供程序。 “审核”对于资源请求和评估周期的工作方式相同。 Azure Policy 将 `Microsoft.Authorization/policies/audit/action` 操作添加到活动日志，并将资源标记为不合规。

### <a name="audit-properties"></a>“审核”属性

“审核”效果没有任何可用于策略定义的 **then** 条件的其他属性。

### <a name="audit-example"></a>“审核”示例

示例：使用“审核”效果。

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists 对匹配 **if** 条件的资源启用审核，但没有在 **then** 条件的 **details** 中指定的组件。

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists 评估

AuditIfNotExists 在资源提供程序处理资源创建或更新请求并返回成功状态代码后运行。 如果没有相关资源或如果由 **ExistenceCondition** 定义的资源未评估为 true，则会发生审核。 与使用“审核”效果时一样，Azure Policy 会将 `Microsoft.Authorization/policies/audit/action` 操作添加到活动日志。 触发后，满足 if 条件**** 的资源是标记为不符合的资源。

### <a name="auditifnotexists-properties"></a>AuditIfNotExists 属性

AuditIfNotExists 效果的****“details”属性具有定义要匹配的相关资源的所有子属性。

- **Type** [必选]
  - 指定要匹配的相关资源的类型。
  - 如果 **details.type** 是 **If** 条件资源下的资源类型，则策略将在已评估资源的范围内查询此**类型**的资源。 否则，策略将在与已评估资源相同的资源组内进行查询。
- **Name**（可选）
  - 指定要匹配的资源的确切名称，并使策略提取一个特定资源，而不是指定类型的所有资源。
  - 当 **if.field.type** 和 **then.details.type** 的条件值匹配时，**Name** 将变为_必需_且必须为 `[field('name')]`。 但是，应改为考虑 [audit](#audit) 效果。
- **ResourceGroupName**（可选）
  - 允许相关资源的匹配来自不同的资源组。
  - 如果 **type** 是 **if** 条件资源下的一个资源，则不适用。
  - 默认值是 if **** 条件资源的资源组。
- **ExistenceScope**（可选）
  - 允许的值为 Subscription __ 和 ResourceGroup__。
  - 设置从中获取相关资源以在其中进行匹配的范围。
  - 如果 **type** 是 **if** 条件资源下的一个资源，则不适用。
  - ResourceGroup __ 将限制在 if **** 条件资源的资源组或 ResourceGroupName **** 中指定的资源组。
  - 对于 Subscription__，则查询全部订阅以获取相关资源。
  - 默认值是 ResourceGroup__。
- **ExistenceCondition**（可选）
  - 如果未指定，任何 **type** 的相关资源均满足此效果，并且不会触发审核。
  - 使用与 if **** 条件的策略规则相同的语言，但会分别针对每个相关资源进行评估。
  - 如果任何匹配的相关资源评估结果为 true，该效果就会得到满足并且不会触发审核。
  - 可以使用 [field()] 检查 if **** 条件中的值的等效性。
  - 例如，可用于验证父资源（位于 if **** 条件中）与匹配的相关资源位于相同的资源位置。

### <a name="auditifnotexists-example"></a>AuditIfNotExists 示例

示例：评估虚拟机以确定是否存在反恶意软件扩展，然后在缺失时进行审核。

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

与 AuditIfNotExists 类似，DeployIfNotExists 策略定义在满足条件时执行模板部署。

> [!NOTE]
> **deployIfNotExists** 支持[嵌套模板](../../../azure-resource-manager/templates/linked-templates.md#nested-template)，但目前不支持[链接模版](../../../azure-resource-manager/templates/linked-templates.md#linked-template)。

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists 评估

部署IfNotExists在资源提供程序处理创建或更新资源请求并返回成功状态代码后大约 15 分钟运行。 如果没有相关资源或如果由 **ExistenceCondition** 定义的资源未评估为 true，则会发生模板部署。
部署的持续时间取决于模板中包含的资源的复杂性。

在评估周期中，具有与资源匹配的 DeployIfNotExists 效果的策略定义被标记为不合规，但不对该资源执行任何操作。 可以使用[修正任务](../how-to/remediate-resources.md)来修正现有的不合规资源。

### <a name="deployifnotexists-properties"></a>DeployIfNotExists 属性

DeployIfNotExists 效果的 **details** 属性包含用于定义要匹配的相关资源和要执行的模板部署的所有子属性。

- **Type** [必选]
  - 指定要匹配的相关资源的类型。
  - 首先尝试提取 if **** 条件资源下的资源，然后在与**** if 条件资源相同的资源组中进行查询。
- **Name**（可选）
  - 指定要匹配的资源的确切名称，并使策略提取一个特定资源，而不是指定类型的所有资源。
  - 当 **if.field.type** 和 **then.details.type** 的条件值匹配时，**Name** 将变为_必需_且必须为 `[field('name')]`。
- **ResourceGroupName**（可选）
  - 允许相关资源的匹配来自不同的资源组。
  - 如果 **type** 是 **if** 条件资源下的一个资源，则不适用。
  - 默认值是 if **** 条件资源的资源组。
  - 如果执行模板部署，则将其部署在此值的资源组中。
- **ExistenceScope**（可选）
  - 允许的值为 Subscription __ 和 ResourceGroup__。
  - 设置从中获取相关资源以在其中进行匹配的范围。
  - 如果 **type** 是 **if** 条件资源下的一个资源，则不适用。
  - ResourceGroup __ 将限制在 if **** 条件资源的资源组或 ResourceGroupName **** 中指定的资源组。
  - 对于 Subscription__，则查询全部订阅以获取相关资源。
  - 默认值是 ResourceGroup__。
- **ExistenceCondition**（可选）
  - 如果未指定，任何 **type** 的相关资源均满足此效果，并且不会触发部署。
  - 使用与 if **** 条件的策略规则相同的语言，但会分别针对每个相关资源进行评估。
  - 如果任何匹配的相关资源评估结果为 true，该效果就会得到满足并且不会触发部署。
  - 可以使用 [field()] 检查 if **** 条件中的值的等效性。
  - 例如，可用于验证父资源（位于 if **** 条件中）与匹配的相关资源位于相同的资源位置。
- **roleDefinitionIds** [必选]
  - 此属性必须包含与可通过订阅访问的基于角色的访问控制角色 ID 匹配的字符串数组。 有关详细信息，请参阅[修正 - 配置策略定义](../how-to/remediate-resources.md#configure-policy-definition)。
- **DeploymentScope**（可选）
  - 允许的值为 Subscription __ 和 ResourceGroup__。
  - 设置要触发的部署类型。 _Subscription_ 指示[在订阅级别部署](../../../azure-resource-manager/templates/deploy-to-subscription.md)，_ResourceGroup_ 指示部署到资源组。
  - 使用订阅级别部署时，必须在 _Deployment_ 中指定 _location_ 属性。
  - 默认值是 ResourceGroup__。
- **Deployment** [必选]
  - 该属性应包含完整的模板部署，因为它将传递给 `Microsoft.Resources/deployments` PUT API。 有关详细信息，请参阅[部署 REST API](/rest/api/resources/deployments)。

  > [!NOTE]
  > **** Deployment 属性中的所有函数都将作为模板（而不是策略）的组件进行评估。 此异常是将值从策略传递到模板的 parameters **** 属性。 本节中模板参数名称下的 value **** 用于执行此值传递操作（请参阅 DeployIfNotExists 示例中的 _fullDbName_）。

### <a name="deployifnotexists-example"></a>DeployIfNotExists 示例

示例：评估 SQL Server 数据库以确定是否启用 transparentDataEncryption。 如果未启用，则执行启用它的部署。

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="enforceopaconstraint"></a>强制实施 OPA 约束

此效果与`Microsoft.Kubernetes.Data`的策略定义*模式*一起使用。 它用于将使用[OPA 约束框架](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework)定义的 Gatekeeper v3 准入控制规则传递给[Azure](https://www.openpolicyagent.org/)上的自管理的 Kubernetes 群集。

> [!NOTE]
> [AKS 引擎的 Azure 策略](aks-engine.md)处于公共预览版，仅支持内置策略定义。

### <a name="enforceopaconstraint-evaluation"></a>强制OPA约束评估

开放策略代理准入控制器实时评估群集上的任何新请求。
每隔 5 分钟完成群集的完整扫描，并将结果报告给 Azure 策略。

### <a name="enforceopaconstraint-properties"></a>强制OPA约束属性

强制OPA约束效果**的详细信息**属性具有描述门卫 v3 准入控制规则的子属性。

- **约束模板**[必需]
  - 定义新约束的约束模板自定义资源定义 （CRD）。 模板定义通过 Azure 策略**的值**传递的 Rego 逻辑、约束架构和约束参数。
- **约束**[必需]
  - 约束模板的 CRD 实现。 使用通过**值**传递的参数作为`{{ .Values.<valuename> }}`。 在下面的示例中，这将是`{{ .Values.cpuLimit }}`和`{{ .Values.memoryLimit }}`。
- **值**[可选]
  - 定义要传递给约束的任何参数和值。 约束模板 CRD 中必须存在每个值。

### <a name="enforceopaconstraint-example"></a>强制 OPA 约束示例

示例：网守 v3 准入控制规则，用于在 AKS 引擎中设置容器 CPU 和内存资源限制。

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## <a name="enforceregopolicy"></a>执行重戈政策

此效果与`Microsoft.ContainerService.Data`的策略定义*模式*一起使用。 它用于传递在[Azure Kubernetes 服务](../../../aks/intro-kubernetes.md)上使用[Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)到[打开策略代理](https://www.openpolicyagent.org/)（OPA） 定义的门卫 v2 准入控制规则。

> [!NOTE]
> [AKS 的 Azure 策略](rego-for-aks.md)处于"有限预览"状态，仅支持内置策略定义

### <a name="enforceregopolicy-evaluation"></a>执行重新策略评估

开放策略代理准入控制器实时评估群集上的任何新请求。
每隔 5 分钟完成群集的完整扫描，并将结果报告给 Azure 策略。

### <a name="enforceregopolicy-properties"></a>强制执行"重新戈策略"属性

强制重新策略效果**的详细信息**属性具有描述门卫 v2 准入控制规则的子属性。

- **策略 Id** [必需]
  - 作为参数传递给 Rego 准入控制规则的唯一名称。
- **策略**[必需]
  - 指定"Rego 准入控制"规则的 URI。
- **策略参数**[可选]
  - 定义要传递给 rego 策略的任何参数和值。

### <a name="enforceregopolicy-example"></a>执行重新策略示例

示例：网守 v2 准入控制规则，以仅允许在 AKS 中指定容器映像。

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## <a name="layering-policies"></a>分层策略

资源可能会受到多个分配的影响。 这些分配可能处于相同或不同的范围。 这些分配中的每一个也可能具有不同的定义效果。 将单独评估每个策略的条件和效果。 例如：

- 策略 1
  - 将资源位置限制为“westus”
  - 分配到订阅 A
  - “拒绝”效果
- 策略 2
  - 将资源位置限制为“eastus”
  - 分配到订阅 A 中的资源组 B
  - “审核”效果
  
此设置将产生以下结果：

- 位于“eastus”的资源组 B 中的任何现有资源都符合策略 2，但不符合策略 1
- 不位于“eastus”的资源组 B 中的任何现有资源都不符合策略 2，并且如果它们不在“westus”中，则也不符合策略 1
- 订阅 A 中任何不在“westus”中的任何新资源将被策略 1 拒绝
- 位于“westus”的订阅 A 和资源组 B 中的任何新资源将会创建，但不符合策略 2

如果策略 1 和策略 2 都具有“拒绝”效果，则情况变为：

- 不位于“eastus”的资源组 B 中的任何现有资源不符合策略 2
- 不位于“westus”的资源组 B 中的任何现有资源不符合策略 1
- 订阅 A 中任何不在“westus”中的任何新资源将被策略 1 拒绝
- 订阅 A 的资源组 B 中的任何新资源将被拒绝

单独评估每个分配。 因此，不存在因范围差异致使资源溜过间隙的可能性。 我们认为分层策略或策略重叠的最终结果是**累积性的最高限制**。 例如，如果策略 1 和策略 2 都具有“拒绝”效果，则重叠和冲突策略会阻止资源。 如果仍然需要在目标范围内创建资源，请查看每项分配的排除项，以验证适当的策略是否会影响适当的范围。

## <a name="next-steps"></a>后续步骤

- 查看[Azure 策略示例](../samples/index.md)中的示例。
- 查看 [Azure Policy 定义结构](definition-structure.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取合规性数据](../how-to/get-compliance-data.md)。
- 了解如何[修复不合规资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
