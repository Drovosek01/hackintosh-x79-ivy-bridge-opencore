## Настройки папки EFI и config.plist

Под настройками папки EFI я имею ввиду то, что я положил в эту папку - кексты, .aml и другие файлы. Про сам пошаговый процесс установки и настройки хакинтоша на x79 можно почитать в [отдельном файле](setup-configure-hackintosh.md)

### Настройка config.plist

Прежде чем вносить изменения, нужно удалить ненужное из стандартного конфига. Вот что, я думаю, нужно сначала удалить из Sample.plist:

```
4 WARNING-комментария в корне
ACPI:
    Add:
        Удалены все пункты
    Patch:
        Удалены все пункты
DeviceProperties:
    Add:
        Удалены все пункты
Kernel:
    Add:
        Удалены все пункты
    Patch:
        Удалены все пункты
NVRAM:
    Add:
        7C436110-AB2A-4BBB-A880-FE41995C9F82
            boot-args
            prev-lang:kbd
UEFI:
    Drivers:
        Удалены все пункты
```

Вот что изменено мной в `config.plist` по сравнению с `Sample.plist`, который идет с релизом OpenCore 0.87. Эти настройки для дебаг версии `config.plist` OpenCore Mod:
```
ACPI:
    Add:
        DSDT.aml
        SSDT-EC.aml
        SSDT-CPUM.aml
        SSDT-PMC.aml
        SSDT-HPET.aml
        SSDT-IMEI.aml
        SSDT-UNC.aml
        SSDT-DTGP.aml
        SSDT-SBUS-MCHC.aml
        SSDT-RTC0-RANGE-HEDT.aml
    Delete:
        Delete CpuPm
            Enable
    Quirks:
        EnableForAll
            false
Booter:
    Quirks:
        EnableForAll
            false
        SyncRuntimePermissions
            true
DeviceProperties:
    Add:
        PciRoot(0x0)/Pci(0x1,0x0)/Pci(0x0,0x0)
            built-in
                AQ==
        PciRoot(0x0)/Pci(0x1B,0x0)
            device_type
                Audio device
            layout-id
                5
            model
                C600/X79 series chipset High Definition Audio Controller
Kernel:
    Add:
        Lilu.kext
        VirtualSMC.kext
        WhateverGreen.kext
        AppleALC.kext
        RadeonSensor.kext
        SMCRadeonGPU.kext
        SMCProcessor.kext
        SMCSuperIO.kext
        FeatureUnlock.kext
        VoodooTSCSync-slice.kext
        NVMeFix.kext
        RealtekRTL8111-v2.2.2.kext
            MaxKernel
                18.9.9
        RealtekRTL8111.kext
            MinKernel
                19.0.0
        AirportBrcmFixup.kext
            MinKernel
                19.0.0
        AirportBrcmFixup.kext/Contents/PlugIns/AirPortBrcmNIC_Injector.kext
            MinKernel
                19.0.0
        USBMap.kext
            (в принципе все работает и без него)
    Quirks:
        AppleCpuPmCfgLock
            true
        AppleXcpmCfgLock
            true
        AppleXcpmExtraMsrs
            true
        CustomSMBIOSGuid
            true
        PanicNoKextDump
            true
        PowerTimeoutKernelPanic
            true
Misc:
    Boot:
        HibernateMode
            Auto
        LauncherOption
            Full
        PickerAttributes
            49
        PickerMode
            External
        PickerVariant
            Acidanthera\GoldenGate
        SkipCustomEntryCheck
            true
    Debug:
        AppleDebug
            true
        ApplePanic
            true
        DisableWatchDog
            true
        Target
            67
    Security:
        AllowSetDefault
            true
        ScanPolicy
            0
        SecureBootModel
            Disabled
        Vault
            Optional
    Tools:
        OpenShell.efi
            Enabled
NVRAM:
    Add:
        7C436110-AB2A-4BBB-A880-FE41995C9F82
            boot-args
                -v keepsyms=1 debug=0x100 npci=0x2000
PlatformInfo:
    Automatic
        false
    SMBIOS -> MacPro6,1
    Board-ID -> Mac-7BA5B2D9E42DDD94 (iMacPro1,1)
        DataHub
            BoardProduct
        PlatformNVRAM
            BID
        SMBIOS
            BoardProduct
            ChassisVersion
            SystemSKUNumber
    UpdateSMBIOSMode
        Custom
UEFI:
    APFS:
        MinDate
            -1
        MinVersion
            -1
    Audio:
        AudioSupport
            true
        PlayChime
            true
        SetupDelay
            8
    Drivers:
        OpenRuntime.efi
        OpenCanopy.efi
        HfsPlus.efi
        ResetNvramEntry.efi
        CrScreenshotDxe.efi
        AudioDxe.efi
        ext4_x64.efi
        OpenLinuxBoot.efi
    ProtocolOverrides:
        TscSyncTimeout
            500000
```

