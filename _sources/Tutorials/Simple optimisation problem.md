# 简单优化问题

[<img src="media/Dapta-Brandmark-RGB.svg" alt="dapta" width="25px" height="25px"> 加载教程到dapta应用程序](https://app.daptaflow.com/tutorial/2).
[<img src="media/github.svg" alt="github" width="25px" height="25px"> 在Github中查看文件](https://github.com/daptablade/docs/tree/master/mynewbook/Tutorials/open-mdao-paraboloid).

```{image} media/paraboloid_optimisation.gif
:alt: paraboloid-optimisation 
:class: bg-primary mb-1
:width: 400px
:align: right
```

**预计时间：15分钟**

在这个示例中，我们解决了一个简单的解析函数最小化问题。我们使用[openMDAO](https://openmdao.org) Python程序包创建了一个优化驱动程序组件，然后使用它来确定[先前示例](./Simple%20component%20analysis.md)中抛物面函数的无约束和有约束最小值。

## 打开已保存的会话

由于我们之前已经创建并分析了抛物面组件，因此我们可以加载先前的会话以加快速度。

从界面控件中选择`打开`以加载我们之前会话的JSON格式版本（dapta_input.json）。
或者，将下面的对象复制到文本编辑器中并在本地保存，然后选择`打开`加载它。

```{literalinclude} ./paraboloid/dapta_input.json   
:language: json
```

抛物面组件应该出现在工作区中，但组件名称旁边的问号表示它缺少一些数据。
为了完全定义它，我们需要再次在组件`属性`选项卡下上传组件`setup.py`和`compute.py`输入文件。
文件内容在[简单组件分析](tutorials-paraboloid-files)示例下可以获得。

## 创建驱动程序组件

在工作区中单击鼠标右键，然后选择`添加空节点`。这将向您的工作区添加一个空的模板组件。

选择空组件以进行编辑。

### 属性

在`属性`选项卡中，填写组件名称`open-mdao`，
然后选择驱动程序组件API为`generic-python3-driver:latest`。

就像之前的示例一样，Python驱动程序需要上传`setup.py`和`compute.py`输入文件。
此外，我们还上传了一个`requirements.txt`文件，以便我们可以在Python代码中访问openMDAO程序包。
您可以检查下面给出的文件内容。

在`compute.py`文件中，可以看到我们的导入包括我们在`requirements.txt`文件中列出的【numpy】和【openmdao】程序包。此外，我们从`component_api2.py`模块中导入一个`call_compute`函数，
该函数专用于`generic-python3-driver`API，并允许驱动程序执行其他组件
（这是与`generic-python3-comp`API的主要区别！）。

`compute.py`模块中的最后一个导入（`来自 om_component import ...`）是特定于我们的组件，
并提供了一个关于openMDAO ExplicitComponent类的自定义实施。 
`om_component.py`模块的内容如下所示，我们将在下一节中将其上传为参数输入文件。

最后，勾选`驱动程序`选项旁的复选框，如下图所示。
 
(tutorials-open-mdao-paraboloid-files)=
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
````{tab-item} om_component
```{literalinclude} ./open-mdao-paraboloid/om_component.py
:language: python
```
````
`````

```{image} media/open-mdao-paraboloid-1.png
:alt: properties-tab-completed
:class: bg-primary mb-1
:width: 700px
:align: center
```

### 参数

选择`参数`选项卡，将'不受限制'的参数 JSON 对象复制到文本框中。

(tutorials-open-mdao-paraboloid-parameters)=
`````{tab-set}
````{tab-item} unconstrained
```{literalinclude} ./open-mdao-paraboloid/unconstrained_params.json
:language: json
```
````
````{tab-item} constrained
```{literalinclude} ./open-mdao-paraboloid/constrained_params.json
:language: json
```
````
`````

这些参数在`compute.py`模块中用于定义一个标准的openMDAO优化问题。

接下来，如前一部分所述，我们还需要上传`om_component.py`模块。
将上面的'om_component.py'选项卡中的内容复制到文本编辑器中并保存为'om_component.py'文件。
在`参数`选项卡底部选择`上传用户输入文件`链接以上传文件。如果在`参数`文本框的JSON 对象部分出现'user_input_files'相应条目，则上传成功。

通过从Python代码中提取参数并在`参数`选项卡中定义它们，
我们可以在不必查看或修改Python代码的情况下更改优化设置。
这使得跟踪输入更改和比较运行（例如通过会话文件）变得更加容易。
该组件也变得更加通用、稳健和可重复使用。在下一节中，
我们会通过将'不受限制'的参数替换为'受限制'的参数来演示这种方法的好处。

选择`保存数据`以保存编辑并关闭组件界面。

选择界面控件中的`下载`来将当前会话保存为一个本地JSON文件。

## 执行驱动程序

现在我们应该有一个包含两个组件的有效{term}`运行`：拋物面分析和open-mdao驱动程序。

通过选择{term}`运行`控件界面上的运行符号 ▶ 来执行{term}`运行`，并等待其完成。
与之前一样，这可能需要几分钟，因为您的{term}`运行`正在云端被处理。
如果您没有收到{term}`运行`成功的消息，
请尝试刷新您的浏览器页面或查阅[常见问题](../Reference/FAQs.md)解答部分以获取故障排除建议。

### 无约束优化问题 

现在我们可以检查无约束优化{term}`运行`的输出结果。

选择`查看日志`以查看运行日志，如下图所示。优化结果被总结于'open-mdao'条目中，
该条目显示优化成功完成。优化输入值(x，y)收敛于已知的解析解
($x=\frac{20}{3}$, $y=-\frac{22}{3}$，小数形式显示)。如果我们向下滚动，
我们可以看到拋物面组件有13条单独的条目，每个条目对应组件的一次分析执行。

```{image} media/open-mdao-paraboloid-2.png
:alt: run-log-open-mdao-paraboloid-unconstrained
:class: bg-primary mb-1
:width: 700px
:align: center
```

我们还可以更详细地查看open-mdao组件的输出。在工作区中选择open-mdao组件，
然后导航到`日志`选项卡，选择`下载文件快照`。'outputs'文件夹应该包含三个文件：

* **n2.html**：优化问题的可视化展示。我们可以通过从组件参数中的【visualise】列表中删除【n2_diagram】条目来取消此输出。 
* **om_problem_recorder_(time-stamp).sqlite** ：记录器数据库可用于进一步的数据分析、可视化和结果存储。数据库的内容可以进行调整（参见open-mdao计算函数中【om.SqliteRecorder】类的使用）。
* **run_driver.log** ：一个日志文件，其中包含openMDAO标准输出和来自`om_component.py`模块的任何附加print()函数输出。

````{note}
Python的print()输出非常有用，可以用于实现捕获中间变量值和调试的目的。为了在日志文件中访问此输出，可以使用以下Python代码结构（请参见`compute.py`模块的示例）：

```{code}
from contextlib import redirect_stdout

with open(run_folder / "filename.log", "w") as f:
    with redirect_stdout(f):
        print("This is a log message!")
```
````

以下是`run_driver.log`文件结尾的摘录。该文件提供了一些关于优化过程的有趣见解。

看起来，SLSQP优化在4次迭代后收敛于一个最优解，其中包括5次【函数评估】来计算函数输出
和4次【梯度评估】来计算抛物面函数对设计变量 x 和 y 的导数（梯度）。

观察最后一次迭代中【Calling compute】【message】出现的频次，
我们可以看到抛物面组件的【函数评估】执行了一次，而【梯度计算】执行了两次（每个设计变量一次）。
这是因为我们通过在参数中设置`“【approx_totals】: true`来使用有限差分梯度计算方法
（【fd_step】参数设置步长大小）。

因此，抛物面组件总共执行了13次，这与{term}`运行`日志输出相匹配。

通过从接口控件中选择`下载`，保存会话数据和运行日志。

```{code}
Driver debug print for iter coord: rank0:ScipyOptimize_SLSQP|5
--------------------------------------------------------------
Design Vars
{'paraboloid.x': array([6.66663333]), 'paraboloid.y': array([-7.33336667])}

Calling compute.
message: 
 {'component': 'paraboloid', 'inputs': {'y': [-7.333366666671174], 'x': [6.666633333303766]}, 'get_grads': False, 'get_outputs': True}
Nonlinear constraints
None

Linear constraints
None

Objectives
{'paraboloid.f_xy': array([-27.33333333])}

Driver total derivatives for iteration: 6
-----------------------------------------

Calling compute.
message: 
 {'component': 'paraboloid', 'inputs': {'y': [-7.333366666671174], 'x': [6.666733333303766]}, 'get_grads': False, 'get_outputs': True}
Calling compute.
message: 
 {'component': 'paraboloid', 'inputs': {'y': [-7.333266666671174], 'x': [6.666633333303766]}, 'get_grads': False, 'get_outputs': True}
Elapsed time to approx totals: 0.11306452751159668 secs

{('paraboloid.f_xy', 'paraboloid.x'): array([[-5.82076609e-11]])}
{('paraboloid.f_xy', 'paraboloid.y'): array([[-5.82076609e-11]])}

Optimization terminated successfully    (Exit mode 0)
            Current function value: -27.333333329999995
            Iterations: 4
            Function evaluations: 5
            Gradient evaluations: 4
Optimization Complete
-----------------------------------
```

### 约束优化问题

我们现在希望将以下约束条件添加到优化问题中

$$ 
  0.0 \leq g(x,y) \leq 10.0  \quad , \quad g(x,y) = x + y  . 
$$

不需要添加另一个组件到{term}`运行`中，我们可以使用openMDAO [ExecComp](https://openmdao.org/newdocs/versions/latest/features/building_blocks/components/exec_comp.html)组件来实施这个简单的约束条件。

在工作区中选择open-mdao组件进行编辑，导航到`参数`选项卡，
并复制和粘贴[约束](tutorials-open-mdao-paraboloid-parameters)的参数值。

与非约束问题的参数相比，进行了一些更改：

* 添加了【ExplicitComponents】部分，以将抛物面组件的 x 和 y 变量提升为全局问题变量。
* 添加了【ExecComps】部分，定义了一个名为【constraint】的组件。
* 将约束输出添加到【output_variables】列表中。
* 通过将【approx_totals】设置为【false】来关闭有限差分梯度计算。

选择`保存数据`保存并关闭组件。

请注意，无需再次上传`om_component.py`模块。要验证这一点，您可以打开open-mdao组件`参数`选项卡，文件条目应再次出现在【user_input_files】下，就像之前一样。

通过在{term}`运行`控件界面中选择运行符号 ▶ 来创建一次新的{term}`运行`。
这应该会非常快速地执行。

选择`查看日志`查看运行日志，如下图所示。open-mdao组件条目列出了受约束问题的最优 x 和 y 值，
这些值已经收敛到了解析解的后几个小数位上（$x=7$, $y=-7$）。

```{image} media/open-mdao-paraboloid-3.png
:alt: run-log-open-mdao-paraboloid-constrained
:class: bg-primary mb-1
:width: 700px
:align: center
```

在工作区中选择open-mdao组件，打开`日志`选项卡并选择`下载文件快照`。

将下图显示的N2图（n2.html）与上一次{term}`运行`的图进行直观比较。
新的约束组件出现在抛物面组件下方作为另一个显式组件。

接下来，检查【run_driver.log】输出文件。只需要2次迭代，
其中包括2次【函数评估】和2次【梯度评估】即可达到收敛。 
【Calling compute_partials】消息证实，我们现在直接从抛物面组件获取解析梯度信息，
而不是使用有限差分。使用一个抛物面组件评估，可以得出所有变量的导数，
这种方法比有限差分方法更加高效。

为了对比分析，可以在openMDAO用户指南的[Optimization of Paraboloid](https://openmdao.org/newdocs/versions/latest/basic_user_guide/single_disciplinary_optimization/first_optimization.html)部分找到此问题的纯Python / openMDAO实现。

通过在界面控件中选择`下载`来保存会话数据和运行日志。

```{image} media/open-mdao-paraboloid-4.png
:alt: n2-open-mdao-paraboloid-constrained
:class: bg-primary mb-1
:width: 700px
:align: center
```

## 清理

您可以通过选择界面中`创建`来删除您的会话，这可能需要一分钟左右在云端重置会话。

```{warning}
当您即将删除一个{term}`运行`时，您应该会看到一个警告消息。
如果您选择继续，则所有的{term}`运行`数据（会话数据、输入和输出）将被永久删除。
```

## 参考文献

1. [Simple Optimization](https://openmdao.org/newdocs/versions/latest/examples/paraboloid.html?highlight=unconstrained) example from the openMDAO user guide.
2. [Optimization of Paraboloid](https://openmdao.org/newdocs/versions/latest/basic_user_guide/single_disciplinary_optimization/first_optimization.html) section of the openMDAO user guide.