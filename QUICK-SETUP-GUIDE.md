# Quick Setup Guide: Maximum Performance + Aggressive Fans

## 🚀 Step-by-Step Configuration

### Step 1: Install Dependencies
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git python3 python3-pip python3-venv lm-sensors
sudo sensors-detect --auto
```

### Step 2: Configure auto-cpufreq for Maximum Performance
```bash
sudo tee /etc/auto-cpufreq.conf > /dev/null << 'EOF'
[charger]
governor = performance
scaling_min_freq = 3800000  # Never below base clock (3.8GHz)
scaling_max_freq = 4670000  # Near max boost (4.67GHz)
turbo = always              # Always enable turbo

[battery]
governor = performance      # High performance even on battery
scaling_min_freq = 2200000  # Still high on battery (2.2GHz)
scaling_max_freq = 4200000  # Reduced but high (4.2GHz)
turbo = auto               # Smart turbo on battery
EOF
```

### Step 3: Create Aggressive Fan Control Script
```bash
sudo tee /usr/local/bin/aggressive-fan-control > /dev/null << 'EOF'
#!/bin/bash

# Aggressive Fan Control - System fans HIGH at medium CPU temps
CPU_TEMP_FILE="/sys/class/hwmon/hwmon3/temp1_input"
GPU_TEMP_FILE="/sys/class/hwmon/hwmon5/temp1_input"

# Temperature thresholds (aggressive)
CPU_TEMP_MED=50      # System fans go HIGH at 50°C
CPU_TEMP_HIGH=65     # All fans HIGH
CPU_TEMP_MAX=75      # Maximum cooling

# PWM speeds (0-255)
PWM_MIN=120          # 47% minimum
PWM_HIGH_SYSTEM=220  # 85% for system fans at medium CPU temp
PWM_HIGH=240         # 94% high speed
PWM_MAX=255          # 100% maximum

# Enable manual PWM control
enable_manual_control() {
    for i in {1..7}; do
        echo 1 > "/sys/class/hwmon/hwmon2/pwm${i}_enable" 2>/dev/null
    done
    echo 1 > "/sys/class/hwmon/hwmon5/pwm1_enable" 2>/dev/null
}

# Set fan speeds based on CPU temperature
set_fans() {
    local cpu_temp=$1
    
    if [[ $cpu_temp -le 35 ]]; then
        # Low temp - normal speeds
        speed=$PWM_MIN
        status="LOW"
    elif [[ $cpu_temp -le $CPU_TEMP_MED ]]; then
        # MEDIUM temp - SYSTEM FANS GO HIGH!
        system_speed=$PWM_HIGH_SYSTEM
        cpu_speed=$PWM_MIN
        status="MED_AGGRESSIVE"
    elif [[ $cpu_temp -le $CPU_TEMP_HIGH ]]; then
        # High temp - all fans high
        speed=$PWM_HIGH
        status="HIGH"
    else
        # Max temp - maximum cooling
        speed=$PWM_MAX
        status="MAX"
    fi
    
    # Apply speeds
    if [[ $status == "MED_AGGRESSIVE" ]]; then
        echo $cpu_speed > /sys/class/hwmon/hwmon2/pwm1 2>/dev/null     # CPU
        echo $cpu_speed > /sys/class/hwmon/hwmon2/pwm2 2>/dev/null     # CPU
        for i in {3..7}; do
            echo $system_speed > "/sys/class/hwmon/hwmon2/pwm${i}" 2>/dev/null
        done
    else
        for i in {1..7}; do
            echo $speed > "/sys/class/hwmon/hwmon2/pwm${i}" 2>/dev/null
        done
    fi
    
    echo $status
}

# Get CPU temperature
get_cpu_temp() {
    if [[ -f $CPU_TEMP_FILE ]]; then
        echo $(($(cat $CPU_TEMP_FILE) / 1000))
    else
        echo 50
    fi
}

# Main loop
echo "Starting Aggressive Fan Control"
while true; do
    enable_manual_control
    CPU_TEMP=$(get_cpu_temp)
    STATUS=$(set_fans $CPU_TEMP)
    
    echo "$(date '+%H:%M:%S') | CPU: ${CPU_TEMP}°C | Mode: $STATUS"
    
    if [[ $STATUS == "MED_AGGRESSIVE" ]]; then
        echo "         >>> SYSTEM FANS AGGRESSIVE (85%) at medium CPU temp! <<<"
    fi
    
    sleep 2
done
EOF

sudo chmod +x /usr/local/bin/aggressive-fan-control
```

### Step 4: Create systemd Service for Fan Control
```bash
sudo tee /etc/systemd/system/aggressive-fan-control.service > /dev/null << 'EOF'
[Unit]
Description=Aggressive Fan Control for Maximum Performance
After=multi-user.target

[Service]
Type=simple
ExecStart=/usr/local/bin/aggressive-fan-control
Restart=always
RestartSec=3
User=root

[Install]
WantedBy=multi-user.target
EOF
```

### Step 5: Install and Start Services
```bash
# Install auto-cpufreq (if not already installed)
sudo auto-cpufreq --install

# Enable and start fan control
sudo systemctl daemon-reload
sudo systemctl enable aggressive-fan-control
sudo systemctl start aggressive-fan-control

# Verify services are running
sudo systemctl status auto-cpufreq
sudo systemctl status aggressive-fan-control
```

### Step 6: Monitor Performance
```bash
# Real-time monitoring
sudo auto-cpufreq --monitor

# Check fan speeds and temperatures
sensors

# View fan control logs
sudo journalctl -fu aggressive-fan-control
```

## 🎯 What This Configuration Does

### CPU Performance:
- **3.8-4.67 GHz** on AC power (never below base clock)
- **Performance governor** always active
- **Aggressive turbo boost** for maximum performance

### Fan Strategy:
- **System fans at 85%** when CPU hits 50°C (medium temp)
- **Immediate aggressive cooling** to prevent thermal throttling
- **2-second response time** for temperature changes

### Expected Results:
- ✅ Maximum sustained CPU performance
- ✅ Lower peak temperatures under load
- ✅ No thermal throttling during intensive tasks
- ✅ Aggressive cooling even at moderate temperatures

## 🔧 Quick Verification

```bash
# Check CPU frequency
cat /proc/cpuinfo | grep MHz

# Check current governor
cat /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor

# Monitor temperatures and fan speeds
watch -n 1 sensors
```

## ⚠️ Important Notes

- **Performance over battery life** - this setup prioritizes maximum performance
- **Aggressive cooling** - fans will be louder but temperatures lower
- **Monitor temperatures** during first use to ensure safe operation
- **System fans go HIGH at 50°C** - this is intentional for aggressive cooling

Ready for maximum performance! 🚀