Когда вы настроили ваш хакинтош и готовы полноценно его запускать без многочисленного лог-текста при запуске, то нужно выставить следующие настройки в `config.plist`:
```
Misc:
    Debug:
        AppleDebug
            false
        ApplePanic
            false
        Target
            0
    Boot
        HideAuxilary
            true
```
А из ключей загрузки убрать 3 аргумента, а именно убрать это:
```
NVRAM:
    Add:
        7C436110-AB2A-4BBB-A880-FE41995C9F82
            boot-args
                -v keepsyms=1 debug=0x100
```

### Настройка DSDT

https://applelife.ru/threads/huananzhi-x79m-pro-razbor-poletov.2965526/#post-1010254

#### Очистка DSDT от ошибок

Прежде чем вносить какие-то правки в DSDT нужно добиться того, чтобы он сохранялся. Сохранялся оригинальный файл. При сохранении или попытке компиляции оригинального файла возникают разные ошибки. Ниже написаны те ошибки, с которыми я столкнулся и как их исправил:

1. 1915, 6049, Length is larger than Min/Max window
   - Range Maximum - Range Minimum + 1 = Length
   - https://forums.gentoo.org/viewtopic-t-954472.html
2. 4975, 6105, Invalid object type for reserved name (_PLD: found Integer at index 0, Buffer required)
   - Rehubman патч Fix _PLD Buffer/Package Error, раскомментировать следующую строку:
   - или применить альтернативный патч с ToPLD https://applelife.ru/threads/pamazhite-ispravid-ashipki-dsdt.35464/page-236#post-1015335

```
into_all all code_regex (Name\s*\(_PLD,\s*)Package(\s\([^\)].*\)[^\)]*) replaceall_matched
begin
%1Package() { Buffer%2 }
end;
```
3. 7402, 6167, _UID inside processor declaration must be an integer (found a string)
   - Удалось нагуглить только пример единичного исправления тут https://www.insanelymac.com/forum/topic/342153-question-about-a-maciasl-157-release/?do=findComment&comment=2704652 И цитату из списка изменений ACPICA тут https://github.com/acidanthera/bugtracker/issues/2031#issuecomment-1141413161
   - https://applelife.ru/threads/pamazhite-ispravid-ashipki-dsdt.35464/page-236#post-1015271
   - Для своего DSDT сделал такой патч, чтобы ручками из всех строк не извлекать hex значения (по примеру из insanelymac):

```
into_all all code_regex Name\s+\(_UID,\s*\"PCI\d\-CP(.*)\"\) replace_matched
begin
Name (_UID, 0x0%1)
end;
```
или такой патч, на основе заметки slice https://applelife.ru/threads/huananzhi-x79m-pro-razbor-poletov.2965526/#post-1010254
```
into_all all code_regex Name\s+\(_UID,\s*(\"PCI\d\-CP.*\")\) replace_matched
begin
Name (_CID, %1) // _CID: Compatible ID\n
Name (_UID, Zero) // _UID: Unique ID
end;
```
На warnings/предупреждения при попытке компиляции, я думаю, можно забить, потому что они не мешают компиляции и, наверное, работе оборудования тоже.
   
#### Исправление DSDT:

Для того, чтобы macOS корректно запускалась нужно внести некоторые корректировки в DSDT, для этого то мы и исправляли ошибки компиляции.

1. Удаление лишних сокетов и процессоров/ядер
    - Без этих правок OpenCore будет несколько раз перезагружаться при попытке запуска macOS
      - https://applelife.ru/threads/patchi-dsdt-x79-x99-x299-voprosy-reshenija.2945315/page-12#post-1018732
    - Нужно найти все сокеты, то есть `Device (SCK` и оставить только самый первый, в данном случае `Device (SCK0)`, а остальные (в данном случае SCK1, SCK2, SCK3) - удалить.
    - Также из первого сокета нужно удалить лишние ядра/процессоры, оставив только то количество, которое равно потоком процессора. В `Device (SCK0)` ищем ядра/процессоры, Обычно они называются `C000`, `C001`, `C002` и так далее, добавляя индекс в 16-ричном формате. Я оставил с `C000` по `C013` включительно, потому что это равно 20 потокам моего процессора
      - Графически это показано тут https://www.insanelymac.com/forum/topic/341600-success-huananzhi-x79-zd3-xeon-e5-2689-mojave-10146-big-sur-1123-monterey-1201/?do=findComment&comment=2794813 и тут https://www.insanelymac.com/forum/topic/350002-installing-montereybig-sur-on-all-x79-motherboard-like-huananzhi-chinese-gigabyte-asus-etc-and-powermanagement/
    - На работе Windows это сказывается негативно и чтобы избежать этого влияния - запускайте Windows из меню BIOS/UEFI, либо используйте модифицированную версию "OpenCore No ACPI"
