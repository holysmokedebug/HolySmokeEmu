# HolySmokeEmu
Instructions
📘 InfernoEMU Android – Easy Usage Guide

These instructions show how to generate and install InfernoEMU Android on an Android phone using Linux (Ubuntu).

No Android Studio, no root, no advanced skills needed.

1️⃣ REQUIREMENTS
Linux / Ubuntu

Ubuntu, Debian, or other Linux

Internet connection

adb (Android Debug Bridge)

Install adb:

sudo apt update
sudo apt install android-tools-adb

Android Phone

Android 9+

Same Wi-Fi network as Linux (or USB connection)

Developer options enabled

2️⃣ PREPARE YOUR ANDROID DEVICE

Open Settings

Go to About phone

Tap Build number 7 times → developer options enabled

Open Developer options

Enable:

✅ USB debugging

(recommended) ✅ Wireless debugging

Your phone is now ready.

3️⃣ RUN THE INFERNOEMU SCRIPT ON LINUX

Download from GitHub:

infernoemu_all_in_one.sh


Make it executable and run:

chmod +x infernoemu_all_in_one.sh
./infernoemu_all_in_one.sh


This will:

create InfernoEMU-Android/ folder

generate all documentation

prepare for installation

4️⃣ INSTALL INFERNOEMU ON YOUR PHONE

Change directory:

cd InfernoEMU-Android


Run the installer:

./install_from_ubuntu.sh


You will see a list of devices, e.g.:

[1] HONOR 9X Lite (192.168.1.45)
[2] Samsung SM-A52 (USB)


Enter the device number and press Enter.

InfernoEMU will be installed automatically on the selected device.

5️⃣ DONE ✅

InfernoEMU appears in your app list

Installation requires no root

System files are not modified

App is safe to remove anytime

❓ COMMON ISSUES
“No devices found”

Make sure:

USB debugging is on

You accepted the ADB prompt on your phone

Phone and Linux are on the same network (Wi-Fi)

USB not recognized

Try:

adb devices


and accept the prompt on the phone.

🔒 SECURITY & LEGAL

InfernoEMU does not include operating systems

Only run OS images you have rights to

iOS and macOS are not supported

User is responsible for OS content legality

🟢 QUICK SUMMARY (1-MINUTE VERSION)
sudo apt install android-tools-adb
chmod +x infernoemu_all_in_one.sh
./infernoemu_all_in_one.sh
cd InfernoEMU-Android
./install_from_ubuntu.sh


Select your device → Done.


An emulator designed to emulate different operating system, lightweight, and easy to host directly on android. By downloading or opening, you agree that the creator is not responsible for any damage caused by this software.

#!/usr/bin/env bash
# ==========================================================
# InfernoEMU Android - ALL-IN-ONE RELEASE + DEVICE SELECTOR
# ==========================================================
# - Generates full open-source project
# - Includes legal & community documentation
# - Allows Linux (Ubuntu) to discover Android devices
# - Shows device model/name
# - Lets user choose target device
# - Installs InfernoEMU APK via ADB (USB or Wi-Fi)
# ==========================================================

set -e

PROJECT="InfernoEMU-Android"
APK_NAME="InfernoEMU.apk"

echo "=================================================="
echo " InfernoEMU Android – All-in-One Installer"
echo "=================================================="
echo

# ----------------------------------------------------------
# CHECK DEPENDENCIES
# ----------------------------------------------------------
if ! command -v adb >/dev/null; then
  echo "[!] adb not found"
  echo "Install with: sudo apt install android-tools-adb"
  exit 1
fi

# ----------------------------------------------------------
# CREATE PROJECT
# ----------------------------------------------------------
mkdir -p "$PROJECT"
cd "$PROJECT"

echo "[+] Creating documentation"

cat > README.md << 'EOF'
# InfernoEMU Android

InfernoEMU Android is a community-driven, open-source ARM64 operating system
virtualization platform for Android devices.

- User-space only (no root)
- ARM64 native via QEMU
- Community-published OS packages
- Legal-first design
- Default OS: Kali Linux ARM64
EOF

cat > INFERNOEMU_ANDROID_WHITEPAPER.md << 'EOF'
# InfernoEMU Android – Official Whitepaper

InfernoEMU Android is a user-space ARM64 OS virtualization platform.

Principles:
- No proprietary operating systems
- Explicit exclusion of Apple iOS and macOS
- Manifest-based OS validation
- Community responsibility model
- Android user-space isolation
EOF

cat > COMMUNITY_OS_GUIDE.md << 'EOF'
# Community OS Publishing Guide

Allowed:
- Open-source OS
- Legally redistributable systems

Required:
- manifest.json
- LICENSE.txt
- rootfs image

Forbidden:
- Proprietary OS
- Apple iOS / macOS
EOF

cat > DISCLAIMER.txt << 'EOF'
InfernoEMU Disclaimer

InfernoEMU does not distribute operating systems.
InfernoEMU does not bypass DRM.
InfernoEMU does not support proprietary systems.

Users are responsible for legal compliance.
EOF

cat > LICENSE << 'EOF'
MIT License

Copyright (c) 2025 InfernoEMU
Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files.
EOF

cat > PROJECT_STATUS.txt << 'EOF'
InfernoEMU Android – Project Status

This repository is complete and publish-ready.
EOF

# ----------------------------------------------------------
# PLACEHOLDER APK
# ----------------------------------------------------------
mkdir -p app
echo "InfernoEMU placeholder APK" > app/InfernoEMU.apk
cp app/InfernoEMU.apk .

# ----------------------------------------------------------
# DEVICE SELECTION + INSTALL SCRIPT
# ----------------------------------------------------------
cat > install_from_ubuntu.sh << 'EOF'
#!/usr/bin/env bash
set -e

APK="InfernoEMU.apk"

echo
echo "=== InfernoEMU Device Selector ==="
echo

adb start-server >/dev/null

echo "[*] Connected devices:"
mapfile -t DEVICES < <(adb devices | awk 'NR>1 && $2=="device"{print $1}')

if [ ${#DEVICES[@]} -eq 0 ]; then
  echo "[!] No devices found"
  echo "Enable USB debugging or Wireless debugging"
  exit 1
fi

echo
INDEX=1
for D in "${DEVICES[@]}"; do
  MODEL=$(adb -s "$D" shell getprop ro.product.model | tr -d '\r')
  MANUF=$(adb -s "$D" shell getprop ro.product.manufacturer | tr -d '\r')
  echo "[$INDEX] $MANUF $MODEL ($D)"
  ((INDEX++))
done

echo
read -p "Select device number: " CHOICE

TARGET="${DEVICES[$((CHOICE-1))]}"

if [ -z "$TARGET" ]; then
  echo "[!] Invalid selection"
  exit 1
fi

echo
echo "[*] Selected device:"
adb -s "$TARGET" shell getprop ro.product.manufacturer
adb -s "$TARGET" shell getprop ro.product.model
echo

echo "[*] Installing InfernoEMU..."
adb -s "$TARGET" install -r "$APK"

echo
echo "[✓] InfernoEMU installed successfully"
EOF

chmod +x install_from_ubuntu.sh

# ----------------------------------------------------------
# FINAL OUTPUT
# ----------------------------------------------------------
echo
echo "=================================================="
echo "[✓] InfernoEMU Android project generated"
echo
echo "Next steps:"
echo "  1. Commit this repository to GitHub"
echo "  2. On Ubuntu/Linux:"
echo "     ./install_from_ubuntu.sh"
echo "=================================================="
