# 简单组件分析

[<img src="media/Dapta-Brandmark-RGB.svg" alt="dapta" width="25px" height="25px"> 加载教程到dapta应用程序](https://app.daptaflow.com/tutorial/1).
[<img src="media/github.svg" alt="github" width="25px" height="25px"> 在Github中查看文件](https://github.com/daptablade/docs/tree/master/mynewbook/Tutorials/paraboloid).

```{image} media/paraboloid_rotation.gif
:alt: paraboloid-surface
:class: bg-primary mb-1
:width: 400px
:align: right
```

**预计时间：10分钟**

我们的第一个示例将创建和分析一个简单的解析函数组件。  

开始工作所需的全部工具仅仅是一个文本编辑器和一个网页浏览器。无需下载或安装任何软件！
然而，由于我们将在所有示例中将会使用Python，因此您需要基本了解Python语法以及安装使用支持Python的代码编辑器（例如VS Code和PyCharm等）。

## 组件描述
抛物面组件求解以下函数：

$$
  f(x,y) = (x-3.0)^2 + x y + (y + 4.0)^2 -3.0 
$$

其中，x和y是组件的输入，f(x,y)是组件的输出。该函数的最小值位于

$$
  x = \frac{20}{3} \quad , \quad y = -\frac{22}{3}
$$

## 创建组件

单击[控制面板](https://app.daptaflow.com/Interface)，通过选择`新建`创建一个空白工作区。

在空白工作区域，单击鼠标右键并选择`添加空节点`，从而创建一个空模板组件。

选择该组件进行编辑。 

### 属性

在【属性】选项卡下，可以打开组件接口。这里是您定义组件核心属性的地方。填写组件名称为`paraboloid`，并选择`generic-python3-comp:latest`作为组件的API（应用程序接口）。

按Tab键或单击组件接口内部以验证所选API。这将触发API输入文件列表的显示，如下图所示。

```{image} media/paraboloid_1.png
:alt: properties-tab-empty
:class: bg-primary mb-1
:width: 700px
:align: center
```

```{Note}
{term}`组件`名称只应包括小写字母a-z，数字0-9和端横线（-），不应包含空格。
```

```{Warning}
单击组件界面之外的区域会关闭组件，并且不会自动保存更改。
```

我们可以看到，`generic-python3-comp`API有三个输入文件：

* `setup.py`
* `compute.py`
* `requirements.txt`

前两个文件是必需的，最后一个是可选的。您可以查看下面给出的关于拋物面示例的文件内容。
这里我们不需要`requirements.txt`文件，因为Python代码不导入任何第三方Python程序包。
我们将在下一个示例[简单优化问题](./Simple%20optimisation%20problem.md)中使用它来导入openMDAO的类和函数。

`setup.py`模块必须包含一个**setup**（设置）函数，该函数将返回在计算函数过程中可用的数据。
这里，我们使用[参数](tutorials-paraboloid-parameters)选项卡下设定的输入字典中的值初始化 x 和 y 的输入。我们同时也初始化输出和偏微分（函数梯度），该部分仅在下一个示例中需要。

`compute.py`模块必须包含一个 **compute** （计算）函数，该函数返回一个数据字典，通常包括组件输出字典。本示例中的计算函数从输入字典中存储的 x 和 y 值计算 f（x，y）。再次强调，偏微分计算仅在下一个示例中被需要。

(tutorials-paraboloid-files)=
`````{tab-set}
````{tab-item} setup
```{literalinclude} ./paraboloid/setup.py
:language: python
```
````
````{tab-item} compute
```{literalinclude} ./paraboloid/compute.py
:language: python
```
````
`````

对于以上每个文件，将其内容复制到文本编辑器中并保存在本地设备。确保在文件名中包括'.py'扩展名。

接下来，在组件界面中选择`setup.py`以上传相应的文件。应该会出现一个绿色的勾号和一个文件上传的时间戳，以确认上传成功。重复以上步骤上传`compute.py`文件。

勾选`开始节点`和`结束节点`选项旁边的复选框（因为在此{term}`运行`中我们只有一个组件），如下图所示。

```{image} media/paraboloid_2.png
:alt: properties-tab-completed
:class: bg-primary mb-1
:width: 700px
:align: center
```

最后，选择`保存数据`以保存组件并关闭组件界面。

在您的工作区中，组件名称应该也已更新，并且将出现绿色勾号，以表示该组件是有效的。
但是，如果您现在尝试运行它，您将会收到报错消息，
因为我们实际上尚未定义Python函数的参数和输入 - 请参见下文。

```{warning}
尽管您刚刚'保存'了组件，但您工作区的内容实际上并没有保存在任何地方，如果您现在刷新网页或关闭浏览器，您将会丢失您的作业信息。为避免这种情况，您应该定期保存已编辑的组件，然后选择工作区`下载`按钮，保存您会话的JSON格式版本（请参见用户手册中的相关部分）。
```

(tutorials-paraboloid-parameters)=
### 参数

请再次选择要编辑的组件，然后选择`参数`选项卡并将以下 JSON 对象复制到文本框中。选择`保存数据`以保存编辑。

```{code}
{
  "x": 5,
  "y": 5,
  "f_xy": 0
}
```

我们将参数定义为组件执行所需的值，但它们不是来自其他{term}`组件`的输入。
例如，参数可以是常量值或与应用程序相关的输入文件。对于这个抛物面示例，
我们使用{term}`参数`选项卡定义了一些默认的组件输入和输出值，这些值用于在设置函数中初始化组件。
要更全面地使用参数，请参见示例[Chaining component analyses](./Chaining%20component%20analyses.md)。

### 输入与输出

打开组件以编辑它，并将以下 JSON 对象添加到`输入`和`输出`选项卡中的文本框中。
通过将抛物面函数的输入和输出分别定义为{term}`组件`的输入和输出，
我们可以将这些值暴露给其他{term}`运行`组件，
例如驱动程序。我们将在下一个教程示例[Simple optimisation problem](./Simple%20optimisation%20problem.md)中探讨这个问题。

输入句柄:
```{code}
{
  "x": "default",
  "y": "default"
}
```

输出句柄：
```{code}
{
  "f_xy": "default"
}
```

我们将在下一节中讨论`日志`选项卡。

选择`保存数据`以保存组件并关闭它。现在，您应该能够在工作区组件的左侧和右侧看到一些输入和输出句柄。将鼠标光标悬停在句柄上以查看变量名称。

## Component analysis 

All being well, you should now be able to launch a {term}`Run` by selecting the play symbol ▶ in the {term}`Run` controls interface. 
The control symbols will start to fade in and out as your {term}`Run` is sent for analysis in the Cloud, this may take a few minutes the first time. 
Eventually, the {term}`Run` should execute (this will happen very quickly) and you should see an alert window confirming that 'The {term}`Run` has completed successfully'. 
If you don't get any messages, try to refresh your web browser page, or consult the [FAQ](../Reference/FAQs.md) section for troubleshooting suggestions. 

We can now inspect the outputs of the {term}`Run`. 

### The Run Log

Select `View Log` in the interface controls to view a summary of the {term}`Run` as a nested JSON text object as shown below. 

The 'time' entry corresponds to the time the Run Log file was generated, while the time stamps in the messages that appear in the 'run_output' and 'paraboloid' relate to the paraboloid compute function execution time. The inputs and outputs of the paraboloid component are available under the corresponding 'paraboloid' entries.   

To save a copy of the Run Log, select `Close` to return to the workspace view and then select `Download`. This should download two JSON files: the Run Log as 'runlog.json' and the copy of your work session as 'dapta_input.json'. 

```{image} media/paraboloid_3.png
:alt: run-log
:class: bg-primary mb-1
:width: 700px
:align: center
```

### The Component Log

Select the component again and navigate to the `Log` tab as shown below. 

Both the Run Log and the Component Log are updated as the {term}`Run` executes, which allows us to monitor progress and view intermediary results.
The Component Log lists events related to the component in order of time of occurrence. A 'SETUP' event corresponds to the execution of the component's setup function and a 'COMPUTE' event corresponds to the execution of the compute function, as defined in the `setup.py` and `compute.py` modules. The event name is followed by a number, that indicates the number of times the component has been executed during the current {term}`Run`. Note that the Component Log is not cleared between successive Runs, but it will clear if you refresh the page. 

The Component Log has another important function: if errors occur during the execution of the component, the Log will list an 'ERROR' event with a description of the error message and traceback information.  

The `Log` tab also includes a `download files snapshot` link. Select this to download a zip file that contains all input and output files as they currently exist in your workspace for this component. 
Save this data, along with the JSON formatted version of your session ('dapta_input.json') and a copy of the {term}`Run` Log ('runlog.json'), to allow you to re-load this example in the future, or to compare inputs and outputs with other Runs. 

```{image} media/paraboloid_4.png
:alt: component-log
:class: bg-primary mb-1
:width: 700px
:align: center
```

## Editing a Run

Once you have successfully started a {term}`Run`, the session and all associated data will be saved in the Cloud until you decide to overwrite or delete it. 

A {term}`Run` can only be edited once it has been stopped (using the stop symbol ⏹) or once it has completed successfully. The second time a {term}`Run` is started it will execute much faster since the processes are already set up in the Cloud, except if you renamed, added or removed components, in which case the existing {term}`Run` will need to be deleted first. Try this by editing the x and y values in the paraboloid component Parameters and starting the {term}`Run` again.    

## Clean-up

You can delete a session by creating a new session (select `New` in from the interface controls) or by loading a JSON session file from your machine (select `Open`). 
It may take a minute or so for the Cloud session to be reset. 

```{warning}
You should see a warning message whenever you are about to delete a {term}`Run`. If you select to continue, then all the {term}`Run` session data (Run log and component logs) will be permanently deleted. 
```

## References

1. ['Paraboloid - A Single-Discipline Model'](https://openmdao.org/newdocs/versions/latest/basic_user_guide/single_disciplinary_optimization/first_analysis.html) example from the openMDAO user guide.