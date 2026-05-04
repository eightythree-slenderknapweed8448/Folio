# Folio 演示材料总览

本目录是为 Folio 演示视频准备的全部素材，自包含、不依赖网络。

## 录制前一次性 setup

```bash
# 1. 编译 release CLI 二进制（已完成；如要重编：）
cargo build --release -p scribe-cli

# 2. 编译 Tauri 桌面 app（已完成；如要重编：）
cd crates/scribe-tauri
./frontend/node_modules/.bin/tauri build --target aarch64-apple-darwin
cd -

# 3. 构建 Python wheel（已完成；如要重新构建：）
maturin build --release --out demo/dist

# 4. （可选）准备一个干净的 Python venv 演示 pip install
python3 -m venv ~/folio-demo
source ~/folio-demo/bin/activate
pip install demo/dist/folio_docx-0.2.1-cp38-abi3-macosx_11_0_arm64.whl
# 或者从 PyPI: pip install folio-docx==0.2.1
```

## 目录结构

```
demo/
├── README.md                          ← 你正在读
├── demo-cn.md                         ← 中文演示文档（含图片、公式、表格、代码、脚注）
├── demo-en.md                         ← 英文演示文档（同等覆盖度）
├── assets/                            ← demo-*.md 引用的图片
│   ├── folio-icon.png                 (PNG raster, 1024×1024)
│   ├── folio-banner.svg               (SVG, 自动缩放至页宽)
│   └── layout-diagram.svg             (SVG, 布局示意)
├── templates/                         ← 真实顶会 Word 模板（演示 --reference-doc）
│   ├── acm.docx                       ACM Master Article Template
│   ├── ieee.docx                      IEEE Conference Template (从 .doc 转换)
│   └── springer-lncs.docx             Springer LNCS Template (从 .dot 转换)
├── scripts/
│   ├── cli-demo.sh                    Bash 演示脚本（逐步暂停版）
│   ├── python-demo.py                 Python 演示脚本 (10 cells, 含中英 + 主题 + 模板 + 多线程)
│   └── python-demo.ipynb              同上的 Jupyter 版本（录制时更上镜）
├── outputs/                           ← 已预生成的所有 .docx，Word 里直接打开看效果
└── dist/                              ← 编译产物
    ├── Folio.app                      macOS app bundle (双击启动)
    ├── Folio_0.2.1_aarch64.dmg        macOS DMG 安装包 (4.5MB)
    ├── folio_docx-0.2.1-cp38-abi3-macosx_11_0_arm64.whl  Python wheel (3MB)
    └── scribe-cli                     → target/release/scribe-cli (符号链接)
```

## 已预生成的 outputs/

每条命令对应一份输出，**录制时只需对比即可，不必现场跑**：

### CLI 路径

| 文件 | 命令 |
|---|---|
| `cli-en-default.docx` | `scribe-cli demo-en.md -o ...` |
| `cli-cn-default.docx` | `scribe-cli demo-cn.md -o ...` |
| `cli-en-academic.docx` | `... --theme academic` |
| `cli-cn-thesis-cn.docx` | `... --theme thesis-cn` （**演示亮点：宋体 + 黑体 + 首行缩进**） |
| `cli-en-report.docx` | `... --theme report` |
| `cli-en-ref-acm.docx` | `... --reference-doc templates/acm.docx` |
| `cli-en-ref-ieee.docx` | `... --reference-doc templates/ieee.docx` |
| `cli-en-ref-springer-lncs.docx` | `... --reference-doc templates/springer-lncs.docx` |

### Python 路径

| 文件 | 调用 |
|---|---|
| `python-default.docx` | `folio.convert(md)` |
| `python-academic/thesis-cn/report.docx` | `folio.convert(md, theme=...)` |
| `python-thesis-cn-custom.docx` | 中文 markdown + thesis-cn 主题 |
| `python-file-academic/thesis-cn.docx` | `folio.convert_file(in, out, theme=...)` |
| `python-ref-acm/ieee/springer-lncs.docx` | `folio.convert_file(in, out, reference_doc=...)` |

## 录制顺序速查

按 `docs/demo-script.md` 的章节走。录制时窗口布局建议：

```
┌────────────────────┬────────────────────┐
│  终端 / 浏览器     │  Microsoft Word    │
│  (运行 CLI 命令)   │  (打开输出 .docx)  │
└────────────────────┴────────────────────┘
```

| 录制段 | 用什么 |
|---|---|
| **CLI 段** | `demo/scripts/cli-demo.sh`，或者直接照 `docs/demo-script.md` 念 |
| **Python 段** | 推荐 `demo/scripts/python-demo.ipynb` 在 Jupyter 里逐 cell 跑，比终端上镜 |
| **桌面段** | 双击 `demo/dist/Folio.app` 直接启动；或挂载 `Folio_0.2.1_aarch64.dmg` 演示安装 |

## 关键镜头清单（必拍）

1. **`scribe-cli demo-en.md` → 在 Word 里双击公式** — 弹出公式编辑器（不是图片）。**这是和 Pandoc 最直观的差异**。
2. **`--theme thesis-cn`** 切到中文 markdown — 宋体正文 + 黑体标题 + 首行缩进 2 字符 + 1.5 倍行距，"立刻明白这是给我用的"瞬间。
3. **三份顶会模板**（acm → ieee → springer-lncs）连续切换 `--reference-doc` — 字体、边距、纸张大小变化明显。
4. **桌面端 Theme 下拉切换** + Export — 三入口对齐的视觉证据。
5. **Python 终端里 `folio.__version__` 显出 `'0.2.1'`** — 证明 PyPI 真的能装。

## 常见录制坑

- ❌ `cargo run -p scribe-cli` 第一次冷编译要几十秒——**始终用 `target/release/scribe-cli`**
- ❌ Word 默认 Ribbon 占屏太多——录制时关掉 ribbon，或把 ribbon 折叠
- ❌ macOS 录屏分辨率默认 Retina 压缩太大——用 1920×1080，QuickTime → File → New Screen Recording 选定区域
- ❌ Python 演示别用 macOS 系统 Python——Python 3.14 + pip 25 会跳奇怪警告，用 `python3 -m venv` 干净环境

## 一句话发布信息

**"Folio v0.2.1 — Markdown to polished `.docx`, without the cleanup pass."**

- GitHub: https://github.com/Livia-Tassel/Folio
- Releases: https://github.com/Livia-Tassel/Folio/releases/tag/v0.2.1
- PyPI: https://pypi.org/project/folio-docx/0.2.1/

```bash
pip install folio-docx
```
