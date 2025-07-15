# auto-cpufreq - Comprehensive Documentation

[![Linux Build](https://github.com/AdnanHodzic/auto-cpufreq/actions/workflows/build-linux.yml/badge.svg?event=push)](https://github.com/AdnanHodzic/auto-cpufreq/actions/workflows/build-linux.yml)
[![Nix Flake](https://github.com/AdnanHodzic/auto-cpufreq/actions/workflows/build-nix.yaml/badge.svg?event=push)](https://github.com/AdnanHodzic/auto-cpufreq/actions/workflows/build-nix.yaml)
[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

**Automatic CPU speed & power optimizer for Linux** - Actively monitors laptop battery state, CPU usage, CPU temperature, and system load, ultimately allowing you to improve battery life without making any compromises.

## 🚀 Quick Start

```bash
# Clone and install
git clone https://github.com/AdnanHodzic/auto-cpufreq.git
cd auto-cpufreq && sudo ./auto-cpufreq-installer

# Monitor system (dry run)
sudo auto-cpufreq --monitor

# Install daemon for permanent optimization
sudo auto-cpufreq --install
```

---

## 📑 Complete Table of Contents

### 📖 User Documentation
- [Overview & Philosophy](#overview--philosophy)
- [Supported Hardware](#supported-hardware)
- [Features](#features)
- [Installation Guide](#installation-guide)
- [Configuration](#configuration)
- [Usage & Commands](#usage--commands)
- [Battery Management](#battery-management)
- [Performance Profiles](#performance-profiles)

### 🔧 Advanced Topics
- [High-Performance Setup](#high-performance-setup)
- [Custom Fan Control](#custom-fan-control)
- [System Integration](#system-integration)
- [Troubleshooting](#troubleshooting)
- [Performance Tuning](#performance-tuning)

### 👩‍💻 Developer Resources
- [Development Setup](#development-setup)
- [Architecture Overview](#architecture-overview)
- [Contributing Guidelines](#contributing-guidelines)
- [Testing Framework](#testing-framework)
- [API Reference](#api-reference)
- [Release Process](#release-process)

---

## 🎯 Overview & Philosophy

auto-cpufreq addresses a fundamental problem in Linux laptop power management: **unoptimized CPU frequency scaling** that negatively impacts battery life and thermal management.

### The Problem
- CPUs often run at maximum performance regardless of power source
- Manual tools like `cpufreq-set` require constant user intervention
- Existing solutions like TLP can be complex and may disable important features
- No intelligent temperature-aware frequency management

### The Solution
auto-cpufreq provides:
- **Intelligent automation** - Set it and forget it
- **Battery-aware optimization** - Different profiles for AC/battery
- **Temperature monitoring** - Prevents overheating while maintaining performance
- **Load-based scaling** - Matches CPU frequency to actual workload
- **Compatibility** - Works alongside thermald and other thermal management tools

---

## 🖥️ Supported Hardware

### CPU Architectures
- **Intel** - All modern Intel processors with P-states/C-states
- **AMD** - Ryzen, EPYC, and legacy AMD processors
- **ARM** - ARM64 processors with cpufreq support

### Device Types
- **Laptops** - Primary target with battery optimization
- **Desktops** - Supported for power saving and thermal management
- **Servers** - Basic support for power efficiency

### Tested Hardware
| CPU Family | Governor Support | Turbo Boost | EPP Support | Notes |
|------------|------------------|-------------|-------------|-------|
| Intel 8th+ gen | intel_pstate | Yes | Yes | Full feature support |
| AMD Ryzen 2000+ | acpi-cpufreq | Yes | Limited | Good support |
| ARM Cortex-A | cpufreq-dt | Varies | No | Basic support |

---

## ✨ Features

### 🔍 Monitoring Capabilities
- **Real-time CPU metrics**
  - Per-core frequency monitoring
  - Per-core usage tracking
  - Temperature monitoring (CPU package + individual cores)
  - Power consumption tracking
- **System metrics**
  - Battery state and charge level
  - System load averages
  - Thermal zone monitoring
  - Power supply detection

### ⚡ Automatic Optimization
- **Governor management**
  - Automatic governor switching based on power source
  - Intelligent performance vs efficiency balancing
  - Temperature-aware governor selection
- **Frequency scaling**
  - Dynamic min/max frequency adjustment
  - Workload-responsive scaling
  - Thermal throttling prevention
- **Turbo boost control**
  - Smart turbo management based on thermals
  - Battery-aware turbo policies
  - User-configurable turbo preferences

### 🔋 Battery Management
- **Charging thresholds** (limited hardware support)
  - Stop charging at custom percentage
  - Start charging at custom percentage
  - Battery health optimization
- **Power supply detection**
  - Multiple power supply support
  - Power adapter wattage detection
  - USB-C PD support

### 🎛️ Advanced Configuration
- **Profile-based settings**
  - Separate AC and battery profiles
  - Custom frequency ranges
  - Governor overrides
  - EPP (Energy Performance Preference) control
- **Platform integration**
  - ACPI platform profile support
  - Intel Energy Performance Bias
  - AMD Platform QoS support

---

## 📦 Installation Guide

### Method 1: auto-cpufreq-installer (Recommended)

**Requirements:**
- Git (for proper versioning)
- Python 3.9+
- systemd or OpenRC
- Root access

```bash
# Install dependencies (Ubuntu/Debian)
sudo apt update
sudo apt install git python3 python3-pip

# Install dependencies (Fedora/RHEL)
sudo dnf install git python3 python3-pip

# Install dependencies (Arch Linux)
sudo pacman -S git python python-pip

# Clone repository
git clone https://github.com/AdnanHodzic/auto-cpufreq.git
cd auto-cpufreq

# Run installer
sudo ./auto-cpufreq-installer

# Follow interactive prompts for:
# - Installation confirmation
# - Service setup
# - Power profiles daemon handling
```

### Method 2: Package Managers

#### Snap Store
```bash
sudo snap install auto-cpufreq
```
**Note:** GUI features limited due to snap confinement.

#### AUR (Arch Linux)
```bash
# Using yay
yay -S auto-cpufreq

# Using paru
paru -S auto-cpufreq

# Manual installation
git clone https://aur.archlinux.org/auto-cpufreq.git
cd auto-cpufreq && makepkg -si
```

#### Gentoo (GURU Repository)
```bash
# Add GURU repository
sudo eselect repository enable guru
sudo emaint sync -r guru

# Unmask package
echo "sys-power/auto-cpufreq ~amd64" >> /etc/portage/package.accept_keywords

# Install
sudo emerge --ask auto-cpufreq
```

#### NixOS
Add to your `flake.nix`:
```nix
{
  inputs = {
    auto-cpufreq = {
      url = "github:AdnanHodzic/auto-cpufreq";
      inputs.nixpkgs.follows = "nixpkgs";
    };
  };
}
```

### Method 3: Development Installation

```bash
# Clone repository
git clone https://github.com/AdnanHodzic/auto-cpufreq.git
cd auto-cpufreq

# Create virtual environment
python3 -m venv venv
source venv/bin/activate

# Install in development mode
pip install -e .

# Run directly
python -m auto_cpufreq.bin.auto_cpufreq --monitor
```

---

## ⚙️ Configuration

### Configuration File Hierarchy

auto-cpufreq searches for configuration files in this order:
1. **Command line**: `--config /path/to/config`
2. **User config**: `$XDG_CONFIG_HOME/auto-cpufreq/auto-cpufreq.conf`
3. **System config**: `/etc/auto-cpufreq.conf`

### Complete Configuration Reference

```ini
# AC Power Configuration
[charger]
# CPU Governor (see available: cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_available_governors)
governor = performance

# Energy Performance Preference (Intel only)
# Options: performance, balance_performance, default, balance_power, power_save
energy_performance_preference = performance

# Energy Performance Bias (Intel only)
# Numeric: 0-15 (0=max performance, 15=max power saving)
# String: performance, balance_performance, default, balance_power, power
energy_perf_bias = balance_performance

# Platform Profile (ACPI)
# Options: performance, balanced, quiet, cool (varies by hardware)
platform_profile = performance

# Frequency Limits (in kHz)
scaling_min_freq = 800000    # 800 MHz minimum
scaling_max_freq = 4600000   # 4.6 GHz maximum

# Turbo Boost
# Options: always, auto, never
turbo = always

# Battery Power Configuration
[battery]
governor = powersave
energy_performance_preference = power_save
energy_perf_bias = power
platform_profile = balanced
scaling_min_freq = 400000    # 400 MHz minimum on battery
scaling_max_freq = 3800000   # 3.8 GHz maximum on battery
turbo = auto

# Battery Charging Thresholds (hardware dependent)
[battery_thresholds]
enable_thresholds = true
start_threshold = 20         # Start charging at 20%
stop_threshold = 80          # Stop charging at 80%

# Ignore specific power supplies
[power_supply_ignore_list]
# Ignore wireless mouse/keyboard batteries
wireless_mouse = true
wireless_keyboard = true
```

### High-Performance Configuration Example

For maximum performance (gaming, content creation, development):

```ini
[charger]
governor = performance
energy_performance_preference = performance
energy_perf_bias = performance
platform_profile = performance
scaling_min_freq = 3800000   # Never drop below base clock
scaling_max_freq = 4600000   # Allow maximum boost
turbo = always

[battery]
governor = performance       # Performance even on battery
energy_performance_preference = balance_performance
scaling_min_freq = 2200000   # Higher minimum on battery
scaling_max_freq = 4200000   # Reduced maximum to save battery
turbo = auto
```

### Power-Saving Configuration Example

For maximum battery life:

```ini
[charger]
governor = powersave
energy_performance_preference = balance_power
scaling_min_freq = 400000
scaling_max_freq = 2200000   # Limit maximum frequency
turbo = auto

[battery]
governor = powersave
energy_performance_preference = power_save
energy_perf_bias = power
scaling_min_freq = 400000
scaling_max_freq = 1800000   # Very conservative maximum
turbo = never
```

---

## 🎮 Usage & Commands

### Basic Commands

```bash
# Monitor mode (shows what would be done)
sudo auto-cpufreq --monitor

# Live mode (temporary optimization)
sudo auto-cpufreq --live

# Install daemon (permanent optimization)
sudo auto-cpufreq --install

# View live statistics
sudo auto-cpufreq --stats

# Remove daemon
sudo auto-cpufreq --remove

# Version information
auto-cpufreq --version
```

### Advanced Commands

```bash
# Force specific governor
sudo auto-cpufreq --force=performance  # or powersave
sudo auto-cpufreq --force=reset        # return to automatic

# Control turbo boost
sudo auto-cpufreq --turbo=always       # always enable turbo
sudo auto-cpufreq --turbo=never        # disable turbo
sudo auto-cpufreq --turbo=auto         # automatic control

# Use custom config file
sudo auto-cpufreq --config=/path/to/custom.conf --monitor

# Debug mode
sudo auto-cpufreq --debug --monitor

# Update daemon
sudo auto-cpufreq --update
```

### Service Management

```bash
# Check daemon status
sudo systemctl status auto-cpufreq

# View logs
sudo journalctl -u auto-cpufreq -f

# Restart daemon
sudo systemctl restart auto-cpufreq

# Enable/disable daemon
sudo systemctl enable auto-cpufreq
sudo systemctl disable auto-cpufreq
```

---

## 🔋 Battery Management

### Charging Thresholds

auto-cpufreq supports battery charging thresholds on select hardware to improve battery longevity.

#### Supported Devices
- **ThinkPad** (most models with `acpi_call` support)
- **IdeaPad** (select models)
- **Framework** laptops
- **System76** laptops
- Some **ASUS** and **MSI** models

#### Configuration

```ini
[battery_thresholds]
enable_thresholds = true
start_threshold = 20    # Start charging when below 20%
stop_threshold = 80     # Stop charging when above 80%
```

#### Manual Testing

```bash
# Check current thresholds (ThinkPad example)
cat /sys/class/power_supply/BAT*/charge_start_threshold
cat /sys/class/power_supply/BAT*/charge_stop_threshold

# Test threshold setting
echo 80 | sudo tee /sys/class/power_supply/BAT0/charge_stop_threshold
```

### Power Supply Detection

auto-cpufreq can ignore specific power supplies to prevent interference:

```ini
[power_supply_ignore_list]
# Common wireless device batteries
HID-BATTERY = true
MOUSE-BATTERY = true
KEYBOARD-BATTERY = true

# Custom power supplies
UPS_BATTERY = true
```

---

## 🔥 High-Performance Setup

For users requiring maximum performance (gaming, content creation, scientific computing), see the dedicated [High-Performance Setup Guide](HIGH-PERFORMANCE-SETUP-README.md).

### Quick High-Performance Setup

```bash
# 1. Create high-performance config
sudo tee /etc/auto-cpufreq.conf << 'EOF'
[charger]
governor = performance
energy_performance_preference = performance
scaling_min_freq = 3800000
scaling_max_freq = 4600000
turbo = always

[battery]
governor = performance
energy_performance_preference = balance_performance
scaling_min_freq = 2200000
scaling_max_freq = 4200000
turbo = auto
EOF

# 2. Install and restart daemon
sudo auto-cpufreq --install
sudo systemctl restart auto-cpufreq

# 3. Verify performance
sudo auto-cpufreq --stats
```

### Performance Monitoring

```bash
# Real-time CPU monitoring
watch -n 1 'cat /proc/cpuinfo | grep "cpu MHz"'

# Temperature monitoring
watch -n 1 'sensors | grep -E "(Core|Package|Tctl)"'

# auto-cpufreq statistics
watch -n 2 'sudo auto-cpufreq --stats'
```

---

## 🌪️ Custom Fan Control

For advanced cooling management, auto-cpufreq can be integrated with custom fan control systems.

### Basic Fan Control Integration

```bash
#!/bin/bash
# /usr/local/bin/adaptive-fan-control

# Get CPU temperature
CPU_TEMP=$(sensors | grep 'Package id 0:' | awk '{print $4}' | cut -d'+' -f2 | cut -d'°' -f1)

# Set fan speeds based on CPU temperature
if (( $(echo "$CPU_TEMP > 75" | bc -l) )); then
    # High temperature - maximum fans
    echo 255 > /sys/class/hwmon/hwmon*/pwm*
elif (( $(echo "$CPU_TEMP > 65" | bc -l) )); then
    # Medium temperature - high fans
    echo 200 > /sys/class/hwmon/hwmon*/pwm*
elif (( $(echo "$CPU_TEMP > 50" | bc -l) )); then
    # Low temperature - medium fans
    echo 150 > /sys/class/hwmon/hwmon*/pwm*
else
    # Idle temperature - low fans
    echo 100 > /sys/class/hwmon/hwmon*/pwm*
fi
```

See [HIGH-PERFORMANCE-SETUP-README.md](HIGH-PERFORMANCE-SETUP-README.md) for comprehensive fan control setup.

---

## 🏗️ Development Setup

### Setting up Development Environment

```bash
# Clone repository
git clone https://github.com/AdnanHodzic/auto-cpufreq.git
cd auto-cpufreq

# Create virtual environment
python3 -m venv venv
source venv/bin/activate

# Install development dependencies
pip install -e .
pip install poetry
poetry install --with dev

# Install pre-commit hooks
pip install pre-commit
pre-commit install
```

### Development Tools

```bash
# Code formatting
poetry run black auto_cpufreq/
poetry run isort auto_cpufreq/

# Linting
poetry run flake8 auto_cpufreq/
poetry run pylint auto_cpufreq/

# Type checking
poetry run mypy auto_cpufreq/

# Running tests
poetry run pytest tests/
```

### Project Structure

```
auto-cpufreq/
├── auto_cpufreq/                 # Main package
│   ├── bin/                      # Entry points
│   │   ├── auto_cpufreq.py      # Main CLI application
│   │   └── auto_cpufreq_gtk.py  # GUI application
│   ├── battery_scripts/          # Battery management
│   ├── config/                   # Configuration handling
│   ├── gui/                      # GUI components
│   ├── modules/                  # Core modules
│   ├── core.py                   # Core optimization logic
│   ├── globals.py               # Global constants
│   ├── power_helper.py          # Power management utilities
│   └── tlp_stat_parser.py       # TLP integration
├── scripts/                      # System integration scripts
├── nix/                         # Nix packaging
├── snap/                        # Snap packaging
├── tests/                       # Test suite
├── pyproject.toml               # Project configuration
└── poetry.lock                 # Dependency lock file
```

---

## 🧪 Testing Framework

### Running Tests

```bash
# All tests
poetry run pytest

# Specific test categories
poetry run pytest tests/unit/
poetry run pytest tests/integration/
poetry run pytest tests/performance/

# With coverage
poetry run pytest --cov=auto_cpufreq --cov-report=html

# Verbose output
poetry run pytest -v -s
```

### Test Categories

1. **Unit Tests** - Individual component testing
2. **Integration Tests** - System interaction testing
3. **Performance Tests** - CPU optimization validation
4. **Hardware Tests** - Device-specific functionality

### Writing Tests

```python
# tests/test_core.py
import pytest
from auto_cpufreq.core import CPUFreqManager

class TestCPUFreqManager:
    def test_governor_switching(self):
        manager = CPUFreqManager()
        assert manager.set_governor('performance') == True
        assert manager.get_current_governor() == 'performance'

    def test_frequency_scaling(self):
        manager = CPUFreqManager()
        result = manager.set_frequency_range(1000000, 3000000)
        assert result.success == True
```

---

## 📊 API Reference

### Core Classes

#### CPUFreqManager
```python
class CPUFreqManager:
    """Main CPU frequency management class."""

    def __init__(self, config_path: str = None):
        """Initialize CPU frequency manager."""

    def set_governor(self, governor: str) -> bool:
        """Set CPU governor for all cores."""

    def get_current_governor(self) -> str:
        """Get current CPU governor."""

    def set_frequency_range(self, min_freq: int, max_freq: int) -> Result:
        """Set CPU frequency range."""

    def enable_turbo(self, enable: bool = True) -> bool:
        """Enable or disable turbo boost."""
```

#### BatteryManager
```python
class BatteryManager:
    """Battery state and threshold management."""

    def is_charging(self) -> bool:
        """Check if battery is charging."""

    def get_charge_level(self) -> int:
        """Get current battery charge percentage."""

    def set_charging_thresholds(self, start: int, stop: int) -> bool:
        """Set battery charging thresholds."""
```

### Configuration API

```python
from auto_cpufreq.config import Config

# Load configuration
config = Config('/etc/auto-cpufreq.conf')

# Access settings
charger_governor = config.get_charger_setting('governor')
battery_governor = config.get_battery_setting('governor')

# Update settings
config.set_charger_setting('governor', 'performance')
config.save()
```

---

## 🤝 Contributing Guidelines

### Getting Started

1. **Fork the repository**
2. **Create a feature branch**: `git checkout -b feature/amazing-feature`
3. **Make your changes**
4. **Run tests**: `poetry run pytest`
5. **Commit changes**: `git commit -m 'Add amazing feature'`
6. **Push to branch**: `git push origin feature/amazing-feature`
7. **Open a Pull Request**

### Code Standards

- **Python 3.9+** compatibility
- **PEP 8** code style (enforced by black)
- **Type hints** for new code
- **Comprehensive tests** for new features
- **Documentation** for public APIs

### Commit Message Format

```
type(scope): brief description

Detailed explanation of changes if needed.

Fixes #issue_number
```

Types: `feat`, `fix`, `docs`, `test`, `refactor`, `chore`

### Pull Request Checklist

- [ ] Tests pass locally
- [ ] Code follows style guidelines
- [ ] Documentation updated
- [ ] Change log updated
- [ ] Backward compatibility maintained
- [ ] Performance impact considered

---

## 🐛 Troubleshooting

### Common Issues

#### 1. Permission Denied Errors
```bash
# Ensure proper permissions
sudo chown root:root /usr/local/bin/auto-cpufreq
sudo chmod +x /usr/local/bin/auto-cpufreq

# Check sudo configuration
sudo visudo
# Add: %wheel ALL=(ALL) NOPASSWD: /usr/local/bin/auto-cpufreq
```

#### 2. Governor Not Changing
```bash
# Check available governors
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_available_governors

# Verify governor support
modprobe cpufreq-userspace
modprobe cpufreq-powersave
modprobe cpufreq-performance

# Check conflicting services
systemctl status power-profiles-daemon
systemctl status tuned
```

#### 3. Frequency Scaling Issues
```bash
# Check frequency limits
cat /sys/devices/system/cpu/cpu*/cpufreq/scaling_min_freq
cat /sys/devices/system/cpu/cpu*/cpufreq/scaling_max_freq

# Verify BIOS settings
sudo dmidecode -t bios
# Ensure: Turbo Boost = Enabled, Power Management = OS Controlled
```

#### 4. Service Not Starting
```bash
# Check service status
systemctl status auto-cpufreq

# View detailed logs
journalctl -u auto-cpufreq --no-pager -l

# Verify installation
which auto-cpufreq
auto-cpufreq --version
```

#### 5. Temperature Monitoring Issuessudo visudo

# Install and configure sensors
sudo apt install lm-sensors
sudo sensors-detect
sudo systemctl restart lm-sensors

# Test sensor detection
sensors
```

### Debug Mode

```bash
# Enable debug logging
sudo auto-cpufreq --debug --monitor

# Check system information
sudo auto-cpufreq --debug --stats

# Monitor with verbose output
sudo auto-cpufreq --debug --live
```

### Hardware-Specific Issues

#### Intel Systems
```bash
# Check P-state driver
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_driver
# Should show: intel_pstate or intel_cpufreq

# Enable intel_pstate if needed
sudo tee /etc/default/grub << 'EOF'
GRUB_CMDLINE_LINUX_DEFAULT="intel_pstate=enable"
EOF
sudo update-grub
```

#### AMD Systems
```bash
# Check CPU driver
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_driver
# Should show: acpi-cpufreq or amd-pstate

# Enable amd-pstate if available (Kernel 5.17+)
sudo tee /etc/default/grub << 'EOF'
GRUB_CMDLINE_LINUX_DEFAULT="amd_pstate=passive"
EOF
sudo update-grub
```

### Performance Validation

```bash
# CPU frequency validation script
#!/bin/bash
echo "=== CPU Frequency Validation ==="
echo "Governor: $(cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor)"
echo "Min Freq: $(cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_min_freq)"
echo "Max Freq: $(cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_max_freq)"
echo "Current: $(cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_cur_freq)"
echo "Turbo: $(cat /sys/devices/system/cpu/intel_pstate/no_turbo 2>/dev/null || echo 'N/A')"
```

---

## 📈 Performance Tuning

### Optimization Levels

#### Level 1: Basic Optimization
- Install auto-cpufreq with default settings
- Enable daemon for automatic operation
- Monitor with `--stats` to verify operation

#### Level 2: Custom Configuration
- Create `/etc/auto-cpufreq.conf`
- Set appropriate governors for workload
- Configure frequency ranges
- Enable battery thresholds if supported

#### Level 3: Advanced Integration
- Custom fan control integration
- Performance monitoring setup
- Workload-specific profiles
- Thermal management coordination

### Benchmarking

```bash
# CPU performance benchmark
sysbench cpu --cpu-max-prime=20000 --threads=12 run

# Power consumption monitoring
sudo powertop --time=60

# Temperature stress test
stress-ng --cpu 12 --timeout 300s
watch -n 1 'sensors | grep Core'
```

### Real-World Optimization Examples

#### Gaming Laptop
```ini
[charger]
governor = performance
energy_performance_preference = performance
scaling_min_freq = 3000000
turbo = always

[battery]
governor = ondemand
energy_performance_preference = balance_performance
scaling_min_freq = 1600000
turbo = auto
```

#### Development Workstation
```ini
[charger]
governor = ondemand
energy_performance_preference = balance_performance
scaling_min_freq = 2200000
turbo = auto

[battery]
governor = powersave
energy_performance_preference = balance_power
scaling_min_freq = 800000
turbo = auto
```

#### Content Creation
```ini
[charger]
governor = performance
energy_performance_preference = performance
scaling_min_freq = 3500000
turbo = always

[battery]
governor = conservative
energy_performance_preference = balance_performance
scaling_min_freq = 1800000
turbo = auto
```

---

## 📚 Additional Resources

### Documentation
- [Kernel cpufreq Documentation](https://www.kernel.org/doc/Documentation/cpu-freq/)
- [Intel P-state Driver](https://www.kernel.org/doc/html/latest/admin-guide/pm/intel_pstate.html)
- [AMD P-state Driver](https://docs.kernel.org/admin-guide/pm/amd-pstate.html)

### Related Projects
- [TLP](https://github.com/linrunner/TLP) - Advanced power management
- [thermald](https://github.com/intel/thermal_daemon) - Thermal management
- [cpupower](https://www.kernel.org/doc/html/latest/admin-guide/pm/cpufreq.html) - CPU frequency utilities

### Community
- [Discord Server](https://discord.gg/Sjauxtj6kH) - Development discussions
- [GitHub Discussions](https://github.com/AdnanHodzic/auto-cpufreq/discussions) - Q&A and feature requests
- [Issue Tracker](https://github.com/AdnanHodzic/auto-cpufreq/issues) - Bug reports

---

## 💡 Tips & Best Practices

### 🎯 Optimization Tips

1. **Start with monitoring** - Always run `--monitor` first to understand your system
2. **One tool at a time** - Don't run auto-cpufreq with TLP or similar tools
3. **Check BIOS settings** - Ensure power management is set to "OS Controlled"
4. **Monitor temperatures** - Use `sensors` to ensure thermal limits aren't exceeded
5. **Test configurations** - Use `--live` mode to test before permanent installation

### ⚡ Performance Best Practices

1. **Frequency floors** - Set appropriate minimum frequencies to prevent performance dips
2. **Governor selection** - Choose governors based on workload patterns
3. **Turbo management** - Use turbo strategically to balance performance and thermals
4. **Battery profiles** - Different optimization for battery vs AC power
5. **Thermal awareness** - Consider thermal limits when setting aggressive profiles

### 🔧 Maintenance

1. **Regular updates** - Keep auto-cpufreq updated for latest optimizations
2. **Log monitoring** - Check systemd logs for any issues or warnings
3. **Performance validation** - Periodically verify optimization effectiveness
4. **Configuration review** - Review and update configuration as needs change

---

## 📄 License

This project is licensed under the GNU General Public License v3.0 - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- **Adnan Hodzic** - Original author and maintainer
- **Contributors** - All the developers who have contributed to the project
- **Community** - Users providing feedback, testing, and documentation
- **Linux Kernel Developers** - For the underlying cpufreq infrastructure

---

## 💰 Support the Project

### Financial Support
- **PayPal**: [Donate via PayPal](https://www.paypal.com/paypalme/AdnanHodzic)
- **Bitcoin**: `bc1qvolntjrnqh8p7k7c3jkd4d4c6j5c3qd8a4d8qn`

### Code Contributions
- Submit bug reports and feature requests
- Contribute code improvements and new features
- Help with testing on different hardware
- Improve documentation and examples

---

*Last updated: 2024 - auto-cpufreq v2.6.0+*
