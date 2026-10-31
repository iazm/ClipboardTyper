# 剪贴板自动输入工具 (Clipboard Typer)

按下设定好的**全局快捷键**后，自动读取剪贴板中的文本，并**模拟键盘输入**到当前焦点窗口。

纯 Python 标准库实现（`ctypes` 调用 Windows API），**无需安装任何第三方依赖**，在 Windows 上直接运行。

---

## 快速开始

1. 双击 `run.bat` 启动（会自动找到本机可用的 Python）。
2. 出现提示后，把鼠标焦点放到要输入的目标窗口（如记事本、聊天框、编辑器）。
3. 按下快捷键，程序就会把剪贴板里的文字“打”出来。

> 也可以手动运行：`python clipboard_typer.py`

---

## 默认快捷键

| 功能 | 快捷键 |
| --- | --- |
| 触发输入 | `Ctrl + Alt + V` |
| 退出程序 | `Ctrl + Alt + Shift + Q` |

快捷键可在 `config.json` 中修改。

---

## 配置说明（`config.json`）

| 字段 | 默认值 | 说明 |
| --- | --- | --- |
| `hotkey` | `ctrl+alt+v` | 触发输入的全局快捷键 |
| `quit_hotkey` | `ctrl+alt+shift+q` | 退出程序的快捷键 |
| `start_delay_seconds` | `0.3` | 按下快捷键后、开始输入前的延迟（秒），方便先聚焦目标窗口 |
| `char_delay_seconds` | `0.02` | 每输入一个字符后的间隔（秒），`0` 表示不等待。有些窗口输入过快会丢字，可适当调大 |
| `mode` | `type` | `type` = 逐字模拟键盘输入；`paste` = 模拟 Ctrl+V 粘贴（更快、更稳，但依赖剪贴板） |
| `trim_trailing_newline` | `true` | 是否去掉文本末尾多余的换行 |

### 快捷键写法

修饰键与主键用 `+` 连接，不区分大小写：

- 修饰键：`ctrl`、`alt`、`shift`、`win`
- 主键：字母 `a`~`z`、数字 `0`~`9`、`F1`~`F12`、`space`、`enter`、`tab`、`esc`、方向键 `left/right/up/down` 等

示例：`ctrl+shift+f9`、`alt+f8`、`ctrl+alt+t`

---

## 两种输入模式

- **`type`（逐字输入）**：用 `SendInput` 的 Unicode 方式逐字模拟按键，**能正确输入中文**；换行会转为真实的回车键，Tab 会转为 Tab 键，适合多行文本。
- **`paste`（粘贴）**：直接模拟 `Ctrl+V`。速度最快，但要求目标窗口支持粘贴，且内容仍通过剪贴板传递。

---

## 自检

运行以下命令可校验配置与系统 API 是否正常（不会真正打字）：

```
python clipboard_typer.py --selftest
```

或：

```
run.bat --selftest
```

---

## 常见问题

1. **按下快捷键没反应 / 热键被占用**
   启动时若提示“注册快捷键失败”，说明该组合键已被其他程序占用，请修改 `config.json` 中的 `hotkey`。

2. **输入到某些程序（如管理员权限运行的程序）无效**
   这是 Windows 的 UIPI（用户界面权限隔离）机制：普通权限程序无法向高权限窗口发送键盘输入。
   解决办法：以“管理员身份”运行本工具（右键 `run.bat` → 以管理员身份运行），或让目标程序以普通权限运行。

3. **逐字输入太快导致丢字**
   增大 `char_delay_seconds`（例如 `0.05`），或改用 `mode: "paste"`。

4. **只想粘贴而不是逐字打**
   把 `mode` 改为 `"paste"`。

5. **希望开机自动启动**
   把 `run.bat` 的快捷方式放入“启动”文件夹：按 `Win + R` 输入 `shell:startup` 回车，把快捷方式复制进去即可。
