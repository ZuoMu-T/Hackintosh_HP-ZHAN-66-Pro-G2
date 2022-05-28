# Fixing Embedded Controller (SSDT-EC/USBX)
### ʲô�� EC��
�ڼ����ƽ̨�ϣ�Embedded Controller �������ǳ�˵�� EC������Ҫ����һ������� MCU �����ɣ�ͨ���ṩ LPC �Ľӿڣ����� LPC ����֮�£��� MCU �����ɵ�һЩ������Ϊ PC �ṩһЩ Power��thermal���Լ�һЩ�����Ŀ���

### What is the SSDT-EC/USBX used for?
> The purpose of SSDT-EC/USBX is a couple things:

* On desktops, the EC(or better known as the embedded controller) isn't compatible with AppleACPIEC driver, to get around this we disable this device when running macOS
* AppleBusPowerController will look for a device named EC, so we will want to create a fake device for this kext to load onto
    * AppleBusPowerController also requires a USBX device to supply USB power properties for Skylake and newer, so we will bundle this device in with the EC fix
* On laptops, the EC is used for hotkeys and battery so disabling this isn't all too ideal. Problem is our EC's name isn't compatible, so we will create a simple "fake" EC device that will satisfy Apple
