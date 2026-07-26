## Links for utilities
- [OpCore Simplified](https://zhoang2801/OpCore-Simplify)
- [USB Mapping Tool](https://github.com/USBToolBox/tool)
- [OCAuxiliaryTools](https://github.com/ic005k/OCAuxiliaryTools)
- [OpenCorePKG](https://github.com/acidanthera/OpenCorePkg/releases)
### Video i'm gonna be following
[The video](https://www.youtube.com/watch?v=GBxo1cf7fDw)


## Links for utilities

- [OpCore Simplified](https://github.com/lzhoang2801/OpCore-Simplify)
- [USB Mapping Tool](https://github.com/USBToolBox/tool)
- [OCAuxiliaryTools](https://github.com/ic005k/OCAuxiliaryTools)
- [OpenCorePKG](https://github.com/acidanthera/OpenCorePkg/releases)

### Video i'm gonna be following

[The video](https://www.youtube.com/watch?v=GBxo1cf7fDw)

---

## Step by step process

### 1. Gather a hardware report

- Run OpCore Simplify on the actual target PC (not a different machine)
- Windows: run `OpCore-Simplify.bat`
- macOS: run `OpCore-Simplify.command`
- Linux: run `OpCore-Simplify.py` with Python installed
- Choose the export hardware report option, this generates a JSON file describing your CPU, GPU, WiFi/Bluetooth chip, audio chipset, and storage controller
- Keep this report, it's the input for the next step

### 2. Generate the EFI with OpCore Simplify

- Feed the hardware report back into OpCore Simplify
- Pick your target macOS version (Big Sur, since that's what you're already using for your VM plan, is a safe supported option)
- Let it auto select kexts and ACPI patches based on your detected hardware
- Review flagged unsupported components, older or unusual WiFi/Bluetooth chips are the most common thing that needs a workaround (USB WiFi dongle is the common fallback)

### 3. Map your USB ports with USBToolBox

- Real Macs have a strict port limit, Hackintosh needs to explicitly map which physical ports are front/back/internal so macOS doesn't run out of virtual USB port slots
- Run the USBToolBox tool, plug in devices port by port as prompted so it can build an accurate port map
- Export the resulting kext and add it into your EFI folder

### 4. Build the bootable installer

- Download the raw macOS installer image for your chosen version
- Use a tool like Rufus (or similar) to write it to a USB drive
- This creates the boot partition, but the EFI folder from OpCore Simplify needs to be manually placed onto that USB's EFI partition since it isn't auto mounted by Windows

### 5. Place the EFI folder

- Mount the EFI partition on the USB installer (Windows won't do this automatically, you'll need a tool to mount it)
- Copy your generated EFI folder (from OpCore Simplify) onto that partition
- This is what actually lets the USB boot into the macOS installer on non-Apple hardware

### 6. First boot and install

- Boot from the USB in your BIOS/UEFI boot menu
- At this stage the Mac will only boot because of the USB's EFI folder, the internal drive doesn't have one yet
- Run through the macOS installer normally onto your target internal drive

### 7. Move the EFI onto the internal disk

- Once macOS is installed, the internal drive still doesn't have a working EFI folder
- Mount the internal drive's EFI partition and copy the same EFI folder used on the USB onto it
- This is the step that lets the machine boot macOS on its own without the USB plugged in afterward

### 8. Use OCAuxiliaryTools for post-install cleanup

- Handles ongoing OpenCore config tweaks after the initial install
- Useful for adjusting settings without hand-editing the config.plist directly
- Good for troubleshooting audio, sleep, or boot picker issues after the fact

### 9. Test core functionality one at a time

- WiFi/Bluetooth (if applicable)
- Audio output (AppleALC layout-ID may need adjusting depending on your chipset)
- Ethernet
- GPU acceleration
- Sleep/wake

---

## Notes

- Don't skip the hardware report step, an inaccurate report leads to a broken EFI
- Not every combination of components works out of the box, some things (like your specific WiFi chip) may need a USB WiFi adapter as a workaround
- This is legally gray territory per Apple's EULA, not something Apple officially sanctions
