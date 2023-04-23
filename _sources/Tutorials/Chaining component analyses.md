# 链接组件分析

[<img src="media/Dapta-Brandmark-RGB.svg" alt="dapta" width="25px" height="25px"> 加载教程到dapta应用程序](https://app.daptaflow.com/tutorial/3).
[<img src="media/github.svg" alt="github" width="25px" height="25px"> 在Github中查看文件](https://github.com/daptablade/docs/tree/master/mynewbook/Tutorials/parametric_wing_model).

**预计时间：45分钟**

在本教程中，我们探讨了{term}`连接`的用法：将{term}`组件`的执行链接起来。
该示例还介绍了一个关于有限元分析的特定组件API ：`calculix-fea-comp`。

```{image} media/parametric-model-1.png
:alt: chained process
:class: bg-primary mb-1
:width: 700px
:align: center
```

## 创建组件

本示例复制了[参数化机翼模型系列](https://youtube.com/playlist?list=PL3ZV4Vo-Sjze6-DaoPgJcIvU-uAYB7KpZ)
第1与2部分中介绍的链接分析。我们利用Python和开源软件[CalculiX GraphiX](http://www.dhondt.de/) 
创建了一个受静态尖端载荷作用的复合材料机翼的参数化有限元模型，
然后使用[CalculiX CrunchiX](http://www.dhondt.de/)进行分析。

我们展示了如何将之前的单体Python代码分割成离散的、具有潜在重用价值的{term}`组件`。
详细的Python代码在之前的视频和相关博客文章中已经涵盖
（见[参考文献1和2](tutorials-chaining-component-analyses-references)）。

我们将参数化模型分析过程分为三个不同的{term}`组件`，如下图所示。

```{image} media/parametric-model-2.png
:alt: chained-process-components
:class: bg-primary mb-1
:width: 700px
:align: center
```

### 参数化模型组件

参数化模型组件使用Python定义参数化三维机翼几何形状。它还会为Calculix GraphiX输出.fdb文件格式的网格生成指令。

该组件的参数包括机翼的展长和弦长，以及通过上传包含 x 和 y 截面坐标的CSV输入文件定义的翼型横截面。

创建组件：

* 在工作区中单击右键，选择`添加空节点`。选择空组件进行编辑。
* 在`属性`选项卡中，填写组件名称为`parametric-model`，并选择通用的Python组件API`generic-python3-comp:latest`。
* 将下面的`setup.py`、`compute.py`、`requirements.txt`和`naca0012.csv`文件内容复制到文本编辑器中，并在本地保存。
然后在`属性`选项卡下上传前三个文件，在`参数`选项卡下通过选择`上传用户输入文件`来上传`naca0012.csv`文件。
* 在`属性`选项卡中，勾选`开始节点`选项的复选框。
* 将下面的参数JSON对象内容复制到`参数`选项卡的文本框中。
* 将以下JSON对象复制到`输出`选项卡的文本框中：

```{code}
{
  "files.cgx_file": "default"
}
```

* 选择`保存数据`保存并关闭组件。

(tutorials-chained_components-parametric_model-files)=
`````{tab-set}
````{tab-item} setup
```{literalinclude} ./parametric_wing_model/parametric-model/setup.py
:language: python
```
````
````{tab-item} compute
```{literalinclude} ./parametric_wing_model/parametric-model/compute.py
:language: python
```
````
````{tab-item} requirements
```{literalinclude} ./parametric_wing_model/parametric-model/requirements.txt
:language: text
```
````
````{tab-item} naca0012
```{literalinclude} ./parametric_wing_model/parametric-model/naca0012.csv
:language: text
```
````
````{tab-item} parameters
```{literalinclude} ./parametric_wing_model/parametric-model/parameters.json
:language: json
```
````
`````

### Calculix-fea组件

该组件首先在参数化模型组件的.fdb文件上批处理执行CalculiX GraphiX，生成有限元模型网格文件（all.msh）。
然后，使用Python生成复合材料壳体截面属性并写入文件（composite_shell.inp），
再使用CalculiX CrunchiX执行模型的有限元分析（FEA）。

该组件的输出：机翼尖部的网格节点挠度（FEA输出文件【ccx_static_tip_shear.dat】），
以及模型网格文件和节点集定义文件，都将被用于下一个组件中的有限元分析结果后处理。

在这里，我们使用一个特定于应用的组件API称为【calculix-fea-comp】，它确保Calculix被安装在本地计算环境。
它还允许我们通过从计算函数中的`calculix`模块导入`execute_cgx`和`execute_fea`方法从Python中执行CalculiX。
在所有其他方面，该API与通用Python组件API`generic-python3-comp`相同。

创建组件：

* 在工作区中单击右键并选择`添加空节点`。选择空组件并编辑。

* 在`属性`选项卡中填写组件名称为`calculix-fea`，并选择组件API`calculix-fea-comp:latest`。

* 将下面的`setup.py`、`compute.py`、`requirements.txt`和`ccx_static_tip_shear.inp`文件的内容复制到文本编辑器中，
并在本地保存。然后在`属性`选项卡下上传前3个文件，在`参数`选项卡下通过选择`上传用户输入文件`上传最后一个文件。

* 将下面的参数JSON对象的内容复制到`参数`选项卡文本框中。

* 将以下JSON对象复制到`输入`选项卡文本框中：

```{code}
{
  "files.cgx_file": "default",
  "fibre_rotation_angle.ORI_0.1": 0
}
```

* 将以下JSON对象复制到`输出`选项卡文本框中：

```{code}
{
  "files.analysis_output_file": "default",
  "files.mesh_file": "default",
  "files.nodeset_file": "default"
}
```

* 选择`保存数据`以保存并关闭组件。

(tutorials-chained_components-calculix_fea-files)=
`````{tab-set}
````{tab-item} setup
```{literalinclude} ./parametric_wing_model/calculix-fea/setup.py
:language: python
```
````
````{tab-item} compute
```{literalinclude} ./parametric_wing_model/calculix-fea/compute.py
:language: python
```
````
````{tab-item} requirements
```{literalinclude} ./parametric_wing_model/calculix-fea/requirements.txt
:language: text
```
````
````{tab-item} ccx_static_tip_shear
```{literalinclude} ./parametric_wing_model/calculix-fea/ccx_static_tip_shear.inp
:language: text
```
````
````{tab-item} parameters
```{literalinclude} ./parametric_wing_model/calculix-fea/parameters.json
:language: json
```
````
`````

### 结果处理组件

执行链中的最后一个组件读取有限元分析输出，并计算机翼尖端网格节点处的挠度和扭转。
该数据作为一个包括六个条目的Python字典输出：全局x、y和z中的三个挠度（“U”）以及绕x、y、z旋转的三个转角（“R”）。

创建组件：

* 在工作区中单击右键并选择`添加空节点`。选择空组件并进行编辑。

* 在`属性`选项卡中，填写组件名称`fea-results-processor`，并选择组件API`generic-python3-comp:latest`。

* 从下面的内容中复制`setup.py`、`compute.py`、`requirements.txt`文件的内容到文本编辑器中，并在本地保存。
然后在`属性`选项卡下上传它们。

* 在`属性`选项卡中勾选`结束节点`选项复选框。

* 将以下JSON对象复制到`输入`选项卡文本框中：

```{code}
{
  "files.analysis_output_file": "default",
  "files.mesh_file": "default",
  "files.nodeset_file": "default"
}
```

* 将以下JSON对象复制到`输出`选项卡文本框中：

```{code}
{
    "Ux": "default",
    "Uy": "default",
    "Uz": "default",
    "Rx": "default",
    "Ry": "default",
    "Rz": "default"
}
```

* 选择`保存数据`以保存编辑并关闭组件界面。

```{note}
现在请记得通过选择界面控件`下载`来保存会话数据。
```

(tutorials-chained_components-results_processor-files)=
`````{tab-set}
````{tab-item} setup
```{literalinclude} ./parametric_wing_model/fea-results-processor/setup.py
:language: python
```
````
````{tab-item} compute
```{literalinclude} ./parametric_wing_model/fea-results-processor/compute.py
:language: python
```
````
````{tab-item} requirements
```{literalinclude} ./parametric_wing_model/fea-results-processor/requirements.txt
:language: text
```
````
`````

## 创建连接

我们现在可以连接我们创建的各个{term}`组件`，以确保预期的输入和输出在它们之间有效传递。{term}`连接`被定义为从{term}`组件`输出句柄到另一个{term}`组件`输入句柄的数据链接。
 
### 第一次连接 

通过选择参数化模型组件的输出句柄并将线条拖动到calculix-fea组件的【files.cgx_file】输入句柄，创建第一个连接。将鼠标悬停在句柄上，可以看到相关输入或输出数据的名称出现在组件下面。

默认情况下，一个新连接是【设计变量】类型连接（黑色线），不能用于传输文件（有关详细信息，请参见仪表板参考部分）。

在工作区中选择{term}`连接`并编辑它。在`属性`选项卡下，通过选择相应的单选按钮，
将{term}`连接`类型选项从默认的【设计变量】更改为【隐式变量或文件】。

选择`保存数据`以保存更改并关闭连接界面。检查连接线颜色是否变为绿色，这表示一种【隐式】类型的数据连接。

```{note}
默认情况下，连接标签设置为相关输出句柄数据的名称。实际上，没有要求输入和输出数据名称必须匹配。
因此，如果我们不小心的话，很容易连接错误！
```

### 更多连接

以相同的方式，在计算calculix-fea组件和fea-results-processor组件之间创建3个连接，将具有匹配数据名称的句柄链接起来。

编辑所有3个连接，将它们修改为【隐式】类型数据连接，因为我们传输文件而不是设计变量。

检查所有3个组件旁边是否出现绿色勾号，以指示它们是有效的。

现在通过从界面控件中选择`下载`来保存会话数据。

```{note}
四个连接对象应该出现在会话数据文件的'connections'键下。   
```

## 执行工作流程

现在，我们可以通过选择{term}`运行`控制界面中的运行符号 ▶ 来执行链接组件的分析运行。

一旦运行开始，每个组件将会被设置然后依次执行。设置顺序是任意的，
但是计算函数总是会从【起始节点】到【结束节点】执行（有关详细信息，请参见控制面板参考部分）。

一旦fea-results-component计算完成后，{term}`运行`应该就完成了。

## 检查输出

{term}`运行`日志总结了组件的输出。通过选择界面控件中的`查看日志`来打开日志。

向下滚动到【run_output】部分，可以看到它包含了所有三个组件按照执行顺序排列的计算函数输出信息，
如下图所示。最后一条信息包含了预期的翼尖挠度和转角数据。

也需要检查所有3个组件的`日志`选项卡，并下载文件快照以查看所有组件的输入、连接和输出文件。

现在，通过选择界面控件中的`下载`来保存会话数据和{term}`运行`日志。

```{note}
【文件快照】zip文件夹中出现的【连接】文件夹包含任何传入的连接文件。
这是为了将它们与用户文件存储系统中的参数化和API输入文件区分开来，其中这些文件存在于指向上游组件文件的符号链接中，
以提高效率。然而，在计算函数开始之前，连接文件会被复制到`parameters["inputs_folder_path"]`中。
```

```{image} media/parametric-model-3.png
:alt: run log
:class: bg-primary mb-1
:width: 700px
:align: center
```
## 清理

通过选择界面中的`创建`来删除您的会话，可能需要一分钟左右在云端重置会话。

```{warning}
当您即将删除一个{term}`运行`时，您应该会看到一个警告消息。如果您选择继续，则所有的{term}`运行`数据（会话数据、输入和输出）将被永久删除。
```

(tutorials-chaining-component-analyses-references)=
## 参考文献

1. [Parametric FEM model creation with Python and CalculiX GraphiX (cgx)](https://www.dapta.com/parametric-fem-model-creation-with-python-and-calculix-graphix-cgx/)
2. [Automated FEM analysis and output processing with Python and CalculiX CrunchiX (ccx)](https://www.dapta.com/automated-fem-analysis-and-output-processing-with-python-and-calculix-crunchix-ccx/)