Example of a self contained VST3/VST2 plugin
============================================

Why this project?
-----------------
The latest VST3 SDK (3.7.2) provides instructions to build a VST that doesn't live inside the SDK itself which is a big enhancement from prior versions which did not provide this capability.

That being said, the solution introduced is still backward in my mind: you need to tell the SDK where your plugin is. The name of the project is still `vstsdk`.

In addition, the VST3 SDK 3.6 introduced a couple of issues which makes it impossible to build it without using Xcode.

This project is exactly the `again` example that ships part of the VST3 SDK but self contained and *depending* on the SDK (vs being part of the SDK). As a result it can be used as a starting point to build other plugins.

What this project is not
------------------------
This project is not a tutorial on how to write a VST3 plugin. It only deals with building a plugin *outside* the SDK.

New - August 2018
-----------------
Although this project is still valid, you should check [Jamba](https://github.com/pongasoft/jamba) which offers a simple command line tool to create a blank self contained plugin which *depends* on the SDK. Jamba also offers a lot of additional features.

Configuration and requirements
------------------------------
This project is known to work on macOS 10.15 with Xcode 12 installed. It requires `cmake` version 3.4.3 at the minimum. Because it uses `cmake` it should work on other platforms but it has not been tested.

Downloading the SDK
-------------------
You need to download the VST3 SDK from [steinberg](https://www.steinberg.net/vst3sdk). It seems like they are updating this link with the current version periodically.

Installing the SDK
-------------------
Unpack the SDK to a location of your choice (in my case the SDK is unpacked and renamed `/Applications/VST3_SDK`).

Building this project
---------------------

- Create a folder *outside* the source tree and `cd` to it:

        mkdir /tmp/vst3-again-build
        cd /tmp/vst3-again-build

- Generate the Makefile(s): `VST3_SDK_ROOT` needs to point to the root of the VST3 SDK (as installed/configured previously) and provide the path to the *source* of this project (which contains `CMakeLists.txt`):

        cmake -DVST3_SDK_ROOT=/Applications/VST3_SDK -DCMAKE_BUILD_TYPE=Debug /Volumes/Development/github/org.pongasoft/vst3-again-sample

- Now build the plugin (all its dependencies will be built as well):

        cmake --build .

- Testing that it is a valid VST3 plugin (already run part of the build, but can be run separately):

        > ./bin/validator VST3/again.vst3

        * Loading module...

        	/tmp/vst3-again-build/VST3/again.vst3

        * Scanning classes...

          Factory Info:
        	vendor = Steinberg Media Technologies
        	url = http://www.steinberg.net
        	email = mailto:info@steinberg.de

          Class Info 0:
        	name = AGain VST3
        	category = Audio Module Class
        	cid = 84E8DE5F92554F5396FAE4133C935A18

          Class Info 1:
        	name = AGain VST3Controller
        	category = Component Controller Class
        	cid = D39D5B65D7AF42FA843F4AC841EB04F0

          Class Info 2:
        	name = AGain SideChain VST3
        	category = Audio Module Class
        	cid = 41347FD6FED64094AFBB12B7DBA1D441

        * Creating tests...

        * Running tests...
        ....

- Testing that it is a valid VST2 plugin (with [MrsWatson](https://github.com/teragonaudio/MrsWatson)):

        > mrswatson64 --display-info -p VST3/again.vst3
        - 00000000 000001 MrsWatson version 0.9.8 initialized, build 20150122
        - 00000000 000001 Plugin 'VST3/again.vst3' is of type VST2.x
        - 00000000 000001 Opening VST2.x plugin 'VST3/again.vst3'
        - 00000000 000099 Information for VST2.x plugin 'again.vst3'
        - 00000000 000099 Vendor: Steinberg Media Technologies
        - 00000000 000099 Version: 16973825
        - 00000000 000099 Unique ID: GnV2
        - 00000000 000099 Plugin type: effect, category 1
        - 00000000 000099 Version: 1
        - 00000000 000099 I/O: 2/2
        - 00000000 000099 InitialDelay: 0 frames
        - 00000000 000099 Parameters (1 total):
        - 00000000 000099   0: 'Unit1.Gain' (1.000000)
        - 00000000 000099 Programs (0 total):
        - 00000000 000099 Current program: ''
        - 00000000 000099 Common canDo's:
        - 00000000 000099   sendVstEvents: No
        - 00000000 000099   sendVstMidiEvent: No
        - 00000000 000099   receiveVstEvents: No
        - 00000000 000099   receiveVstMidiEvent: Yes
        - 00000000 000099   receiveVstTimeInfo: Yes
        - 00000000 000099   offline: Yes
        - 00000000 000099   midiProgramNames: No
        - 00000000 000099   bypass: Yes
        E 00000000 000099 Output source could not be opened, exiting

- Deploying the plugin and testing in a real DAW

    --  For VST2 (like Maschine and Reason) you copy and *rename* it:

        mkdir -p ~/Library/Audio/Plug-Ins/VST
        cp -r VST3/again.vst3 ~/Library/Audio/Plug-Ins/VST/again.vst

    --  For VST3:

        mkdir -p ~/Library/Audio/Plug-Ins/VST3
        cp -r VST3/again.vst3 ~/Library/Audio/Plug-Ins/VST3

Because this project uses `cmake` you can also generate an Xcode project by using the proper generator (`-G Xcode`). You can also load the project directly in CLion.

Licensing
---------
Since this project contains an exact copy of the `again` sample code provided with the VST3 SDK, the licensing is the same as the VST3 SDK: GPL version 3
