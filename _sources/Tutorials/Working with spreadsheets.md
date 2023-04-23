# 使用电子表格仿真

[<img src="media/Dapta-Brandmark-RGB.svg" alt="dapta" width="25px" height="25px"> 加载教程到dapta应用程序](https://app.daptaflow.com/tutorial/6).
[<img src="media/github.svg" alt="github" width="25px" height="25px"> 在Github中查看文件](https://github.com/daptablade/docs/tree/master/mynewbook/Tutorials/libreoffice).

**预计时间：15分钟**

在这个例子中，我们探索了在仿真工作流中**电子表格和宏**的使用。

我们复制了[简单优化问题](./Simple%20optimisation%20problem.md)的仿真工作流程，
但这次使用电子表格组件`libreoffice-comp`来计算抛物面函数，而不是纯Python组件。

没有时间亲自尝试这个示例吗？那就看**[视频教程](https://youtu.be/2GaVVrot-4I)**吧。

```{image} media/spreadsheet_1.png
:alt: paraboloid-spreadsheet
:class: bg-primary mb-1
:width: 700px
:align: center
```

## 组件说明

像Microsoft's Excel®文件一样，电子表格在工程领域被广泛使用。

在仿真工作流程中，电子表格可用于：

* 导入数据，例如来自实验的数据；
* 自动对输入数据进行计算；
* 记录、可视化和存储输出。

组件API，电子表格可以轻松集成到任何dapta{term}`运行`中。

`libreoffice-comp`包括[LibreOffice](https://www.libreoffice.org/)的完整安装版，
其中包括电子表格编辑器（Calc）。Calc可以打开和保存其本地的Open Document Format (.ods)文件，
以及Microsoft Excel®格式(.xls)文件，并且它也兼容宏（当前支持的语言是LibreOffice Basic、BeanShell、JavaScript和Python，同时还支持导入Microsoft VBA宏）。了解更多有关编写LibreOffice宏的信息，
我们建议查看[入门指南](https://books.libreoffice.org/en/GS70/GS7013-GettingStartedWithMacros.html)。参考文献1也为Calc的Python宏提供了很好的介绍。

请在这里下载示例电子表格（如上图所示）：[paraboloid.ods](https://github.com/daptablade/docs/raw/master/mynewbook/Tutorials/libreoffice/paraboloid.ods)。

它包括一个VBA宏，每当电子表格的x和y输入被更新时，
自动计算[简单组件分析](./Simple%20component%20analysis.md)示例中的抛物面函数f(x,y)。
电子表格还包含一个最初为空的优化历史记录表，该表将用于存储和绘制{term}`运行`期间的优化历程数据。

## 打开已保存的会话

由于我们之前已经创建和分析了抛物面组件，我们可以加载先前的会话以加快速度。

从界面控件中选择`打开`以加载我们先前会话的JSON格式版本（dapta_input.json）。
或者，将以下对象复制到文本编辑器中并在本地保存，然后选择`打开`以加载它。

```{literalinclude} ./paraboloid/dapta_input.json   
:language: json
```

抛物面组件应该已经出现在工作区，但组件名称旁边的问号表示它缺少一些数据。

## 更新抛物面组件

将以下新的`setup.py`和`compute.py` API文件的内容复制到文本编辑器中，并将文件保存到本地：

* `setup.py`函数与先前的函数类似，但它还将电子表格文件复制到输出文件夹中，并通过调用`start_libreoffice`函数以无头模式启动LibreOffice。

* `compute.py`函数打开电子表格，将输入的x和y值写入输入单元格（这将自动执行电子表格宏），然后将当前的x，y和计算出的f(x，y)值复制到优化历程记录表中。这也会自动更新线条图。最后保存并关闭电子表格。 

在工作区中选择【抛物面组件】并打开它。

更新`属性`选项卡：

1. 从API下拉菜单中选择`libreoffice-comp:latest`

2. 通过单击相应的链接上传新的`setup.py`和`compute.py` API文件

然后，更新`参数`选项卡：

1. 将以下 键/值 添加到JSON对象中：`ods_file：paraboloid.ods`
2. 通过选择`上传用户输入文件`来上传输入电子表格（paraboloid.ods）。

通过选择`保存数据`按钮保存并关闭组件。

您可以通过执行{term}`运行`来检查组件是否按预期工作，或者在启动{term}`运行`之前添加下一部分的驱动程序组件。

`````{tab-set}
````{tab-item} setup
```{literalinclude} ./libreoffice/setup.py
:language: python
```
````
````{tab-item} compute
```{literalinclude} ./libreoffice/compute.py
:language: python
```
````
`````

## 添加驱动程序组件

我们将重复使用之前在[简单优化问题](./Simple%20optimisation%20problem.md)中使用的OpenMDAO驱动程序。
我们调整驱动程序参数以解决这个优化问题：

* 过在驱动程序参数中设置`approx_totals: true`和`fd_step: 0.0001`来计算链接组件全微分（使用有限差分）。
* 通过将【lot_history】选项添加到【visualise】参数列表中来显示优化迭代历程记录图。

创建驱动程序组件：

* 在工作区中单击右键并选择`添加空节点`。选择空组件进行编辑。

* 在`属性`选项卡中，填写组件名称为`open-mdao`，并选择组件API`generic-python3-driver:latest`。

* 将以下`setup.py`，`compute.py`，`requirements.txt`文件的内容复制到文本编辑器中，并在本地保存。然后在`属性`选项卡下上传它们。

* 在`属性`选项卡中，勾选`驱动程序`选项旁边的复选框。

* 将以下参数JSON对象的内容复制到`参数`选项卡文本框中。

* 将下面的`om_component.py`文件的内容复制到文本编辑器中并将其保存到本地。
然后通过选择`上传用户输入文件`在`参数`选项卡下上传它。

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
```{literalinclude} ./libreoffice/driver_parameters.json
:language: json
```
````
````{tab-item} om_component
```{literalinclude} ./open-mdao-paraboloid/om_component.py
:language: python
```
````
`````

### 执行工作流程

现在，我们可以通过在运行控件界面中【运行】符号 ▶ 来执行优化设计。

与之前一样，在13次paraboloid组件迭代（1次open-mdao组件迭代）后，{term}`运行`完成。

### 检查输出

{term}`运行`日志总结了组件的输出。通过在界面控件中选择`查看日志`来打开日志。
日志的【un_output】条目（在日志的末尾）应该显示【OpenMDAO计算已完成】。

接下来，关闭{term}`运行`日志并选择paraboloid组件。然后选择`日志`选项卡并单击`下载文件快照`。

打开下载的zip文件夹并在outputs文件夹下打开【araboloid.ods】输出电子表格。
优化历程数据应显示为左侧的0到12次迭代。此数据还应在右侧的线图中绘制。
历史数据包括用于计算SLSQP算法使用的有限差分梯度的主迭代、次迭代（线搜索）和函数评估。

同样，您可以检查open-mdao组件的输出，其中应包括显示主要迭代的收敛历程线图。

最后，您可以通过选择界面控件中的`下载`来保存会话数据和运行日志。

## 清理

通过选择界面中的`创建`来删除您的会话，可能需要一分钟左右在云端重置会话。


```{warning}
当您即将删除一个{term}`运行`时，您应该会看到一个警告消息。如果您选择继续，
则所有的{term}`运行`数据（会话数据、输入和输出）将被永久删除。
```
(tutorials-working-with-spreadsheets-references)=
## 参考文献

1. [Interface-oriented programming in OpenOffice / LibreOffice : automate your office tasks with Python Macros, 06/12/2015](http://christopher5106.github.io/office/2015/12/06/openoffice-libreoffice-automate-your-office-tasks-with-python-macros.html)