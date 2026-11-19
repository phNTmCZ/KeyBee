## Unsorted, must validate - with your own risk.
---
### MX Master 3S mouse on Linux
Several libraries need to be installed on your system in order to run Logiops. Run the following command in a terminal window to install them:

`sudo apt install cmake libevdev-dev libudev-dev libconfig++-dev

`git clone https://github.com/PixlOne/logiops.git

```
cd logiops
mkdir build
cd build
cmake ..
make
```
touch /etc/logid.cfg
```
devices: (
{
  // For the 3S, the name is changed to "MX Master 3S"
  name: "Wireless Mouse MX Master 2S";
  smartshift:
  {
    on: true;
    threshold: 20;
  };
  hiresscroll:
  {
    hires: false;
    invert: false;
    target: false;
  };
  dpi: 1750;
buttons: (
  {
    # Thumb button
    cid: 0xc3;
    action =
    {
      type: "Gestures";
      gestures: (
        {
          direction: "Up";
          mode: "OnRelease";
          action =
          {
            type: "Keypress";
            keys: ["KEY_VOLUMEUP"];
          };
        },
        {
          direction: "Down";
          mode: "OnRelease";
          action =
          {
            type: "Keypress";
            keys: ["KEY_VOLUMEDOWN"];
          };
        },
        {
          direction: "Left";
          mode: "OnRelease";
          action =
          {
            type: "Keypress";
            keys: ["KEY_PREVIOUSSONG"];
          };
        },
        {
          direction: "Right";
          mode: "OnRelease";
          action =
          {
            type: "Keypress";
            keys: ["KEY_NEXTSONG"];
          }
        },
        {
          direction: "None";
          mode: "OnRelease";
          action =
          {
            type: "Keypress";
            keys: ["KEY_PLAYPAUSE"];
          }
        }
      );
    };
  },
  {
    # Button behind the scroll
    cid: 0xc4;
    action =
    {
      type = "ToggleSmartshift";
    };
  },
  {
    # Back button: Previous workspace
    cid: 0x53;
    action =
    {
      type: "Keypress";
      keys: ["KEY_LEFTMETA", "KEY_PAGEUP"];
    };
  },
  {
    # Next button: Next workspace
    cid: 0x56;
    action =
    {
      type: "Keypress";
      keys: ["KEY_LEFTMETA", "KEY_PAGEDOWN"];
    };
  });
});
```
sudo make install
sudo systemctl enable --now logid

...missing a library? No prob:
sudo apt-get install libglib2.0-dev
;] done

----










