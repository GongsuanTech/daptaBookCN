# 自动化复材机翼模型优化设计

[<img src="media/Dapta-Brandmark-RGB.svg" alt="dapta" width="25px" height="25px"> 加载教程到dapta应用程序](https://app.daptaflow.com/tutorial/5).
[<img src="media/github.svg" alt="github" width="25px" height="25px"> 在Github中查看文件](https://github.com/daptablade/docs/tree/master/mynewbook/Tutorials/parametric_wing_model).

**预计时间：15分钟**

驱动程序组件可用于自动化复杂分析工作流程。

在先前的示例中，我们对单个变量进行了参数研究。在本教程中，
我们进一步使用了来自[简单优化问题](./Simple%20optimisation%20problem.md)示例的OpenMDAO优化组件，
以使机翼设计达到翼尖最小翼展角。

```{image} media/open-mdao-parametric-model-1.png
:alt: chained process with optimiser
:class: bg-primary mb-1
:width: 700px
:align: center
```

## 打开已保存的会话

请参考前一个示例中[打开以保存会话](tutorials-automating-parametric-studies-opening-a-saved-session)
部分给出的处理流程。

## 自动化一个优化设计研究

设计目标是确定哪种复合纤维角度可以最小化挠曲翼展角（“Ry”），同时满足最大允许垂直挠度（“Uz”）为6厘米的设计约束条件。

为了回答这个问题，我们可以进行优化设计研究。
本示例基于[参考文献 1](tutorials-automating-design-optimisations-references)中的纯Python实施。

### 驱动程序组件 

与先前的示例一样，感兴趣的变量是`calculix-fea`输入变量`fibre_rotation_angle.ORI_0.1`。
驱动程序组件与[简单优化问题](./Simple%20optimisation%20problem.md)示例中使用的OpenMDAO优化组件相同，
只是调整了用于机翼优化问题的驱动参数：

* 【input_variables】和【output_variables】参数设置了优化设计变量、目标函数和约束函数。
* 通过在驱动程序参数中设置`【approx_totals】: true`和`【fd_step】: 1.0`实现夸链接组件全微分计算（使用有限差分法）。
* 通过在【visualise】参数列表中添加【plot_history】选项输出优化迭代历程图。

创建驱动程序组件：

* 在工作区中单击右键并选择`添加空节点`。选择空组件并编辑它。

* 在`属性`选项卡中，填写组件名称为`open-mdao`，并选择组件API`generic-python3-driver:latest`。

* Then upload them under the `Properties` tab. 将下面的`setup.py`、`compute.py`和`requirements.txt`文件内容复制到文本编辑器中，并将其保存到本地设备。然后在`属性`选项卡下上传它们。

* 在`属性`选项卡中勾选`驱动程序`选项旁边复选框。

* 将下面的参数JSON对象的内容复制到`参数`选项卡文本框中。

* 将下面的`om_component.py`文件内容复制到文本编辑器中，并在本地保存。然后通过选择【上传用户输入文件】将其上传到【参数】选项卡下。

* 选择`保存数据`保存并关闭组件。

`````{tab-set}
````{tab-item} setup
```{literalinclude} ./open-mdao-paraboloid/setup.py
:language: python
```
````
````{tab-item} compute
```{literalinclude} ./open-mdao-paraboloid/compute.py
:language: python
```
````
````{tab-item} requirements
```{literalinclude} ./open-mdao-paraboloid/requirements.txt
:language: text
```
````
````{tab-item} parameters
```{literalinclude} ./parametric_wing_model/3_Automating_the_design_optimisation/driver_parameters.json
:language: json
```
````
````{tab-item} om_component
```{literalinclude} ./open-mdao-paraboloid/om_component.py
:language: python
```
````
`````

### 添加设计变量与连接

尽管所有组件似乎都是有效的，但如果我们现在尝试启动运行，将会遇到故障。

通过检查驱动计算函数，我们可以看到OpenMDAO仅知道设计变量输入、输出和连接（`connection["type"] == "design"`）。
设计变量连接专门用于OpenMDAO确定组件执行顺序。

在我们可以启动运行之前，需要定义以下人工设计变量和连接。

选择 **parametric-model** 组件并更新`输入`和`输出`选项卡：

`````{tab-set}
````{tab-item} Inputs
```{code} 
{
    "input_0": 0.1
}
```
````
````{tab-item} Outputs
```{code} 
{
  "files.cgx_file": "default",
  "output_0": 1.1
}
```
````
`````

选择 **calculix-fea** 组件并更新`输入`和`输出`选项卡：

`````{tab-set}
````{tab-item} Inputs
```{code} 
{
  "files.cgx_file": "default",
  "fibre_rotation_angle.ORI_0.1": 0,
  "output_0": 1.1
}
```
````
````{tab-item} Outputs
```{code} 
{
  "files.analysis_output_file": "default",
  "files.mesh_file": "default",
  "files.nodeset_file": "default",
  "output_1": 1.1
}
```
````
`````

选择 **fea-results-processor** 组件并只更新`输入`选项卡：

`````{tab-set}
````{tab-item} Inputs
```{code} 
{
  "files.analysis_output_file": "default",
  "files.mesh_file": "default",
  "files.nodeset_file": "default",
  "output_1": 1.1
}
```
````
`````

接下来，创建两个设计变量连接：

1. 从 parametric-model 组件的【output_0】输出句柄到 calculix-fea 组件的【output_0】输入句柄。
2. 从 calculix-fea 组件的【output_1】输出句柄到 fea-results-processor 组件的【output_1】输入句柄。

### 执行工作流程
 
现在，我们可以通过在运行控制界面中选择【运行】符号 ▶ 来执行优化设计。

在链接组件执行23次迭代（`open-mdao`组件的1次迭代）后，{term}`运行`完成。

```{note}
请注意，`parametric-model`组件只会执行一次，因为人为创建的设计变量输入（【input_0】：0.1）不会更改。
```

### 检查输出

{term}`运行`日志总结了组件的输出。通过在界面控件中选择`查看日志`来打开日志。【run_output】条目（在日志的结尾处）应该指出【OpenMDAO compute completed】（OpenMDAO计算完成）。

接下来，关闭{term}`运行`日志并选择 `open-mdao` 组件。然后选择`日志`选项卡并单击`下载文件快照`。

优化研究的输出总结在【outputs】文件夹中的【run_driver.log】文件的末尾，如下图所示。
我们还可以在同一文件夹中检查设计变量、目标和约束函数的收敛历程图。

最优复合纤维角度在11个SLSQP算法迭代后收敛到约22.68度，这导致翼尖入射角为-0.0377弧度，垂直挠度为6厘米。

```{code}
Driver debug print for iter coord: rank0:ScipyOptimize_SLSQP|22
---------------------------------------------------------------
Design Vars
{'calculix_fea.fibre_rotation_angle-ORI_0-1': array([22.68391541])}

Calling compute.
message: 
 {'component': 'parametric-model', 'inputs': {'design': {'input_0': [0.1]}}, 'get_grads': False, 'get_outputs': True}
Calling compute.
message: 
 {'component': 'calculix-fea', 'inputs': {'design': {'fibre_rotation_angle.ORI_0.1': [22.683915410923078], 'output_0': [1.1]}}, 'get_grads': False, 'get_outputs': True}
Calling compute.
message: 
 {'component': 'fea-results-processor', 'inputs': {'design': {'output_1': [24.0]}}, 'get_grads': False, 'get_outputs': True}
Nonlinear constraints
{'fea_results_processor.Uz': array([0.05999995])}

Linear constraints
None

Objectives
{'fea_results_processor.Ry': array([-0.03771947])}

Optimization terminated successfully    (Exit mode 0)
            Current function value: -0.03771946528223136
            Iterations: 11
            Function evaluations: 22
            Gradient evaluations: 11
Optimization Complete
-----------------------------------
```


```{image} media/open-mdao-parametric-model-2.png
:alt: results-plot
:class: bg-primary mb-1
:width: 400px
:align: center
```
```{image} media/open-mdao-parametric-model-3.png
:alt: results-plot
:class: bg-primary mb-1
:width: 400px
:align: center
```
```{image} media/open-mdao-parametric-model-4.png
:alt: results-plot
:class: bg-primary mb-1
:width: 400px
:align: center
```

## 清理

通过选择界面中的`创建`来删除您的会话，可能需要一分钟左右在云端重置会话。


```{warning} 
当您即将删除一个{term}`运行`时，您应该会看到一个警告消息。如果您选择继续，
则所有的{term}`运行`数据（会话数据、输入和输出）将被永久删除。
```

(tutorials-automating-design-optimisations-references)=
## 参考文献

1. [Design optimisation in practice with Python, OpenMDAO and Scipy](https://www.dapta.com/design-optimisation-in-practice-with-python-openmdao-and-scipy/)