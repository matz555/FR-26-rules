# aibot_bringup — Top-Level Launch & Setup Guide

Single-command launches for the complete Aibot Voice Command Challenge system.

## System Overview

```
                    ┌──────────────────┐
                    │  Phone Hotspot   │
                    │   (4G/5G WiFi)   │
                    └────────┬─────────┘
                             │ WiFi
                ┌────────────┴────────────┐
                │                         │
        ┌───────┴────────┐       ┌────────┴───────┐
        │     Laptop     │◄─────►│  TurtleBot3    │
        │  (Ubuntu 20)   │  ROS  │     Burger     │
        │                │       │   (RPi 4)      │
        │ • voice mgr    │       │                │
        │ • mission FSM  │       │ • LiDAR        │
        │ • move_base    │       │ • motors       │
        │ • AMCL + map   │       │ • USB mic/spkr │
        │ • GUI          │       │ • odom + IMU   │
        └────────────────┘       └────────────────┘
                ROS_MASTER on laptop
```

## Packages

| Package | Lines | Purpose |
|---------|-------|---------|
| `aibot_voice` | ~1350 | Hybrid online (Google) + offline (Vosk) STT with mic gating |
| `aibot_mission` | ~1250 | State machine for 5-task mission + command pipelining |
| `aibot_gui` | ~1900 | PyQt5 GUI (Match / Tuning / Mapping tabs) |
| `aibot_nav` | YAML + launch | move_base, AMCL, costmap configs tuned for the arena |
| `aibot_bringup` | launch only | Top-level launch files (this package) |

## Complete Setup (one-time)

### 1. Hardware preparation

- TurtleBot3 Burger with RPi 4 (Ubuntu 20.04 Server)
- LDS-02 LiDAR mounted
- USB microphone mounted on robot (NOT on laptop — too far from speaker)
- USB speaker / 3.5mm speaker connected to RPi
- Verify motors, LiDAR, mic, speaker all work via simple tests

### 2. Laptop setup (Ubuntu 20.04 Desktop)

```bash
# ROS Noetic
sudo apt install ros-noetic-desktop-full ros-noetic-turtlebot3 \
    ros-noetic-turtlebot3-bringup ros-noetic-turtlebot3-slam \
    ros-noetic-turtlebot3-navigation ros-noetic-dwa-local-planner

# Python deps
sudo apt install python3-pip python3-pyaudio portaudio19-dev \
    python3-pyqt5 espeak-ng

pip3 install --user google-cloud-speech vosk webrtcvad

# Vosk model
mkdir -p /opt/vosk/models && cd /opt/vosk/models
wget https://alphacephei.com/vosk/models/vosk-model-small-en-us-0.15.zip
unzip vosk-model-small-en-us-0.15.zip
```

### 3. Workspace setup

```bash
mkdir -p ~/aibot_ws/src
cd ~/aibot_ws/src
# Copy all 5 packages here
cd ~/aibot_ws
catkin_make
source devel/setup.bash
echo "source ~/aibot_ws/devel/setup.bash" >> ~/.bashrc
```

### 4. Network setup (hotspot)

On phone: enable hotspot, name it e.g. `Aibot-Match`, set password.

On laptop:
```bash
# Connect laptop to hotspot
# Find laptop IP
hostname -I
# Add to .bashrc:
echo "export ROS_MASTER_URI=http://<laptop-ip>:11311" >> ~/.bashrc
echo "export ROS_HOSTNAME=<laptop-ip>" >> ~/.bashrc
```

On RPi (via SSH):
```bash
# Connect RPi to same hotspot
# Add to .bashrc:
echo "export ROS_MASTER_URI=http://<laptop-ip>:11311" >> ~/.bashrc
echo "export ROS_HOSTNAME=<rpi-ip>" >> ~/.bashrc
```

### 5. Google Cloud credentials

```bash
# Get credentials JSON from GCP console (see aibot_voice/README.md)
mkdir -p ~/.gcp
mv ~/Downloads/aibot-credentials.json ~/.gcp/

# Edit aibot_voice/config/voice_config.yaml:
#   credentials_path: "/home/your_user/.gcp/aibot-credentials.json"
```

### 6. Generate announcement WAVs

```bash
cd ~/aibot_ws/src/aibot_mission/sounds
bash ../scripts/generate_wavs.sh
# OR record your own clearer voice with arecord
```

## Pre-Match Workflow

### Phase 1: Build the map (do this once per arena)

