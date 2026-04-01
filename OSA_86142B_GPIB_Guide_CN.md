# HP/Agilent 86142B 光谱分析仪 — GPIB 数据采集指南（中文版）

> **仪器型号：** HP/Agilent 86142B 光谱分析仪（OSA）  
> **通信接口：** GPIB (IEEE-488)，通过 USB 适配器连接  
> **运行平台：** Windows 10/11 + Python  
> **日期：** 2026 年 4 月

---

## 目录

1. [所需设备](#1-所需设备)
2. [硬件接线](#2-硬件接线)
3. [设置 OSA 的 GPIB 地址](#3-设置-osa-的-gpib-地址)
4. [软件安装](#4-软件安装)
5. [验证连接（NI MAX）](#5-验证连接ni-max)
6. [Python 代码 — 测试连接](#6-python-代码--测试连接)
7. [Python 代码 — 完整数据采集](#7-python-代码--完整数据采集)
8. [Python 代码 — 快速读取（不执行扫描）](#8-python-代码--快速读取不执行扫描)
9. [常见问题排查](#9-常见问题排查)
10. [SCPI 命令参考](#10-scpi-命令参考)

---

## 1. 所需设备

| 序号 | 设备 | 说明 |
|------|------|------|
| 1 | **光谱分析仪 (OSA)** | HP/Agilent 86142B |
| 2 | **GPIB-USB 适配器** | 推荐使用 National Instruments NI GPIB-USB-HS，或 Keysight 82357B |
| 3 | **GPIB 电缆** | IEEE-488 电缆（24 针连接器），如适配器不能直接插入 OSA 则需要 |
| 4 | **Windows 电脑** | Windows 10 或 11，需有空闲 USB 接口 |
| 5 | **USB 电缆** | GPIB-USB 适配器自带 |

---

## 2. 硬件接线

### 2.1 连接示意图

```
┌─────────────────┐        ┌────────────────────┐        ┌─────────────────────┐
│                 │  USB   │                    │  GPIB  │                     │
│   Windows 电脑   │────────│  NI GPIB-USB-HS    │────────│  86142B OSA         │
│                 │        │  适配器             │        │  （后面板 GPIB 接口） │
└─────────────────┘        └────────────────────┘        └─────────────────────┘
```

### 2.2 接线步骤

1. **关闭** 86142B OSA 的电源。
2. 在 OSA **后面板**找到 **GPIB 接口** — 这是一个 24 针 IEEE-488 连接器。
3. 将 **GPIB 电缆**从 **NI GPIB-USB-HS 适配器**连接到 OSA 的 GPIB 接口。
4. **用手拧紧**连接器两侧的螺丝，确保连接牢固。
5. 将 NI GPIB-USB-HS 适配器的 **USB 端**插入电脑的 USB 接口。
6. **开启** OSA 电源，等待其完全启动。

> ⚠️ **重要提示：** 连接 GPIB 电缆时请务必先关闭仪器电源，以避免损坏。

---

## 3. 设置 OSA 的 GPIB 地址

默认 GPIB 地址通常为 **20**。验证或修改方法如下：

1. 在 OSA 前面板上，按 **`System`**（系统）键。
2. 找到 **`GPIB Address`**（GPIB 地址）选项（根据固件版本可能显示为 `Remote Interface`）。
3. 记下当前的 GPIB 地址（默认 = **20**）。如需修改，有效范围为 1–30。
4. 按 **`Enter`** 确认。
5. **记录此地址** — Python 代码中需要使用。

---

## 4. 软件安装

### 4.1 安装 NI-VISA 驱动程序

1. 下载地址：<https://www.ni.com/en/support/downloads/drivers/download.ni-visa.html>
2. 运行安装程序 → 选择 **完整安装（Full installation）**。
3. 安装完成后**重启电脑**。

### 4.2 安装 NI-488.2 驱动程序

1. 下载地址：<https://www.ni.com/en/support/downloads/drivers/download.ni-488-2.html>
2. 使用默认设置运行安装程序。
3. 安装完成后**重启电脑**。

### 4.3 安装 Python 库

打开**命令提示符**或 **PowerShell**，运行以下命令：

```bash
pip install pyvisa
pip install pyvisa-py
pip install numpy
pip install matplotlib
```

---

## 5. 验证连接（NI MAX）

1. 从 Windows 开始菜单打开 **NI MAX**（Measurement & Automation Explorer，测量与自动化资源管理器）。
2. 在左侧面板展开 **Devices and Interfaces**（设备和接口）。
3. 应能看到 **"GPIB0 (GPIB-USB-HS)"** 或类似名称。
4. 右键点击 GPIB 接口 → **Scan for Instruments**（扫描仪器）。
5. OSA 应显示为 **`GPIB0::20::INSTR`**（其中 `20` 是您设置的 GPIB 地址）。
6. 双击该仪器 → 点击 **Communicate with Instrument**（与仪器通信）。
7. 输入 `*IDN?` 并点击 **Query**（查询）— 应显示仪器识别字符串。

---

## 6. Python 代码 — 测试连接

保存为 **`osa_test.py`**：

```python
"""
测试与 HP/Agilent 86142B OSA 通过 GPIB 的连接。
首次使用时请先运行此脚本验证通信。
"""
import pyvisa


def test_connection():
    # 创建 VISA 资源管理器
    rm = pyvisa.ResourceManager()

    # 列出所有已连接的仪器
    print("可用仪器列表：")
    resources = rm.list_resources()
    for r in resources:
        print(f"  {r}")

    if not resources:
        print("错误：未找到任何仪器！")
        print("请检查：")
        print("  1. NI-VISA 和 NI-488.2 驱动已安装")
        print("  2. GPIB-USB 适配器已插入电脑")
        print("  3. GPIB 电缆已连接到 OSA")
        print("  4. OSA 已开机")
        return

    # 连接到 OSA（如地址不同请修改）
    gpib_address = 20
    resource_string = f"GPIB0::{gpib_address}::INSTR"

    try:
        osa = rm.open_resource(resource_string)
        osa.timeout = 10000  # 超时时间 10 秒（单位：毫秒）

        # 查询仪器身份
        idn = osa.query("*IDN?")
        print(f"\n已连接到：{idn.strip()}")
        print("连接成功！")

        osa.close()
    except Exception as e:
        print(f"连接 {resource_string} 时出错：{e}")

    rm.close()


if __name__ == "__main__":
    test_connection()
```

---

## 7. Python 代码 — 完整数据采集

保存为 **`osa_read.py`**：

```python
"""
通过 GPIB 从 HP/Agilent 86142B OSA 读取光谱数据。
将波长和功率数据保存为 CSV 文件，并绘制光谱图。

86142B 编程参考：
  - 使用 SCPI 类命令集
  - 光谱数据以 ASCII 逗号分隔值返回
"""
import pyvisa
import numpy as np
import matplotlib.pyplot as plt
import csv
import time
from datetime import datetime


# ============================================================
# 配置参数 — 请根据您的实际设置修改以下参数
# ============================================================
GPIB_ADDRESS = 20               # OSA 的 GPIB 地址
TIMEOUT_MS = 30000              # 超时时间（毫秒），30 秒
START_WAVELENGTH_NM = 1500.0    # 起始波长（纳米）
STOP_WAVELENGTH_NM = 1600.0     # 终止波长（纳米）
RESOLUTION_NM = 0.1             # 分辨率带宽（纳米）
SENSITIVITY = "MID"             # 灵敏度："HIGH1"、"HIGH2"、"HIGH3"、"MID"、"NORMAL"
OUTPUT_CSV = "osa_spectrum.csv"  # 输出 CSV 文件名


def connect_osa(gpib_address):
    """建立与 OSA 的连接。"""
    rm = pyvisa.ResourceManager()
    resource_string = f"GPIB0::{gpib_address}::INSTR"

    print(f"正在连接 {resource_string} ...")
    osa = rm.open_resource(resource_string)
    osa.timeout = TIMEOUT_MS
    osa.read_termination = "\n"
    osa.write_termination = "\n"

    # 查询仪器身份
    idn = osa.query("*IDN?")
    print(f"已连接：{idn.strip()}")

    return rm, osa


def configure_osa(osa):
    """配置 OSA 测量参数。"""
    print("\n正在配置 OSA...")

    # 设置波长范围
    osa.write(f"SENS:WAV:STAR {START_WAVELENGTH_NM}NM")
    osa.write(f"SENS:WAV:STOP {STOP_WAVELENGTH_NM}NM")
    print(f"  波长范围：{START_WAVELENGTH_NM} - {STOP_WAVELENGTH_NM} nm")

    # 设置分辨率带宽
    osa.write(f"SENS:BAND:RES {RESOLUTION_NM}NM")
    print(f"  分辨率带宽：{RESOLUTION_NM} nm")

    # 设置灵敏度
    sensitivity_map = {
        "NORMAL": "NORM",
        "MID": "MID",
        "HIGH1": "HIGH1",
        "HIGH2": "HIGH2",
        "HIGH3": "HIGH3",
    }
    sens_cmd = sensitivity_map.get(SENSITIVITY, "MID")
    osa.write(f"SENS:POW:DC:RANG:LOW:SENS {sens_cmd}")
    print(f"  灵敏度：{SENSITIVITY}")

    # 设置光谱数据格式
    osa.write("TRAC:STAT:FORM YVAL")
    print("  配置完成。")


def perform_sweep(osa):
    """触发单次扫描并等待完成。"""
    print("\n正在执行扫描...")
    osa.write("INIT:IMM")
    osa.query("*OPC?")
    print("  扫描完成。")


def read_trace_data(osa):
    """从 OSA 读取光谱数据（波长和功率）。"""
    print("\n正在读取光谱数据...")

    # 获取数据点数
    num_points_str = osa.query("TRAC:POIN? TRA")
    num_points = int(float(num_points_str.strip()))
    print(f"  数据点数：{num_points}")

    # 读取波长数据（X 轴） — 返回值单位为米
    osa.timeout = 60000  # 增加超时时间以适应大数据传输
    x_data_str = osa.query("TRAC:DATA:X? TRA")
    x_values = np.array([float(v) for v in x_data_str.strip().split(",")])
    wavelengths_nm = x_values * 1e9  # 米 → 纳米

    # 读取功率数据（Y 轴） — 返回值单位为 dBm
    y_data_str = osa.query("TRAC:DATA:Y? TRA")
    power_dbm = np.array([float(v) for v in y_data_str.strip().split(",")])

    print(f"  波长范围：{wavelengths_nm[0]:.2f} - {wavelengths_nm[-1]:.2f} nm")
    print(f"  功率范围：{power_dbm.min():.2f} - {power_dbm.max():.2f} dBm")

    return wavelengths_nm, power_dbm


def save_to_csv(wavelengths, power, filename):
    """将波长和功率数据保存为 CSV 文件。"""
    print(f"\n正在保存数据到 {filename} ...")
    with open(filename, "w", newline="") as f:
        writer = csv.writer(f)
        writer.writerow(["Wavelength (nm)", "Power (dBm)"])
        for wl, pwr in zip(wavelengths, power):
            writer.writerow([f"{wl:.4f}", f"{pwr:.4f}"])
    print(f"  已保存 {len(wavelengths)} 个数据点。")


def plot_spectrum(wavelengths, power):
    """绘制光谱图。"""
    print("\n正在绘制光谱图...")
    plt.figure(figsize=(12, 6))
    plt.plot(wavelengths, power, "b-", linewidth=0.8)
    plt.xlabel("波长 / Wavelength (nm)", fontsize=12)
    plt.ylabel("功率 / Power (dBm)", fontsize=12)
    plt.title(
        f"光谱图 - {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}",
        fontsize=14,
    )
    plt.grid(True, alpha=0.3)
    plt.tight_layout()

    plot_filename = "osa_spectrum.png"
    plt.savefig(plot_filename, dpi=150)
    print(f"  图片已保存到 {plot_filename}")
    plt.show()


def find_peaks(wavelengths, power, threshold_dbm=-40.0):
    """查找光谱中高于阈值的峰值。"""
    print(f"\n正在查找高于 {threshold_dbm} dBm 的峰值...")
    peaks = []
    for i in range(1, len(power) - 1):
        if power[i] > power[i - 1] and power[i] > power[i + 1]:
            if power[i] > threshold_dbm:
                peaks.append((wavelengths[i], power[i]))

    if peaks:
        print(f"  找到 {len(peaks)} 个峰值：")
        for idx, (wl, pwr) in enumerate(peaks, 1):
            print(f"    峰值 {idx}：{wl:.3f} nm，{pwr:.2f} dBm")
    else:
        print("  未找到高于阈值的峰值。")

    return peaks


# ============================================================
# 主程序
# ============================================================
def main():
    print("=" * 60)
    print("HP/Agilent 86142B OSA 数据采集程序")
    print("=" * 60)

    rm = None
    osa = None

    try:
        rm, osa = connect_osa(GPIB_ADDRESS)
        configure_osa(osa)
        perform_sweep(osa)
        wavelengths, power = read_trace_data(osa)
        save_to_csv(wavelengths, power, OUTPUT_CSV)
        find_peaks(wavelengths, power, threshold_dbm=-40.0)
        plot_spectrum(wavelengths, power)
        print("\n完成！")

    except pyvisa.errors.VisaIOError as e:
        print(f"\nVISA I/O 错误：{e}")
        print("故障排查：")
        print("  - 检查 GPIB 地址是否与 OSA 前面板设置一致")
        print("  - 确认电缆连接牢固")
        print("  - 尝试增大 TIMEOUT_MS 的值")
    except Exception as e:
        print(f"\n错误：{e}")
        raise
    finally:
        if osa:
            osa.close()
        if rm:
            rm.close()
        print("连接已关闭。")


if __name__ == "__main__":
    main()
```

---

## 8. Python 代码 — 快速读取（不执行扫描）

保存为 **`osa_quick_read.py`**：

```python
"""
快速读取：获取 86142B 当前显示的光谱数据。
不触发新的扫描 — 仅读取现有数据。
"""
import pyvisa
import numpy as np
import matplotlib.pyplot as plt


GPIB_ADDRESS = 20


def main():
    rm = pyvisa.ResourceManager()
    osa = rm.open_resource(f"GPIB0::{GPIB_ADDRESS}::INSTR")
    osa.timeout = 30000
    osa.read_termination = "\n"
    osa.write_termination = "\n"

    print(f"仪器识别：{osa.query('*IDN?').strip()}")

    # 读取当前光谱数据
    x_raw = osa.query("TRAC:DATA:X? TRA")
    y_raw = osa.query("TRAC:DATA:Y? TRA")

    wavelengths = np.array([float(v) for v in x_raw.strip().split(",")]) * 1e9
    power = np.array([float(v) for v in y_raw.strip().split(",")])

    # 绘图
    plt.plot(wavelengths, power)
    plt.xlabel("波长 / Wavelength (nm)")
    plt.ylabel("功率 / Power (dBm)")
    plt.title("OSA 光谱（当前显示）")
    plt.grid(True)
    plt.show()

    osa.close()
    rm.close()


if __name__ == "__main__":
    main()
```

---

## 9. 常见问题排查

| 问题 | 解决方法 |
|------|----------|
| 未找到任何仪器 | 确认 NI-VISA 和 NI-488.2 驱动已安装。打开 NI MAX → 扫描仪器。 |
| 超时错误 | 增大 `TIMEOUT_MS` 至 60000。高灵敏度模式下扫描较慢。 |
| 数据格式错误 | 部分固件版本使用 `TRACE:DATA? TRA` 而非 `TRAC:DATA:Y? TRA`，请尝试替换。 |
| GPIB 地址不匹配 | 在 OSA 前面板确认：System → GPIB Address。 |
| USB 适配器未被识别 | 重新安装 NI-488.2 驱动程序，尝试更换 USB 接口。 |
| 权限错误 | 以**管理员身份**运行 Python 或命令行终端。 |

---

## 10. SCPI 命令参考

| 命令 | 说明 |
|------|------|
| `*IDN?` | 查询仪器身份信息 |
| `*RST` | 恢复出厂默认设置 |
| `*OPC?` | 等待当前操作完成 |
| `SENS:WAV:STAR <值>NM` | 设置起始波长 |
| `SENS:WAV:STOP <值>NM` | 设置终止波长 |
| `SENS:BAND:RES <值>NM` | 设置分辨率带宽 |
| `INIT:IMM` | 触发单次扫描 |
| `TRAC:POIN? TRA` | 查询数据点数 |
| `TRAC:DATA:X? TRA` | 读取波长数据（单位：米） |
| `TRAC:DATA:Y? TRA` | 读取功率数据（单位：dBm） |
| `TRACE:DATA? TRA` | 读取完整光谱数据（替代命令） |
| `SENS:WAV:STAR?` | 查询当前起始波长 |
| `SENS:WAV:STOP?` | 查询当前终止波长 |

---

## 快速上手清单

```
1. 接线：    电脑 ──USB──> GPIB-USB-HS ──GPIB──> 86142B 后面板
2. 安装：    NI-VISA → NI-488.2 → 重启 → pip install pyvisa numpy matplotlib
3. 验证：    打开 NI MAX → 确认看到 GPIB0::20::INSTR
4. 测试：    python osa_test.py
5. 运行：    python osa_read.py
```