2. Завести USB можно либо создав SSDT-USB-RESET в SSDTTime, либо в DSDT найти все `Name (_UPC` и в фигурных кавычках все только первые слова `Zero` заменить на `0xFF`
   1. https://applelife.ru/threads/huananzhi-x79m-pro-razbor-poletov.2965526/page-5#post-1017751
   2. https://applelife.ru/threads/huanan-x79-i-podobnoe-zhelezo.1947010/page-98#post-965953

#### Остальные исправления DSDT

Алгоритм работы OpenCore такой, что сначала он применяется ACPI патчи из конфига, а потом внедряет наши кастомные .aml файлы и, соответственно если есть кастомный DSDT.aml к которому мы хотим применить патчи переименования, то нам нужно будет сделать это вручную, потому что ACPI патчи в OpenCore применяются только к оригинальным DSDT.

В принципе система запускается и работает и без этих патчей и с ними тоже, так что я не знаю нужно ли их использовать, но я на всякий случай использую их в своей DSDT.

Патчи записаны в формате Base64, так как они записываются в конфиге, и если вы хотите применять их к .aml файлу, то вам нужно будет их конвертировать из Base64 в hex.

Патчи из SSDTTime:

Эти патчи были сгенерированы утилитой SSDTTime при выборе опции FixHPET. Эти патчи можно применить к файлу DSDT только к .aml файлу с помощью замены байт в каком либо hex редакторе. Например 010 Editor, Hxd или HexFiend или другие.

- HPET _CRS to XCRS Rename
    - Kl9DUlM - KlhDUlM=
- TMR IRQ 0 Patch
  - IgEAeQA= - IgAAeQA=
- RTC0 IRQ 8 Patch
  - IgABeQA= - IgAAeQA=
- Generic IRQ Patch 1 of 2 - 11 - 22F81C
  - IvgcKgAAMEcB - IvgUKgAAMEcB
- Generic IRQ Patch 2 of 2 - 11 - 22F81C
  - IvgcKgAAOHkA - IvgUKgAAOHkA


Косметические патчи:

На этой странице https://dortania.github.io/OpenCore-Install-Guide/clover-conversion/Clover-config.html говорится что эти патчи косметические, также некоторые патчи переименования нашел тут https://www.insanelymac.com/forum/topic/188920-master-chiefs-p5k-pro-acpi-warfare/?do=findComment&comment=1280888 и тут https://www.tonymacx86.com/threads/how-to-extend-the-imac-pro-to-x99-successful-build-extended-guide.227001/post-1612807

Как я уже писал - какой-то пользы или вреда от этих патчей я не заметил на macOS Monterey

- SAT0 to SATA
  - U0FUMA== - U0FUQQ==
- TMR_ to TIMR
  - VE1SXw== - VElNUg==
- PIC_ to IPIC
  - UElDXw== - SVBJQw==
- DMAD to DMAC
  - RE1BRA== - RE1BQw==
- COPR to MATH
  - Q09QUg== - TUFUSA==
- OMSC to LDRC
  - T01TQw== - TERSQw==
- CHN0 to PRT0
  - Q0hOMA== - UFJUMA==
- CHN1 to PRT1
  - Q0hOMQ== - UFJUMQ==
- EUSB to EHCI
  - RVVTQg== - RUhDSQ==
- USBE to UHCI
  - VVNCRQ== - VUhDSQ==

### Кексты

- Lilu - https://github.com/acidanthera/Lilu
- VirtualSMC - https://github.com/acidanthera/VirtualSMC
  - SMCProcessor
  - SMCSuperIO
- WhateverGreen - https://github.com/acidanthera/WhateverGreen
- AppleALC - https://github.com/acidanthera/AppleALC
- RadeonSensor - https://github.com/aluveitie/RadeonSensor
  - SMCRadeonGPU
- FeatureUnlock - https://github.com/acidanthera/FeatureUnlock
- VoodooTSCSync-slice - https://github.com/CloverHackyColor/VoodooTSCSync
  - Внутри кекста в файле `Info.plist` нужно будет вписать/заменить цифру, которая обозначает количество потоков минус 1
- NVMeFix - https://github.com/acidanthera/NVMeFix
- RealtekRTL8111-v2.2.2 - https://github.com/Mieze/RTL8111_driver_for_OS_X/releases/tag/v2.2.2
- RealtekRTL8111 - https://github.com/Mieze/RTL8111_driver_for_OS_X
- AirportBrcmFixup - https://github.com/acidanthera/AirportBrcmFixup
  - AirportBrcmFixup.kext/Contents/PlugIns/AirPortBrcmNIC_Injector.kext