```bash
# Terminal 1 (RPi via SSH):
ssh ubuntu@<rpi-ip>
roslaunch turtlebot3_bringup turtlebot3_robot.launch

# Terminal 2 (laptop):
roslaunch aibot_bringup mapping.launch
```

- GUI opens → switch to MAPPING tab
- Click "Launch SLAM" (already launched by mapping.launch but button can restart it)
- Use virtual joystick to drive robot slowly around the entire arena
- When map looks complete in RViz → click "Save Map..."
- Drive to each goal (START, ROOM_1, etc.) and click "Capture Pose"
- Click "Export to mission_config.yaml" → paste captured goals into config

### Phase 2: Tune navigation (1-2 practice sessions)

```bash
# Terminal 1 (RPi):
roslaunch turtlebot3_bringup turtlebot3_robot.launch

# Terminal 2 (laptop):
roslaunch aibot_bringup practice.launch
```

- GUI → TUNING tab
- Start with `safe_practice` preset
- Test: GUI → MATCH tab → START MATCH → speak commands
- Observe: does robot reach rooms cleanly? Any collisions?
- Adjust sliders, save new presets
- Iterate until you find a preset that runs the full mission < 60s
  with zero collisions

### Phase 3: Voice recognition tuning

```bash
# Standalone test
rosrun aibot_voice test_voice.py --set SET_A --rounds 10
rosrun aibot_voice test_voice.py --set SET_A --offline-only --rounds 10
```

- Verify confidence > 0.85 for clear speech
- Test BOTH sets, pick the one your team's accent handles better
- Add pronunciation aliases to `voice_config.yaml` if needed

## Match Day

```bash
# 30 minutes before match — boot everything
# Terminal 1 (RPi):
ssh ubuntu@<rpi-ip>
roslaunch turtlebot3_bringup turtlebot3_robot.launch

# Terminal 2 (laptop):
roslaunch aibot_bringup match.launch
```

GUI opens to MATCH tab. From here:

1. **Verify in GUI:**
   - ROS health indicator green (●)
   - Mic indicator shows status
   - State = IDLE

2. **Load match preset (TUNING tab):**
   - Select `match_aggressive` from preset dropdown
   - Click "Load"
   - Switch back to MATCH tab

3. **Position robot at START in arena**

4. **Set initial pose:**
   - In RViz (if open), click "2D Pose Estimate" and drag from robot location
   - OR the launch arg `initial_pose_x/y/a` already sets it

5. **Pick command set:**
   - Click SET A or SET B radio button

6. **When referee gives start signal:**
   - Click big green **START MATCH** button
   - Confirm dialog → Yes
   - Player speaks first command (e.g. "Sensor Grid")

7. **Watch the GUI during match:**
   - State transitions: WAITING_CMD → NAV_TO_ROOM → STOP_AT_ROOM → ...
   - Last voice command shown — verify accuracy
   - Timer counts up
   - Rooms entered: ○○○ → ✓○○ → ✓✓○ → ✓✓✓

8. **If something goes wrong:**
   - Click **⛔ EMERGENCY STOP** (instant, no confirmation)
   - If rooms done but Task 4/5 fails: **Restart from CP1**

9. **Mission complete:**
   - Final state = DONE
   - Timer shows total time
   - Note score for ref

## Troubleshooting on Match Day

| Symptom | Quick fix |
|---------|-----------|
| GUI says ROS disconnected | Check ROS_MASTER_URI on laptop matches its IP |
| Robot doesn't move at all | Check turtlebot3_robot.launch is running on RPi |
| Voice not recognized | Switch to offline-only in voice_config.yaml; check mic plugged in |
| Robot collides | Load `safe_practice` preset (higher inflation) |
| Robot stops in wrong place | Re-set initial pose in RViz; verify map matches reality |
| Internet down before match | System auto-falls back to Vosk offline; verify in voice test |

## Logs Location

Match logs are saved automatically:
- Voice utterances: `/tmp/aibot_voice_logs/utterances_<timestamp>.jsonl`
- ROS logs: `~/.ros/log/latest/`
- Mission status: subscribe to `/mission/status` to record

## Folder Structure

```
~/aibot_ws/
└── src/
    ├── aibot_bringup/      ← top-level launches (this package)
    ├── aibot_voice/        ← STT + mic gating
    ├── aibot_mission/      ← FSM + announcer
    ├── aibot_gui/          ← PyQt5 GUI
    └── aibot_nav/          ← navigation config
```
