## О хакинтоше на x79 по гайду с dortania

[dortania](https://dortania.github.io/) это популярный и хороший сборник инструкций на разное оборудование, но строго следуя инструкции с dortania по настройке платформы x79 мне не удалось даже добраться до экрана запуска установки macOS Monterey на моей платформе x79 с Ivy Bridge.

Ниже я покажу что предлагается на dortania добавить в папку EFI и в конфиг OpenCore для запуска и работы в macOS на платформе x79. Там не указывается для какой именно версии macOS эта инструкция, но последняя стабильная версия macOS это macOS Ventura и на ней это не будет работать 100%, потому что у Sandy Bridge и Ivy Bridge процессоров нет инструкций AVX 2.0, необходимых для работы macOS Ventura "из коробки", а я устанавливал на свою платформу x79 по этому гайду macOS Monterey.

- Ivy Bridge Desktop
  - https://dortania.github.io/OpenCore-Install-Guide/config.plist/ivy-bridge.html
- Ivy Bridge Laptop
  - https://dortania.github.io/OpenCore-Install-Guide/config-laptop.plist/ivy-bridge.html
- Sandy and Ivy Bridge-E HEDT
  - https://dortania.github.io/OpenCore-Install-Guide/config-HEDT/ivy-bridge-e.html

Настройки с dortania для Sandy and Ivy Bridge-E HEDT:
```
ACPI
    Add
        SSDT-EC
        SSDT-UNC
DeviceProperties
    Add
        PciRoot(0x0)/Pci(0x1b,0x0)
            device-id
                33150000
Kernel
    Quirks
        AppleCpuPmCfgLock
            true
        AppleXcpmExtrasMsrs
            true
        DisableIoMapper
            true
        PanicNoKextDump
            true
        PowerTimeoutKernelPanic
            true
Misc
    Boot
        HideAuxiliary
            true
    Debug
        AppleDebug
            true
        ApplePanic
            true
        DisableWatchDog
            true
        Target
            67
    Security
        AllowSetDefault
            true
        BlacklistAppleUpdate
            true
        ScanPolicy
            0
        SecureBootModel
            Default
        Vault
            Optional
NVRAM
    Add
        7C436110-AB2A-4BBB-A880-FE41995C9F82
            boot-args
                -v debug=0x100 keepsyms=1 alcid=1
PlatformInfo
    Generic
        SystemProductName
            MacPro6,1
UEFI
    APFS
        MinVersion
            -1
        MinDate
            -1
    Quirks
        IgnoreInvalidFlexRatio
            true
    Drivers
        OpenRuntime.efi
        HfsPlus.efi
```

- На страницах с Ivy Bridge для ПК, ноутбуков и "рабочих станций" довольно много различий, хотя, мне кажется, различий между ПК и рабочими станциями, на ой взгляд, тут быть не должно, разве что в присутствии встроенной графики на ПК
- На странице с Sandy and Ivy Bridge-E не написано про необходимость использования аргумента загрузки `npci=0x2000` или `npci=0x3000`
- На странице Ivy Bridge для ПК или ноутбуков там предлагается использовать SSDT-PLUG, судя по красной обводке на скриншоте, хотя SSDT-PLUG работает только на процессорах поколения Haswell или новее, насколько мне известно
- На странице с Sandy and Ivy Bridge-E ничего не написано про SSDT-CPUM (о скрипте ssdtPRGen), хотя на странице с Ivy Bridge Desktop есть информация, что нужно добавить этот SSDT
- Для Sandy Bridge нет патчей для энергосбережения как на странице с конфигом для HEDT, так и на странице с отдельной настройкой Power Management для Sandy и Ivy Bridge
- В разделе Post-Install ничего не сказано про то, что для RX 580 при использовании SMBIOS нужно выставить Board-ID от `iMacPro1`,1, чтобы заработало аппаратное ускорение на видеокарте или хотябы сменить SMBIOS на iMacPro1,1
  - shiki-агументы загрузки, о которых [написано](https://dortania.github.io/GPU-Buyers-Guide/misc/bootflag.html#amd-boot-arguments) в разделе GPU Buyers Guide начиная с macOS Big Sur больше не работают, насколько мне известно
- На [странице](https://dortania.github.io/Wireless-Buyers-Guide/unsupported.html#catalina-10-15-and-older) раздела Wireless Buyers Guide написано, что модуль BCM94331 (BCM4331) поддерживается только на macOS Catalina или более старых версиях macOS, но мой опыт показал, что этот модуль можно заставить работать и на macOS Monterey (и в меню установки macOS Ventura значок Wifi тоже отображался, но с восклицательным знаком, не знаю что значит восклицательный знак в данном случае)
  - Хотя "намек" на это есть на [странице](https://dortania.github.io/OpenCore-Install-Guide/extras/big-sur/#known-issues) с особенностями macOS Big Sur
- [Страница](https://dortania.github.io/OpenCore-Install-Guide/extras/big-sur/) с особенностями macOS Big Sur скрыта из бокового меню и вместо нее там страница с особенностями macOS Monterey, хотя можно же было оставить оба пункта
- На [странице](https://dortania.github.io/OpenCore-Install-Guide/ktext.html#extras) с кекстами не указано, что CpuTscSync не будет работать при отсутствии в процессоре регистра TSC_ADJUST

Такое ощущение, что инструкция по настройке Sandy and Ivy Bridge-E HEDT была написана исключительно для брендовых материнских плат, таких как SuperMicro, HP, ASUS и кто там еще делает серверные материнские платы. Потому что на брендовых материнских плат меньше проблем с настройкой хакинтош и работает сон, в отличие от китайских "серверных" материнских плат. Либо это была чисто гипотетическая инструкция, ни разу не применяемая авторами на практике, тогда странно, что об этом нигде нет предупреждения.

Да, некоторые из перечисленных особенностей связаны с конкретной работой конкретных кекстов, но ведь для некоторых кекстов указываются эти особенности. например для RealtekRTL8111 указывается, что для старых версия macOS нужно использовать старую версию кекста

Уверен если потратить больше времени на анализ, то можно найти еще кучу некорректной информации

---

dortania не плохой ресурса для начала изучения хакинтоша, но там много разрозненной и не актуально (и даже ложной) информации, которая может запутать новичков, которые загорелись желание настроить хакинтош и делать все строго по инструкции.

Если бы на страницах с настройкой конфиг файлов было написано для какой версии macOS ориентированы эти инструкции, а также то, что эти инструкции гипотетические, либо проверены на практике с какими-то конкретными материнским платами и процессорами, то, думаю, новичкам было бы проще разобраться и понять, что слепо следовать этим инструкциям не стоит.

Таким образом, я сделал вывод, что инструкция по настройке платформы x79 на dortania, на мой взгляд, не пригодна к использованию. По крайней мере в паре с китайскими материнскими платами.