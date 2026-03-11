## Legal

This project is provided for **educational and personal automation purposes only**.

It is **not affiliated with Nintendo, Game Freak, or The Pokémon Company**.

See:

- LICENSE
- NOTICE
- DISCLAIMER


# Shiny Bot

Automated shiny hunting for **Pokémon FireRed / LeafGreen** on **Nintendo Switch** using a **Mac + Raspberry Pi Pico W**.

The bot:

1. Resets the game  
2. Continues the save  
3. Selects the starter  
4. Opens the summary screen  
5. Detects the **shiny star** using OpenCV  
6. Stops automatically when a shiny is detected  

You can then **manually save the shiny Pokémon**.

---

# Hardware Required

- Nintendo Switch + Dock  
- Raspberry Pi **Pico W**  
- HDMI Capture Card  
- Mac  
- USB cable for Pico  

---

# Mac Setup

Install dependencies:

```
brew install cmake
pip3 install opencv-python numpy
```

Required software:

- Python 3  
- OBS Studio  
- Pico SDK  

Clone the repo:

```
git clone https://github.com/satoshi831/shiny-bot.git
cd shiny-bot
```

---

# Build Pico Firmware

Set Pico SDK path:

```
export PICO_SDK_PATH=~/pico-sdk
```

Navigate to firmware:

```
cd pico-fw
mkdir build
cd build
```

Configure firmware with WiFi credentials:

```
cmake .. \
-DPICO_BOARD=pico_w \
-DWIFI_SSID='"YOUR_WIFI_NAME"' \
-DWIFI_PASSWORD='"YOUR_WIFI_PASSWORD"'
```

Build firmware:

```
cmake --build . -j
```

Flash Pico:

1. Hold **BOOTSEL**  
2. Plug Pico into Mac  
3. Copy firmware  

```
cp shinybot_pico_fw.uf2 /Volumes/RPI-RP2/
```

The Pico will reboot and connect to WiFi.

---

# Test Pico Connection

Find the Pico IP address from your router.

Check status:

```
curl http://PICO_IP:8080/status
```

Expected response:

```
ready=1;queue=0;wifi=1
```

Test button press:

```
curl -X POST http://PICO_IP:8080/cmd -d "press A 120"
```

Test reset:

```
curl -X POST http://PICO_IP:8080/reset
```

---

# OBS Setup

1. Connect Switch Dock HDMI → Capture Card  
2. Connect Capture Card → Mac  
3. Open **OBS Studio**  
4. Add **Video Capture Device**  
5. Select the capture card  

Confirm the Switch video appears.

---

# Test Shiny Detection

Place screenshots in your **Downloads** folder.

Test shiny screenshot:

```
python3 check_star.py --image shiny.jpg --show
```

Test normal screenshot:

```
python3 check_star.py --image normal.jpg --show
```

Test live capture:

```
python3 check_star.py --watch-seconds 5 --show
```

Expected output:

```
STAR_DETECTED
```

or

```
NO_STAR
```

---

# Test Button Sequence

Make script executable:

```
chmod +x run_sequence.sh
```

Run the sequence:

```
./run_sequence.sh
```

This should:

- reset the game  
- continue the save  
- select the starter  
- open the summary screen  

---

# Run the Bot

Start the hunt loop:

```
python3 hunt_loop.py
```

To prevent macOS from sleeping:

```
caffeinate -i python3 hunt_loop.py
```

---

# Runtime Files

The bot writes files that can be used for **OBS overlays**.

`hunt_state.json`

Stores attempt count and runtime.

`encounter_count.txt`

Example:

```
Encounters: 5421
```

`encounter_time.txt`

Example:

```
22:14:35
```

---

# Notes

- The bot **stops automatically when a shiny star is detected**
- You must **manually save the shiny Pokémon**
- Display sleep is safe, but **system sleep must be disabled**
```


