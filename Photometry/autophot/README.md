# 清华时域天文研究组 AutoPhot 服务器快速批量测光使用说明

Autophot 是一个自动测光程序，上游项目见 [Astro-Sean/autophot](https://github.com/Astro-Sean/autophot)。本目录中的 `Autophot.py` 是在上游 Autophot Python 包外面增加的一层便捷脚本，用来读取 YAML 配置、检查数据路径并启动批量测光。

## 1. 登录服务器并建立个人目录

使用账号 `thusn` 登录 278 服务器。服务器地址请以组内提供的信息为准：

```bash
ssh thusn@<278服务器地址>
```

登录后，在 `~`（即 `/home/thusn/`）下建立以自己名字命名的目录。以后请把个人数据和运行结果放在自己的目录中，不要直接修改或覆盖他人的文件。例如：

```bash
cd ~
mkdir -p yanshy
```

## 2. 公共 Autophot 文件

公共脚本位于：

```text
/home/thusn/yanshy/autophot/
```

其中常用文件包括：

- `Autophot.py`：运行入口。它读取 YAML 参数，调用 Autophot 包完成自动测光。
- `autophot_simple.yaml`：精简配置示例，只保留最常修改的参数，适合第一次运行时使用。
- `telescope.yml`：望远镜和仪器信息，包括 FITS 头关键字、滤光片名称、像元尺度、台址等。

请先确认服务器上的实际目录名和大小写。如果公共目录实际名为 `Autophot`，后面的命令也要相应改为 `/home/thusn/yanshy/Autophot/Autophot.py`。Linux 路径区分大小写。

## 3. 整理观测图像

先在个人目录下为目标建立一个目录，例如：

```bash
mkdir -p ~/yanshy/SN2021agco/image/TNT/phot1
cd ~/yanshy/SN2021agco/image/TNT/phot1
```

把同一批待处理的 FITS 图像放在同一个目录中。可以使用 `image/TNT/phot1/` 这样的多级结构，但运行脚本时必须进入**直接包含 FITS 图像的最末级目录**。`Autophot.py` 只检查当前目录，不会递归查找下级目录。

建议把精简配置复制到该数据目录，每批数据各自保存一份：

```bash
cp /home/thusn/yanshy/autophot/autophot_simple.yaml ./
```

随后编辑 `autophot_simple.yaml`，至少检查：

- `fits_dir`：待处理 FITS 图像所在目录；
- `wdir`：Autophot 工作目录及望远镜配置所在位置；
- `target_ra`、`target_dec`：目标坐标；
- `catalog.use_catalog`：定标星表；
- `template_subtraction`：是否准备模板及进行模板相减；
- `wcs.solve_field_exe_loc` 和 `hotpants_exe_loc`：外部程序路径。

## 4. 激活运行环境

278 服务器使用 Conda 管理环境：

```bash
conda activate autophot_env
```

建议在首次使用时确认当前调用的 Python 和 Autophot 版本：

```bash
which python
python --version
python -m pip show autophot
```

这里使用的是 `autophot_env` 环境中安装的 Autophot 版本。由于命令显式使用了 `python Autophot.py`，真正生效的是当前 Conda 环境的 Python，而不是脚本第一行 shebang 中记录的旧解释器路径。

`Autophot.py` 主要调用上游包中的两个接口：

```python
from autophot.prep_input import load
from autophot.autophot_main import run_automatic_autophot
```

其中 `load()` 读取 Autophot 的默认参数，`run_automatic_autophot()` 执行实际的自动测光流程。本地脚本负责用 YAML 覆盖默认参数、检查当前路径，并将最终参数传给上游 Autophot。

## 5. 前台运行

进入直接包含 FITS 图像和 YAML 文件的目录后运行：

```bash
python /home/thusn/yanshy/autophot/Autophot.py autophot_simple.yaml
```

如果 YAML 中的 `fits_dir` 与当前目录不同，程序会显示类似提示：

```text
FITS path in autophot_simple.yaml is not current path but ...
Whether change the path into the current path([yes]/no):
```

此时直接按回车即可。原因是 `Autophot.py` 把空输入 `""` 和 `yes`、`y` 一样都视为确认，然后仅在本次运行所用的参数中把 `fits_dir` 改成当前目录：

```python
yes = ["Y", "y", "Yes", "yes", "YES", ""]
```

注意：这个修改不会自动写回原 YAML 文件。为了让配置可重复使用，仍建议手动把 YAML 中的 `fits_dir` 改成正确的绝对路径。

### 命令的含义

```text
python Autophot.py autophot_simple.yaml
│      │           └─ 本次运行使用的参数文件
│      └─ 本仓库提供的便捷入口脚本
└─ 当前 autophot_env 环境中的 Python 解释器
```

脚本先载入上游 Autophot 的全部默认参数，再用 `autophot_simple.yaml` 中出现的项目覆盖对应默认值，最后调用 `run_automatic_autophot()`。因此精简 YAML 中没有写出的参数仍采用当前安装版本的默认值。

## 6. 使用模板相减

当目标位于宿主星系等复杂背景上时，可以使用模板相减。模板图像应是目标尚未出现或已经消失时拍摄的同一天区图像。

### 6.1 设置 HOTPANTS 路径

激活 `autophot_env` 后，先查找服务器上 HOTPANTS 的实际路径：

```bash
conda activate autophot_env
which hotpants
```

例如输出为：

```text
/usr/local/bin/hotpants
```

把该绝对路径写入 YAML，并开启模板相减：

```yaml
template_subtraction:
  do_subtraction: true
  prepare_templates: true
  hotpants_exe_loc: /usr/local/bin/hotpants
```

- `do_subtraction: true`：对科学图像进行模板相减；
- `prepare_templates: true`：准备模板目录和模板图像；
- `hotpants_exe_loc`：`which hotpants` 返回的可执行文件绝对路径。

如果 `which hotpants` 没有任何输出，说明当前环境找不到 HOTPANTS。此时不要随意填写路径，应先联系管理员或 ysy 确认安装位置。

部分 AutoPhOT 版本也支持 ZOGY 等其他减模板算法。服务器版本实际支持的算法和选择参数，请先运行 `-D` 生成完整配置并查看 `template_subtraction` 部分；不要直接照搬其他版本的参数名。当前上游版本使用 `method: zogy` 选择 ZOGY，但 278 服务器安装的旧版未必使用同一个键名。

### 6.2 使用自己的模板图像

在 YAML 的 `fits_dir` 所指向的数据目录中建立 `templates`，再按滤光片分别建立 `<滤光片>_template` 子目录：

```bash
cd ~/yanshy/SN2021agco/image/TNT/phot1
mkdir -p templates/{rp_template,ip_template,gp_template,B_template,V_template}
```

目录名与图像中的滤光片名称应一一对应：

| 科学图像滤光片 | 模板目录 |
| --- | --- |
| `rp` | `templates/rp_template/` |
| `ip` | `templates/ip_template/` |
| `gp` | `templates/gp_template/` |
| `B` | `templates/B_template/` |
| `V` | `templates/V_template/` |

如果还有其他滤光片，也使用相同规则，例如 `zs` 对应 `templates/zs_template/`，`U` 对应 `templates/U_template/`，`R` 对应 `templates/R_template/`，`I` 对应 `templates/I_template/`。大小写必须与 FITS 头及 `telescope.yml` 中的滤光片定义一致。

把每个波段对应的模板 FITS 文件放入相应目录。

最终目录结构示例：

```text
phot1/
├── autophot_simple.yaml
├── science_001.fits
├── science_002.fits
└── templates/
    ├── rp_template/
    │   └── rp_template.fits
    ├── ip_template/
    │   └── ip_template.fits
    ├── gp_template/
    │   └── gp_template.fits
    ├── B_template/
    │   └── B_template.fits
    └── V_template/
        └── V_template.fits
```

每个滤光片目录建议只放一幅最终采用的模板。模板应覆盖科学图像的目标区域，具有正确或可求解的 WCS，并尽量选择背景干净、视宁度较好且没有目标暂现源的图像。

### 6.3 自动下载 Pan-STARRS 模板

如果目标天区在 Pan-STARRS 覆盖范围内，可以让 AutoPhOT 自动下载对应天区的模板。YAML 中同时设置：

```yaml
catalog:
  use_catalog: pan_starrs

template_subtraction:
  do_subtraction: true
  prepare_templates: true
  get_PS1_template: true
  hotpants_exe_loc: /usr/local/bin/hotpants
```

这里需要特别注意：

- `catalog.use_catalog` 必须设为 `pan_starrs`；
- `get_PS1_template: true` 必须位于 `template_subtraction` 下面，并保持正确缩进；
- `target_ra`、`target_dec` 和科学图像 WCS 必须正确；
- 服务器需要能够访问 Pan-STARRS 数据服务；
- Pan-STARRS 主要提供 `g`、`r`、`i`、`z`、`y` 波段模板，不能直接替代 Johnson `B`、`V` 等不同滤光片的自备模板。

配置正确后，AutoPhOT 会根据目标天区自动从 Pan-STARRS 下载可用模板，并放入相应的模板目录。第一次下载可能较慢，应观察终端输出或 `autophot.log`，确认下载和模板准备步骤成功完成。

如果服务器当前安装版本提示 `get_PS1_template` 不是有效参数，请运行完整默认配置命令：

```bash
python /home/thusn/yanshy/autophot/Autophot.py -D
```

然后以生成的 `autophot.yaml` 中实际存在的 Pan-STARRS 模板参数为准。AutoPhOT 不同版本的模板下载参数可能发生变化。

### 6.4 运行及检查

模板准备完成后，仍使用正常命令运行：

```bash
python /home/thusn/yanshy/autophot/Autophot.py autophot_simple.yaml
```

运行后应检查减后图像和诊断图，确认模板配准正确，目标位置没有明显的正负偶极残差，亮星附近也没有严重的卷积残差。模板相减完成并不代表结果一定可靠，最终星等仍需结合零点图和减后图像进行质量检查。

## 7. 生成默认参数文件

第一次使用或不清楚某个参数时，可以先生成配置模板。

### 完整默认参数：`-D`

```bash
python /home/thusn/yanshy/autophot/Autophot.py -D
```

程序会在当前目录生成 `autophot.yaml`，并在终端打印完整保存路径。该文件包含当前安装版本提供的全部默认参数，可作为修改参数时的主要参考。

注意：本地 `Autophot.py` 中定义的是大写 `-D`，小写 `-d` 无效。

### 精简默认参数（常用）：`--SD`

```bash
python /home/thusn/yanshy/autophot/Autophot.py --SD
```

程序会在当前目录生成 `autophot_simple.yaml`。它适合日常运行，但不包含所有可调参数。需要进一步调参时，请对照完整的 `autophot.yaml` 以及上游 Autophot 文档和默认配置中的说明。

可以使用下面的命令查看本地脚本支持的所有选项：

```bash
python /home/thusn/yanshy/autophot/Autophot.py -h
```

## 8. 后台运行（选做）

数据较多时可使用 `nohup`：

```bash
nohup python /home/thusn/yanshy/autophot/Autophot.py autophot_simple.yaml > autophot.log 2>&1 &
```

查看日志：

```bash
tail -f autophot.log
```

使用 `nohup` 前，务必先把 YAML 中的 `fits_dir` 改成当前 FITS 数据目录。后台任务无法正常回答前台的路径确认问题；如果路径不一致，程序可能停在交互步骤或因无法读取输入而退出。

还应提前确认 YAML 中其他路径和参数均正确。建议先以前台方式试跑，确认程序已经开始处理图像后，再使用 `nohup` 批量运行。

## 9. 查看测光结果

全部测光完成后，程序会在数据目录旁生成名称以 `_REDUCED` 结尾的结果目录。例如，输入目录为：

```text
SN2021agco/image/TNT/phot1/
```

对应结果目录通常类似：

```text
SN2021agco/image/TNT/phot1_REDUCED/
```

其中主要汇总文件为：

```text
REDUCED.csv
```

`REDUCED.csv` 汇总各幅图像的观测时间、望远镜、滤光片、星等、误差、极限星等及模板相减状态等结果，是后续整理光变曲线时最主要的数据文件。

结果目录中还会包含每幅图像的中间结果和诊断图。零点定标可重点查看类似下面的文件：

```text
zeropoint_<image_name>.pdf
```

应检查零点拟合是否合理、参与定标的星是否存在明显离群点，以及目标测光和模板相减结果是否正常，不要只读取 `REDUCED.csv` 而忽略质量检查。

## 10. 添加新的望远镜或仪器

如果程序不能识别图像所用的望远镜或仪器，可以先联系 ysy；也可以自行检查 FITS 头并在 `telescope.yml` 中增加配置。

使用 WCSTools 查看头信息，例如：

```bash
imhead image.fits
```

也可以结合 `grep` 查找常用关键字：

```bash
imhead image.fits | grep -E "TELESCOP|INSTRUME|FILTER|GAIN|RDNOISE|AIRMASS"
```

然后参照 `telescope.yml` 中已有望远镜的结构，加入新望远镜和仪器的信息，重点确认：

- `TELESCOP` 和 `INSTRUME` 对应的取值；
- 滤光片头关键字及滤光片名称映射；
- `GAIN`、`RDNOISE`、`AIRMASS` 等头关键字；
- `pixel_scale`；
- 台址经度、纬度和海拔。

修改后建议先用少量图像测试，并检查零点 PDF 和 `REDUCED.csv`，确认识别及定标结果正确后再批量处理。

