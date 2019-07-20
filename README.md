installation instructions for macOS High Sierra on PC.
based of [tonymacx86](https://www.tonymacx86.com/)
updated on 07/20/2019
# Installation
## Before you begin
1. Start downloading macOS [High Sierra](https://itunes.apple.com/us/app/macos-high-sierra/id1246284741?ls=1&mt=12) from App Store on macOS computer. To follow the link you might need to open it in Safari and have it redirect to Mac App Store.
2. You need to have an account at [tonymacx86](https://www.tonymacx86.com/) to be able to download 2 programs needed for this guide.
3. Have a USB drive ready, for formatting information see **Formatting** subsection.

## Formatting
Create a GUID formatted flash drive using *diskutil* command in Terminal
1. Open terminal.app on macOS
2. Type `diskutil list`
3. Search for your USB drive number listed as disk, easy hint is to match the size of your USB card to the listed disks.
4. Type `diskutil list disk#` where *disk#* is the number corresponding to your USB drive to verify that this is the disk you want to format.
5. Unmount your USB disk `diskutil unmountDisk /dev/disk#`
6. Format your USB disk `diskutil eraseDisk JHFS+ USB /dev/disk#` where *USB* is the name you want to give to your newly formatted USB drive.
7. You will now have USB formatted to macOS Extended Journaled format.
Sample input
```
diskutil list
diskutil list disk3
diskutil unmountDisk /dev/disk3
diskutil eraseDisk JHFS+ USB /dev/disk3
```

## Create a Bootable USB drive with UniBeast
1. Insert newly formatted USB drive into computer
2. Download and run [Unibeast 8.1.0](https://www.tonymacx86.com/resources/categories/tonymacx86-downloads.3/)
3. At *Select a Destination* choose your **USB drive**
4. At *Select OS Installation* choose **High Sierra**
5. At *Bootloader Configuration* choose **UEFI Boot Mode**
6. Don't choose anything at *Graphics Configuration*
7. Verify your options and Intall, the installation takes a long time so let it run.
8. Download [Multibeast - High Sierra 10.2.0](https://www.tonymacx86.com/resources/categories/tonymacx86-downloads.3/)
9. Download [Clover EFI bootloader](https://sourceforge.net/projects/cloverefiboot/)
9. Once Unibeast finished intalling, drag **Multibeast** and **Clover EFI bootloader** into *Install macOS High Sierra* partition on USB.

## Recommended BIOS Settings
Check your motherboard manufacturer for recommended BIOS settings for hackintosh. Googling your motherboard name BIOS and hackintosh should bring up a list of things you should do before installing macOS High Sierra. My motherboard *ASROCK PRO4* didn't require any changes to its BIOS. However I strongly recommend to change boot order to boot into USB first.

## Install macOS High Sierra
Best thing is to install High Sierra on a fresh SSD. If you're partitioning your SSD there may be other steps for you to perform to get your SSD partitioned. Prepare your computer by unplugging unneeded HDDs and SDDs, leaving your one and only SDD. You can plug them back in after installation.
1. Turn on the computer while repeatedly pressing boot device hotkey on the keyboard. F12 for Gigabyte motherboards, F8 for ASUS motherboards, F11 for ASrock motherboards.
2. Choose USB drive you created.
3. If Clover screen doesn't appear, or the computer loads your operating system, restart, repeat steps 1 and 2 and choose a different selection.
4. At Clover sceen choose **Boot OS X Install from Install macOS High Sierra**. The computer will boot into macOS recovery.
5. At Utilities Screen, top left menu bar select *Utilities* and click on *Terminal*
6. You need to format your SSD, to do so see **Formatting** subsection. This time you're formatting your SSD not USB drive.
7. Once you've formatted SSD, close out of the terminal window to return to Utilities Screen.
8. Click on Install macOS High Sierra, follow instructions to complete installation. The installation should take a little while with multiple reboots.

## Post Installation
Once you completed installation you have to install some kexts. Kexts are something like drivers, they let your hardware work with macOS software. **Do not use Multibeast** to install kexts. Multibeast installed kexts often break and do not survive updates. On top of that, it becomes hard to update kexts, remove unwanted kexts and troubleshoot if something breaks.

### Install Clover UEFI
First thing you want to do is install Clover UEFI into your macOS installation. It is easily done with Multibeast.
1. Open up Finder and navigate to your USB drive.
2. Open and run *Multibeast*.
3. Click on *Bootloaders* icon.
4. Select *Clover UEFI Boot Mode*, click on Build and click on Install.

### Download kexts
Now for kexts, these are kexts that I had to download for my motherboard ASROCK PRO4. The first 4 kexts are required for all systems and you should download them too.
1. Download [FakeSMC.kext](https://bitbucket.org/RehabMan/os-x-fakesmc-kozlek/downloads/), choose *RehabMan-FakeSMC-2017-1017.zip*. This allows your computer to boot a hackintosh.
2. Download [Lilu.kext](https://github.com/vit9696/Lilu/releases), choose *1.2.1.DEBUG.zip*. This is required for audio to work.
3. Download [USBInjectAll.kext](https://bitbucket.org/RehabMan/os-x-usb-inject-all/downloads/), choose *RehabMan-USBInjectAll-2017-1214.zip*. This enables USB.
4. Download [AppleALC.kext](https://github.com/vit9696/AppleALC/releases), choose *1.2.1.DEBUG.12.11.17.zip*. This allows audio to work.
5. Download ethernet kext. You need to find out what chipset your motherboard requires. For example, motherboards using an Intel Ethernet Chipset, download [IntelMausiEthernet.kext](https://bitbucket.org/RehabMan/os-x-intel-network/downloads/), choose *RehabMan-IntelMausiEthernet-v2-2017-0914.zip*.

### Adding kexts to Clover
Everytime your computer boots, it'll check for existing kexts and automatically inject them into the system. To add kexts to Clover:
1. Open Finder and navigate to your USB
2. Open Clover Configurator
3. Select mount EFI under Tools
4. Click Mount Partition
5. Click Open Partition, this is your EFI folder.
6. Place downloaded kexts into EFI/Clover/kexts/Other
7. Edit config.plist to always inject kexts on start up, to do so open up config.plist as a text file in EFI/Clover directory, if window in Clover Configurator pops up close the window and open config.plist with textedit.app, look for this
```
<key>SystemParameters</key>
<dict>
    <key>InjectKexts</key>
    <string>Detect</string>
```
Modify `<string>Detect</string>` to `<string>Yes</string>`, save and close the window. Now Clover will always inject kexts.

### Setting up audio
To get audio to work follow these steps:
1. Open config.plist using Clover Configurator
2. Click Apci
3. Verify the HDAS to HDEF patch exists under DSDT -> Patches box. If not click the add “+” button and enter:
   Comment: Rename HDAS to HDEF
   Find* [HEX]: 48444153
   Replace [HEX]: 48444546
4. Next click Devices on the left column
5. Under Audio column, check ResetHDA checkbox
6. Under Audio column, type in the audio ID number based on what audio codec your motherboard is using you will need to experiment with different numbers if the one is bold doesn’t work for you. My motherboard requires **ALC887**, in the inject box I type **7**.
| Audio Codec | Audio ID
ALC1220 | Inject = **11**, 1, 2, 5, 7
S1220A | Inject = **7**, 1, 2, 5
ALC1150 | Inject = **1**, 2, 3, 5, 7, 11
ALC892 | Inject = **1**,  2, 3, (4 for laptop), 5, 7, 28, 92, 99
ALC887 | Inject = **7**, 1, 2, 3, 5, 11, 13, 17, 18, 33, 99
7. Save and close config.plist file
8. Restart Hackintosh
9. After restart, open *System Preferences*
10. Select *Sound*
11. Click the tab called *Output*
12. Select *Internal Speakers*
13. Check if sound is working, if not, make sure **Lilu.kext** and **AppleALC.kext** are in the proper directory, refer to **Adding kexts to Clover** susection. Try changing audio ID to a different number in **Setting up audio** step 6.

### Setting up iMessage
Setting up iMessage is quick an easy, all you have to do is add a serial number that your system can be identified as. To do so:
1. Open config.plist using Clover Configurator
2. Navigate to the SMBIOS settings.
3. Click on the up/down arrows right of the big ? mark and select your model identifier that matches the current model of your system. To determine the current model of your system go to *About This Mac*, click on **System Report** in *Overview* tab, under *Hardware Overview* look for *Model Identifier*, this will be the model you'll choose in Clover Configurator.
4. Copy the number generated in *Serial Number* textbox, go to [Apple Check Coverage webpage](https://checkcoverage.apple.com) and enter the serial number to the textbox. If the result comes back *"We're sorry, but this serial number is not valid. Please check your information and try again."* you're good to go! If not, generate a different serial number in step 4 and try again.
5. Once you have your serial number, copy and paste it into *Board Serial Number* textbox in Clover Configurator and add 5 random letters to the end with caps on.
6. Open terminal.app and type `uuidgen`, copy the results of the terminal output to *SmUUID* textbox in Clover Configurator.
7. Save your config.plist
8. Restart your Hackintosh
9. You should have working iMessage, try signing in and sending a message.

### Installing NVIDIA Graphics Drivers
Installing graphics driver for your NVIDIA card is easy. [Download](https://www.tonymacx86.com/nvidia-drivers/) appropriate version based on your OS Build. Once the download has finished, click and run the installer. If the installer says *"Mac OS X version is not compatible"* go back to the **Download** page and try a different version. Typically the latest macOS build will work with the latest NVIDIA driver version.