- USBMap - https://github.com/corpnewt/USBMap

Я использую хакинтош для запуска macOS Monterey и macOS Mojave, и некоторые кексты не нажны или не работают в macOS Mojave. Например комбомодуль BCM4331 работает нативно в macOS Mojave, соответственно в ней не нужен кекст AirportBrcmFixup и его плагины, поэтому в конфиге для этого кекста нужно выставить в пункте `MinKernel` значение `19.0.0`, чтобы этот кекст применялся только при запуске macOS Catalina или новее.

Также в macOS Mojave не работает последняя версия кекста RealtekRTL8111 от Mieze, но работает старая версия 2.2.2 этого кекста. Соответственно кладем в папку `kexts` 2 кекста RealtekRTL8111, одни последней версии, другой версии 2.2.2 и какой-то из них переименовывем (только сам кекст-папку-директорию). Для последней версии кекста также нужно выставить в пункте `MinKernel` значение `19.0.0`, чтобы этот кекст применялся только при запуске macOS Catalina или новее, а для версии 2.2.2 нужно выставить в пункте `MaxKernel` значение `18.9.9`, чтобы этот кекст применялся только при запуске macOS Mojave или более старой версии.

Функции, которые добавляет кекст FeatureUnlock, наверное не работают, точнее их нет в macOS Mojave, поэтому для этого кекста можно тоже выставить в пункте `MinKernel` значение `21.0.0`, чтобы этот кекст применялся только при запуске macOS Monterey, но и без этого macOS Mojave запускается без проблем. Но этот кекст нужен только тем, у кого есть техника Apple, без этого кекста macOS Monterey также без проблем запускается.

Без кектса NVMeFix SSD WD Black SN730 вроде бы тоже работает нормально, но думаю этот кекст хуже не сделает. А вот при использовании кекста Innie, чтобы диски не отображались как внешние, у меня при запуске macOS появляется паника ядра.

Кексты SMCProcessor, SMCSuperIO, RadeonSensor, SMCRadeonGPU, нужны только для более корректного мониторинга датчиков с комплектующих компьютера. На работу и стабильность хакинтоша эти кексты, вроде бы, не влияют.

USBMap тоже как-бы можно не делать, потому что на материнской плате всего 13 портов (если USB 3.0 порты считать за 2 порта), а исправление UPC в DSDT, либо применение SSDT-USB-Reset решает проблему с работой всех USB портов, но вроде как многие советуют делать "карту USB портов", поэтому и я сделал.

### AML файлы

- DSDT.aml - мой DSDT, который я извлек из Windows с помощью [SSDTTime](https://github.com/corpnewt/SSDTTime) и выполнил разные исправления в [MaciASL](https://github.com/acidanthera/MaciASL)
- SSDT-EC.aml - сделал с помощью [SSDTTime](https://github.com/corpnewt/SSDTTime)
- SSDT-CPUM.aml - сделал с помощью [ssdtPRGen](https://github.com/Piker-Alpha/ssdtPRGen.sh)
- SSDT-PMC.aml - сделал с помощью [SSDTTime](https://github.com/corpnewt/SSDTTime)
- SSDT-HPET.aml - сделал с помощью [SSDTTime](https://github.com/corpnewt/SSDTTime)
- SSDT-IMEI.aml - взял из [dortania](https://dortania.github.io/Getting-Started-With-ACPI/Universal/imei.html)
- SSDT-UNC.aml - взял из [dortania](https://dortania.github.io/Getting-Started-With-ACPI/Universal/unc0.html)
- SSDT-DTGP.aml
  - В данном случае его наличие и отсутствие вроде бы не влияет на работу хакинтоша. Добавил на всякий случай.
  - Взял тут - https://github.com/jsassu20/Lenovo-ThinkPad-MacOS-Tools/blob/master/Static%20Patching/SSDT-DTGP.aml
  - https://www.tonymacx86.com/threads/modify-dsdt-for-lpcb-ionamematch.210070/#post-1395509
  - https://www.reddit.com/r/hackintosh/comments/l0t6fl/anyone_knows_what_ssdtdtgp_is/
- SSDT-SBUS-MCHC.aml - взял из [dortania](https://dortania.github.io/Getting-Started-With-ACPI/Universal/smbus.html)
  - В данном случае его наличие и отсутствие вроде бы не влияет на работу хакинтоша. Добавил на всякий случай.
- SSDT-RTC0-RANGE-HEDT.aml - взял из [dortania](https://dortania.github.io/Getting-Started-With-ACPI/Universal/awac-methods/prebuilt.html)