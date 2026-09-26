PulseManager CLI Reference Manual
pulsemanager.py is a specialized Command Line Interface (CLI) utility written in Python 3 designed to streamline audio management under Linux systems operating on PulseAudio or PipeWire (via its pulse compatibility layer). It abstracts complex pactl and pacmd back-end commands into intuitive actions.
This document serves as the comprehensive user manual, organized in a logical operational workflow.
1. System Requirements & Setup
Prerequisites

    Operating System: Linux with PulseAudio
    Runtime: Python 3
    Optional Package: termcolor (highly recommended for colorized status output highlighting the active devices).
    bash

    pip install termcolor


Installation
Make the script executable upon download:
bash

chmod +x pulsemanager.py

2. Configuration File Syntax
The script supports custom, human-readable aliases for complex audio hardware identifiers. It automatically scans for a configuration file located at:
~/.config/pulsemanager/config
Syntax Rules

    Lines starting with # are treated as comments and ignored.
    Shortcuts are mapped using a simple key-value association syntax: Short_Alias = Long System Description.
    Do not wrap strings in quotation marks.
    The system description must exactly match the device.description parameter reported by PulseAudio.

Configuration Example
text

# ~/.config/pulsemanager/config
# Define easy-to-remember aliases for system audio nodes

Headset = Plantronics C725 Analog Stereo
Speakers = Built-in Audio Analog Stereo
TV = HDMI / DisplayPort 2 Output

3. Operational Workflow & Commands Reference
Step 1: Inspect Audio Topology (Status)
Before making modifications, inspect your current system configuration. Running the script with no arguments prints a structured map of all hardware audio cards, sinks (outputs), sources (inputs), and running audio streams.

    Command:
    bash

    ./pulsemanager.py

Modify (--show-monitors): By default, virtual monitor sources are hidden. Use this flag to reveal them.
bash

./pulsemanager.py --show-monitors


Step 2: Route Audio Outputs (Sinks)
Modify the default system playback target. Selecting a sink implicitly routes the fallback audio stream to it, unmutes the targeted device, and mutes all alternative output channels.

    Route via Config Alias: Use the custom shortcut declared in your configuration file.
    bash

    ./pulsemanager.py --sink Headset

Route via Numerical Index: Target the device using the raw system index integer reported in the status screen.
bash

./pulsemanager.py --sink 1

Mute All Outputs: Pass the none token to silence all system audio playback hardware.
bash

./pulsemanager.py --sink none


Step 3: Route Audio Inputs (Sources)
Modify the system default recording target (Microphones).

    Route via Pattern Match: Provide a partial keyword string matching the microphone description.
    bash

    ./pulsemanager.py --source Micro


Mute All Inputs: Pass the none token to entirely isolate system audio capture.
bash

./pulsemanager.py --source none


Step 4: Control System Volume
Interact directly with the volume envelope of the currently active default audio output device.

    Retrieve Current Volume: Fetch raw integer amplitude coordinates alongside human-readable percentages.
    bash

    ./pulsemanager.py --getvol


Set Absolute Volume: Set the active channel sound pressure level directly via an integer percentage value.
bash

./pulsemanager.py --setvol 65


Adjust Relative Volume: Increment or decrement active volume relative to its current state using signed values.
bash

./pulsemanager.py --setvol +10
./pulsemanager.py --setvol -5


4. Helper Flags & Global Options

    Quiet Mode (-q, --quiet): Prevents the script from automatically printing the complete system audio topology status block following an output mutation execution.
    bash

    ./pulsemanager.py --sink Headset --quiet


Debug Mode (-d, --debug): Prints the precise underlying pactl or pacmd string commands executed against the operating system shell environment.
bash

./pulsemanager.py --getvol --debug
