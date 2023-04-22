# 控制面板

The dashboard is a graphical web interface that allows user to create, edit, execute and inspect the outputs of an analysis {term}`Run`. 
控制面板是一个图形化的网页界面，允许用户创建、编辑、执行和检查一次分析{term}`运行`的输出。
要访问您的控制面板，请打开一个网页浏览器并访问 https://www.dapta.com 。
然后使用您的用户名和密码登录，并单击出现在网站菜单中的【控制面板】按钮。

```{image} media/dashboard-overview.png
:alt: dashboard-overview
:class: bg-primary mb-1
:width: 700px
:align: center
```

## 界面控制
 
**新建** : 重置工作区。完成一次{term}`运行`后，也可使用此功能从云端删除您的会话。

**打开** : 通过上传一个JSON格式的会话定义文件，打开已保存的工作区会话。

**下载** : 下载当前会话的JSON格式版本以及运行日志（如果有）。

**查看日志** : 查看当前的运行日志（如果有）。

**...** : 查看次要控件、有用链接和新功能菜单列表。

## 工作区

The workspace provides a visual representation of the {term}`Component`s and {term}`Connection`s currently defined in your session. 

**Right-click** in the workspace to open a workspace context menu. Select the 'Add Empty Node' option to add an empty template {term}`Component` to the session. 

**Left-click** {term}`Component`s or {term}`Connection`s to edit or delete them. 

**Left-click and hold** to drag {term}`Component`s or {term}`Connection`s across the workspace.

**Scroll** to zoom-in or out. 

Select the workspace controls **+** and **-** to zoom in and out, or the **◻** to fit all {term}`Component`s into the current view.
## Components

{term}`Component`s are defined as calculation blocks that appear as boxes in the workspace. 
Select a {term}`Component` to edit it. 

Each {term}`Component` has a name that is displayed at the top of the {term}`Component` box in the workspace view.
A symbol appearing next to the name indicates the validity status of the {term}`Component`:

**?** : Pending : Some inputs are missing.

**🗸** : Valid : All necessary inputs have been defined. 

**!** : Invalid : Some inputs are erroneous. 

## Connections

A {term}`Connection` is defined as a data link from an origin {term}`Component` output handle (right side handle) to a target {term}`Component` input handle (left side handle).
There are three types of {term}`Connection`s:

**Design variable connection** : transfer design variable values (numbers or arrays of numbers only) between {term}`Component`s. 
They appear as **black** arrows in the workspace view.
Updated variables are transferred after every origin {term}`Component` calculation iteration.
This is the default {term}`Connection` type.

**Implicit variable or file connection** : transfer implicit variables (any JSON serialisable object) or files between {term}`Component`s. 
They appear as **green** arrows in the workspace view.
Updated variables are transferred after every origin {term}`Component` calculation iteration.
This is the default {term}`Connection` type for file {term}`Connection`s.

**Setup variable or file connection** :  transfer setup variables (any JSON serialisable object) or files between {term}`Component`s.
They appear as **blue** arrows in the workspace view.
Updated variables are transferred only once after all {term}`Component`s completed setup and before the first compute of the connection target {term}`Component`.

The data transferred through {term}`Connection`s has to be a JSON serialisable python object (this includes most python data types) or a file reference. 
File reference keys start with the prefix "files." .

### Valid Connections

The compute functions will always be executed in order from a single 'Start Node' to a single 'End Node'. 
This means that a 'Start Node' may not have any incoming connections, and an 'End Node' may not have any outgoing connections. 
Driver components cannot have any connections. 

## Run Controls

**▶** / **Play**: Launch a new {term}`Run`. This option only becomes available once all {term}`Component`s are valid. 

**⏸** / **Pause** : Pause a {term}`Run`. This option only becomes available once a {term}`Run` has been started. Execution can be continued by selecting the play ▶ button.

**⏹** / **Stop**: Stop a {term}`Run`. This option only becomes available once a {term}`Run` has been started. This terminates the current {term}`Run` and may result in an error message.  

