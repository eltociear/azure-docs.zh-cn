---
title: Azure 事件网格机器学习事件架构
description: 介绍为 Azure 事件网格中的机器学习工作区事件提供的属性
services: event-grid
author: jenns
ms.service: event-grid
ms.topic: reference
ms.date: 10/18/2019
ms.author: jenns
ms.openlocfilehash: 4051598a9abd787f6707e67a8c4dab12fc6d626a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79202138"
---
# <a name="azure-event-grid-event-schema-for-azure-machine-learning"></a>Azure 机器学习的 Azure 事件网格事件架构

本文提供了机器学习工作区事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。

有关示例脚本和教程的列表，请参阅 [AzureML 事件源](event-sources.md#azure-machine-learning)。

## <a name="available-event-types"></a>可用事件类型

Azure 机器学习发出以下事件类型：

| 事件类型 | 描述 |
| ---------- | ----------- |
| Microsoft.MachineLearningServices.ModelRegistered | 已成功注册新模型或模型版本时引发。 |
| Microsoft.MachineLearningServices.ModelDeployed | 将模型成功部署到终结点时引发。 |
| Microsoft.MachineLearningServices.RunCompleted | 在成功完成运行时引发。 |
| Microsoft.MachineLearningServices.DatasetDriftDetected | 当数据集偏移监视器检测到偏移时引发。 |
| 微软.机器学习服务.运行状态已更改 | 当运行状态更改为"失败"时引发。 |

## <a name="the-contents-of-an-event-response"></a>事件响应的内容

触发某个事件后，事件网格服务会将有关该事件的数据发送到订阅终结点。

本部分包含一个示例，介绍每个事件的数据外观。

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Microsoft.MachineLearningServices.ModelRegistered event

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "models/sklearn_regression_model:20",
  "eventType": "Microsoft.MachineLearningServices.ModelRegistered",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ModelName": "sklearn_regression_model",
    "ModelVersion": 20,
    "ModelTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ModelProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Microsoft.MachineLearningServices.ModelDeployed event

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "endpoints/my-sklearn-service",
  "eventType": "Microsoft.MachineLearningServices.ModelDeployed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ServiceName": "my-sklearn-service",
    "ServiceComputeType": "ACI",
    "ModelIds": "sklearn_regression_model:1,sklearn_regression_model:2",
    "ServiceTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ServiceProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Microsoft.MachineLearningServices.RunCompleted event

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Microsoft.MachineLearningServices.DatasetDriftDetected event

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "datadrifts/{}/runs/{}",
  "eventType": "Microsoft.MachineLearningServices.DatasetDriftDetected",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "DataDriftId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef",
    "DataDriftName": "myDriftMonitor",
    "RunId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef_1571590300380",
    "BaseDatasetId": "3c56d136-0f64-4657-a0e8-5162089a88a3",
    "TargetDatasetId": "d7e74d2e-c972-4266-b5fb-6c9c182d2a74",
    "DriftCoefficient": 0.83503490684792081,
    "StartTime": "2019-07-04T00:00:00+00:00",
    "EndTime": "2019-07-05T00:00:00+00:00"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesrunstatuschanged-event"></a>微软.机器学习服务.运行状态更改事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    },
   "RunStatus": "failed"
   },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```




## <a name="event-properties"></a>事件属性

事件具有以下顶级数据：

| properties | 类型 | 说明 |
| -------- | ---- | ----------- |
| 主题 | 字符串 | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| subject | 字符串 | 事件主题的发布者定义路径。 |
| eventType | 字符串 | 此事件源的一个注册事件类型。 |
| EventTime | 字符串 | 基于提供程序 UTC 时间的事件生成时间。 |
| id | 字符串 | 事件的唯一标识符。 |
| data | 对象 (object) | Blob 存储事件数据。 |
| dataVersion | 字符串 | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | 字符串 | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

对于每个事件类型，数据对象具有以下属性：

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft.MachineLearningServices.ModelRegistered

| properties | 类型 | 说明 |
| -------- | ---- | ----------- |
| ModelName | 字符串 | 已注册模型的名称。 |
| ModelVersion | 字符串 | 已注册模型的版本。 |
| ModelTags | 对象 (object) | 已注册模型的标记。 |
| ModelProperties | 对象 (object) | 已注册模型的属性。 |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft.MachineLearningServices.ModelDeployed

| properties | 类型 | 说明 |
| -------- | ---- | ----------- |
| ServiceName | 字符串 | 已部署服务的名称。 |
| ServiceComputeType | 字符串 | 已部署服务的计算类型（例如 ACI、AKS）。 |
  | ModelIds | 字符串 | 模型 ID 的逗号分隔列表。 服务中部署的模型的 ID。 |
| ServiceTags | 对象 (object) | 已部署服务的标记。 |
| ServiceProperties | 对象 (object) | 已部署服务的属性。 |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft.MachineLearningServices.RunCompleted

| properties | 类型 | 说明 |
| -------- | ---- | ----------- |
| ExperimentId | 字符串 | 此运行所属的试验的 ID。 |
| ExperimentName | 字符串 | 此运行所属的试验的名称。 |
| RunId | 字符串 | 已完成的运行的 ID。 |
| RunType | 字符串 | 已完成的运行的运行类型。 |
| RunTags | 对象 (object) | 已完成的运行的标记。 |
| RunProperties | 对象 (object) | 已完成的运行的属性。 |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft.MachineLearningServices.DatasetDriftDetected

| properties | 类型 | 说明 |
| -------- | ---- | ----------- |
| DataDriftId | 字符串 | 触发了事件的数据偏移监视器的 ID。 |
| DataDriftName | 字符串 | 触发了事件的数据偏移监视器的名称。 |
| RunId | 字符串 | 检测到数据偏移的运行的 ID。 |
| BaseDatasetId | 字符串 | 用于检测偏移的基础数据集的 ID。 |
| TargetDatasetId | 字符串 | 用于检测偏移的目标数据集的 ID。 |
| DriftCoefficient | double | 触发了事件的系数结果。 |
| StartTime | datetime | 导致了偏移检测的目标数据集时序的开始时间。  |
| EndTime | datetime | 导致了偏移检测的目标数据集时序的结束时间。 |

### <a name="microsoftmachinelearningservicesrunstatuschanged"></a>微软.机器学习服务.运行状态已更改

| properties | 类型 | 说明 |
| -------- | ---- | ----------- |
| ExperimentId | 字符串 | 此运行所属的试验的 ID。 |
| ExperimentName | 字符串 | 此运行所属的试验的名称。 |
| RunId | 字符串 | 已完成的运行的 ID。 |
| RunType | 字符串 | 已完成的运行的运行类型。 |
| RunTags | 对象 (object) | 已完成的运行的标记。 |
| RunProperties | 对象 (object) | 已完成的运行的属性。 |
| 运行状态 | 字符串 | 运行的状态。 |

## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)
* 有关如何将 Azure 事件网格与 Azure 机器学习配合使用的简介，请参阅[使用 Azure 机器学习事件](/azure/machine-learning/service/concept-event-grid-integration)
* 若要通过示例来了解如何将 Azure 事件网格与 Azure 机器学习配合使用，请参阅[创建事件驱动的机器学习工作流](/azure/machine-learning/service/how-to-use-event-grid)
