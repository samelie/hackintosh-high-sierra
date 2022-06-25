# Re-installing MacOS High Sierra 10.13.6

## Why? 

nVidIa's web drivers had their certiicates revoked/expired causing hackintoshs to boot with a black screen.

Thankfully the community banded together because without the resources past and recent, I would have been lost

## Steps

### Actually get a UI

In Clover menu

- Boot Args
  - `nv_disable=1`

Now nVidea will be disabled and you can work on implementing the thead

### The Thread :information_source:

> https://forums.macrumors.com/threads/cannot-install-or-use-nvidia-webdrivers-anymore.2346445/

I was acctually on HighSierra 10.113.6 build `17G65`. As the thread says, as a base we need to be on `17G66`. The requires a re-install.

#### Re-install High Sierra `17G66`

Download full copy of installer and rename to `Install macOS High Sierra` in Applications.

https://github.com/ninxsoft/Mist

`mist download installer "10.13.6" application --application-name "Install %NAME% %VERSION%-%BUILD%.app"`

Get a 8GB USB stick. I had problems with the 64GB.

Flash it.

`sudo /Applications/Install\ macOS\ High\ Sierra.app/Contents/Resources/createinstallmedia --volume /Volumes/Install\ macOS\ High\ Sierra`

#### Clover bootloader

So UEFI can boot into Clover.

Get latest https://bitbucket.org/RehabMan/clover/downloads/

Install on the USB with the OS on it. 

When you 'Customize'

- UEFI boot only

- 64drivers (used default group check)

Only works on USB-2 port on computer!

### Clover configurator (?)

https://www.tonymacx86.com/resources/clover-configurator.467/

Re-use old EFI? Unclear if we need our master EFI (in repo source) we're running our working hackintosh on. Probably not, but I did this step.

1.  Mount the EFI on the USB in CC
2.  Copy the EFI folder into the mounted EFI drive.

**Now ready to boot off USB**

### This is where USB boot got tricky.

Even after setting the USB as only boot item in BIOS, the new Clover menu on the USB did not show 'Install macOS High Sierra', only

- Boot from <MY_SSD_WITH_HIGH_SIERRA>
- Boot from Recovery on <MY_SSD...>

Looked everywhere for solution. Issue with HFS file system? Something along these lines maybe? None worked.

[post #1](https://www.tonymacx86.com/threads/solved-clover-boot-issues.240006/page-2) [post #2](https://www.tonymacx86.com/threads/no-options-on-clover-menu.243892/) [post #3](https://www.tonymacx86.com/threads/solved-internal-efi-clover-macos-boot-stuck.257455/) [post #4](https://www.insanelymac.com/forum/topic/328723-os-x-not-showing-in-bootable-usbclover/) [post #5](https://www.insanelymac.com/forum/topic/343187-clover-usb-boot-manager-not-showing-install-osx-catalina-option-dell-inspiron-3847-haswell-i5-4460/)

Solution was 

- boot into Recovery on <MY_SSD...>, 
- Disk Utility > Startup Disk > **USB drive**
- Restart

After this was guided through the install. It surprised me it installed on the right disk. I don't remember selecting a destination.

Changed BIOS settings. Booted as normal and now on `17G66`

### Security Updates

As per thread once on `17G66` you need 005 and 006

- Reboot in SafeMode
  - Clover Menu > Boot Args > `-x`
  - Long boot process
  - Check in SafeMode 
    - System Report > Software

- Open AppStore and download 005, but **no dont update or restart**
  - Note: I did not have success with the .dmg downloads
- /Library/Updates
- See new folder with the right looking .pkg
- Only install the 1 pkg
- Restart
  - Do **not** let it install the AppStore updates over the top of what you just installed!
- In CloverMenu, boot SafeMode again
- When macOS first shows Desktop, you should see a Setting Up This Mac flash briefly
- Verify it no longer says 17G66 in About This Mac
- Open AppStore, download 006, repeat steps
- Back in SafeMode with **17G14042** security update installed, ready to follow this part.

### Cleanup before nVidia drivers re-install

\3. Open **System Preferences**.

 \4. Open "**NVIDIA Driver Manager**" to uninstall your existing GPU driver. If "**NVIDIA Driver Manager**" preference pane opens proceed to step 5. If not go to step 6.

 \5. Once you enter "**NVIDIA Driver Manager**" preference pane, on the lower right hand corner you will find the "**Open Uninstaller**" button. Click that, and follow its instruction to uninstall your GPU driver. Proceed to step. 8.

 \6. If you get an error message asking you to reinstall the driver, then  it means that the certificate is revoked and macOS is unable to load the associated files. You will have to manually uninstall your GPU driver  (proceed from step 7 for that).

 \7. Open Terminal (found under "utilities" in your applications folder)  and run the following commands to manually uninstall GPU driver:

 (a) **pkgutil --expand /Library/PreferencePanes/NVIDIA\ Driver\  Manager.prefPane/Contents/MacOS/NVIDIA\ Web\ Driver\  Uninstaller.app/Contents/Resources/NVUninstall.pkg /tmp/foo**

 (b) **sudo****/tmp/foo/Scripts/postinstall 0 0 /**
 (c) Enter your password when prompted. Wait for the process to complete.

 \8. Uninstall your existing certificate revoked CUDA drivers.

 (a) Here are some typical CUDA driver uninstallation steps. You will  have to run these commands in Terminal (found under "utilities" in your  applications folder):

**How to remove CUDA driver:**

 Type each command in the Terminal (when prompted enter your password):

- **sudo rm -rf /Library/Extensions/CUDA.kext**
- **sudo rm -rf /Library/Frameworks/CUDA.framework**
- **sudo rm -rf /Library/LaunchAgents/com.nvidia.CUDASoftwareUpdate.plist**
- **sudo rm -rf /System/Library/StartupItems/CUDA**
- **sudo rm -rf /usr/local/cuda**

**How to remove CUDA preference pane:**

1. Open **System Preferences**.
2. Right-click on **CUDA**.
3. Click Remove **CUDA Preference** pane.

(b) Refer to the "uninstall" notes, which are contained in the  "installation guide" for your respective version in the archive here: https://developer.nvidia.com/cuda-toolkit-archive

 For example, for version 10.2 this is the link: https://docs.nvidia.com/cuda/archive/10.2/cuda-installation-guide-mac-os-x/index.html#uninstall

 (c) Or search on Google for an appropriate tutorial to manually uninstall your CUDA driver. (*What I have observed is that any driver (GPU or CUDA) once their  certificates are revoked, their respective uninstallers--which might  have been installed during the past installation process--might not  work. You have to go about the process manually using terminal and/or  finder, or a combination of both.*)

### Download the signed Nvidia driver

In case you missed it. And still in Safe-Mode.

[mirror #1](https://drive.google.com/uc?export=download&id=1lPuKuQqI7OtNzMMmSrQ8MqVMC1EnlfCv) [mirror #2](https://storage.googleapis.com/samrad-samelie/NVIDIA_macOS_10.13.6.zip)

\9. Install the GPU & CUDA Driver combo 

 \10. Reboot (into normal mode)

