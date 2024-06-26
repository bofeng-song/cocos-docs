# 使用模板创建扩展

本文将详细介绍如何通过 Creator 提供的扩展模板创建一个带有面板的扩展并使用。

## 创建扩展

Creator 提供了多种扩展模板，包括 **HTML 面板**、**Vue2.x 面板**、**Vue3.x 面板** 等，具体可点击编辑器顶部菜单栏中的 **扩展 -> 创建扩展** 查看，然后根据需要选择相应的扩展模板创建新扩展：

![create-extension-panel](./image/create-extension-panel.png)

| 扩展选项 | 功能说明 |
| :--- | :----- |
| **扩展名** | 创建的扩展名称，要求不能以 `_` 或 `.` 开头、不能含有大写字母。因为扩展名会成为 URL 的一部分，因此也不能含有 URL 的非法字符、不能含有 `.`、`'` 和 `,`。 |
| **作者** | 扩展的作者 |
| **依赖的编辑器版本** | 创建的扩展运行时要求的 Creator 版本，目前要求最低为 v3.3。 |
| **扩展管理器中显示** | 若勾选该项，则扩展创建完成后，会自动打开 **扩展管理器**，并显示创建的扩展。<br>若不勾选该项，则扩展创建完成后，可点击编辑器顶部菜单栏中的 **扩展 -> 扩展管理器** 查看。 |
| **在文件夹中展示** | 若勾选该项，则扩展创建完成后会自动在系统文件管理器中打开扩展包。 |
| **扩展创建的位置** | 创建的扩展包所在目录，可选择 **项目**/**全局** 目录。<br>若选择 **全局** 目录，则是将扩展包应用到所有的 Creator 项目，**全局** 路径为：`%USERPROFILE%\.CocosCreator\extensions`<br>若选择 **项目** 目录，则是将扩展包应用到指定的 Creator 项目，**项目** 路径为 `$你的项目地址\extensions`。|

扩展相关信息设置完成后，点击右下方的 **创建扩展** 按钮，即可创建成功。

### 构建扩展

扩展创建完成后打开扩展包所在目录，执行以下命令：

```bash
# 安装依赖模块
npm install
# 构建
npm run build
```

每个扩展都会带有一些自己的定义，但都依赖于 **Node.js** 环境，通常都会使用一些 **Node.js** 上的第三方包。所以在启用扩展之前需要先在扩展目录下执行 `npm install` 安装依赖模块才能正常编译。

Creator 推荐使用基于 TypeScript 的工作流，Creator 中默认提供的大部分扩展模版也都是基于 TypeScript，这样在编写时能够带上完整的代码提示。但 TypeScript 需要执行 `npm run build` 编译后才能运行。

> **注意**：扩展的 `tsconfig.json` 配置文件中开启了 `resolveJsonModule`，以便通过导入的扩展的 `package.json` 来获取扩展名称，因此 TypeScript 需要升级到 **v4.3**，否则在导入根目录以外的 `json` 时会出现编译结果路径错误的问题。

### 启用扩展

扩展创建并编译完成后，回到编辑器，点击顶部菜单栏中的 **扩展 -> 扩展管理器**，在 **扩展管理器** 的 **项目**/**全局** 选项卡中找到创建的扩展，然后点击右侧的启用按钮，便可启用该扩展。

![enable extension](./image/enable-extension.png)

## 使用扩展

以使用 **Vue2.x 面板** 模板创建的扩展为例。

启用扩展后点击顶部菜单栏中的 **面板 -> 扩展名称 -> 默认面板**，即可打开扩展的默认面板。

![default extension panel](./image/default-extension-panel.png)

点击顶部菜单栏中的 **开发者 -> 扩展名称 -> 发送消息给面板**，便会根据 `package.json` 中的 `contributions.menu` 定义发送消息 `send-to-panel` 给扩展。当扩展收到消息后，根据 `package.json` 中的 `contributions.messages` 定义，调用扩展默认面板中默认的 `hello` 方法，然后将日志信息 `hello` 显示在默认面板中，并打印到 **控制台** 中。

![output](./image/output.png)

> **注意**：每个模板创建出来的扩展都不是完全一样的，更多内容请参考对应扩展包目录下的 `README.md` 文件。
