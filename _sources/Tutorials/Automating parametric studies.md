# 自动化复材机翼模型参数研究 

[<img src="media/Dapta-Brandmark-RGB.svg" alt="dapta" width="25px" height="25px"> 加载教程到dapta应用程序](https://app.daptaflow.com/tutorial/4).
[<img src="media/github.svg" alt="github" width="25px" height="25px"> 在Github中查看文件](https://github.com/daptablade/docs/tree/master/mynewbook/Tutorials/parametric_wing_model).

**预计时间：15分钟**

驱动程序组件可用于自动化复杂分析工作流程。

在本教程中，我们使用前面示例中的链式组件分析来自动执行参数研究。

```{image} media/driver-parametric-model-1.png
:alt: chained process
:class: bg-primary mb-1
:width: 700px
:align: center
```
(tutorials-automating-parametric-studies-opening-a-saved-session)=
## 打开已保存的会话

首先，加载之前示例的会话文件。

从界面控件中选择`打开`以加载我们先前会话的JSON格式版本（dapta_input.json）。
或者，将下面的JSON对象复制到文本编辑器中并在本地保存，然后选择`打开`以加载它。
工作区中应该会出现三个连接的组件。

````{dropdown} dapta_input.json
```{literalinclude} ./parametric_wing_model/1_Chaining_component_analyses/dapta_input.json   
:language: json
```
````

组件名称旁边的问号表示它们缺少一些数据。要完全定义它们，我们需要上传所有组件的`setup.py`，`compute.py`，`requirements.txt`和用户输入文件，正如之前的示例所述。

以下是[链接组件分析](./Chaining%20component%20analyses.md)示例文件内容的快速链接：

* [Parametric model component](tutorials-chained_components-parametric_model-files)
* [Calculix-fea component](tutorials-chained_components-calculix_fea-files)
* [Results processor component](tutorials-chained_components-results_processor-files)

上传文件后，请检查所有组件是否有效。在工作区中，组件名称旁应显示绿色勾号。

## 自动化一个参数研究

改变复合材料的属性对翼尖挠度的影响是什么？

为了回答这个问题，我们可以进行一个参数研究。
我们针对一系列复合材料属性输入执行链接分析，并记录输出的平均翼尖挠度和转角。
此示例基于[参考文献 1](tutorials-automating-parametric-studies-references)中的纯Python实现。

### 驱动程序组件

感兴趣的设计变量是`calculix-fea`输入变量`fibre_rotation_angle.ORI_0.1`，我们之前将它设置为零（纤维与翼展方向对齐）。驱动程序`compute.py`函数必须执行以下任务：

1. 在一系列变量值上进行迭代，变量取值范围通过组件`参数`选项卡中的`rotation_min`（-10度）到`rotation_max`（10度）的值来定义，并设置增量`rotation_inc`（5度）；
2. 在每次迭代中，调用`run_workflow`函数以使用当前变量值执行链接的组件分析；
3. 最后，绘图函数(`_plot_study_results`)生成翼尖挠度作为变量值函数的线性图。

```{note}
请注意，即使输入不变，`参数模型`组件在每个工作流迭代中都会被执行。为了减少与此组件相关的计算量，
我们可以将`参数模型`计算函数逻辑移动到设置函数中，该函数仅执行一次。
```

创建驱动组件的方法如下：

* 在工作区中单击右键，选择`添加空节点`。选择空组件并进行编辑。

* 在`属性`选项卡中，填写组件名称为`parametric-study-driver`，并选择组件API`generic-python3-driver:latest`。

* 从以下文件中复制`setup.py`、`compute.py`、`requirements.txt`文件的内容到文本编辑器中，
然后在`属性`选项卡下上传它们。

* 在`属性`选项卡中，勾选`驱动程序`选项的复选框。

* 将下面的参数JSON对象的内容复制到`参数`选项卡文本框中。

* 选择`保存数据`保存并关闭组件。

`````{tab-set}
````{tab-item} setup
```{literalinclude} ./parametric_wing_model/parametric-study-driver/setup.py
:language: python
```
````
````{tab-item} compute
```{literalinclude} ./parametric_wing_model/parametric-study-driver/compute.py
:language: python
```
````
````{tab-item} requirements
```{literalinclude} ./parametric_wing_model/parametric-study-driver/requirements.txt
:language: text
```
````
````{tab-item} parameters
```{literalinclude} ./parametric_wing_model/parametric-study-driver/parameters.json
:language: json
```
````
`````

### 执行工作流程

现在我们可以通过在运行控件界面中选择【运行】符号 ▶ 来执行参数研究。

一旦运行开始，每个组件将会被设置和依次执行。设置顺序是任意的，
但计算函数将始终从【开始节点】到【结束节点】执行（有关详细信息，请参阅控制面板参考部分）。

{term}`运行`应在连接组件的5次迭代（或`parametric-study-driver`组件的1次迭代）后完成。

### 检查输出

{term}`运行`日志总结了组件的输出。通过在界面控件中选择`查看日志`来打开日志。

我们可以看到每个组件的计算函数都执行了5次。【un_output】（在日志末尾）仅列出了第5个工作流迭代的计算输出信息。

接下来，关闭{term}`运行`日志，选择`parametric-study-driver`组件。然后选择`日志`选项卡，单击`下载文件快照`。
 
参数研究的输出被总结在【outputs】文件夹中的【results_plot.png】和【results_plot.png】图表。

```{image} media/driver-parametric-model-2.png
:alt: results-plot
:class: bg-primary mb-1
:width: 500px
:align: center
```

## 清理

通过选择界面中的`创建`来删除您的会话，可能需要一分钟左右在云端重置会话。

```{warning}
当您即将删除一个{term}`运行`时，您应该会看到一个警告消息。
如果您选择继续，则所有的{term}`运行`数据（会话数据、输入和输出）将被永久删除。
```

(tutorials-automating-parametric-studies-references)=
## 参考文献

1. [Automated FEM analysis and output processing with Python and CalculiX CrunchiX (ccx)](https://www.dapta.com/automated-fem-analysis-and-output-processing-with-python-and-calculix-crunchix-ccx/)