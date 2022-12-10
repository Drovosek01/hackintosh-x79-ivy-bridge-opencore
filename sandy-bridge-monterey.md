## Процессоры Sandy Bridge на macOS Monterey

Насколько мне известно, для того чтобы процессоры Intel семейства Sandy Bridge корректно работали на OS X 10.10 и новее, то для них нужно применять специальные патчи ядра. Это нужно для корректной работы технологий энергосбережения, таких как SpeedStep, Turbo Boost и так далее. В общем чтобы корректно работал Power Management.

Для каждой версии macOS для Sandy Bridge эти патчи энергосбережения разные, если я правильно понял.

Вот что я нашел по этой теме:
- https://elitemacx86.com/threads/how-to-enable-intel-cpu-power-management-on-x79-c606-motherboards-sandy-bridge-e-ep-and-ivy-bridge-e-ep.423/
- https://www.insanelymac.com/forum/topic/324270-sandy-bridge-e-ivy-bridge-e-power-management-1013-appstore-release/
- https://www.insanelymac.com/forum/topic/346988-sandy-bridge-e-power-management-big-sur-1121-big-sur-114/
- https://www.olarila.com/topic/21661-mac-os-montereybig-sur-on-huananzhi-and-other-x79-motherboard-and-powermanagement
- https://www.insanelymac.com/forum/topic/350002-installing-montereybig-sur-on-all-x79-motherboard-like-huananzhi-chinese-gigabyte-asus-etc-and-powermanagement/
- https://www.hackintosh-forum.de/forum/thread/55510-install-monterey-big-sur-on-any-x79-motherboard-huananzhi-chinese-gigabyte-etc-a/
- https://github.com/luchina-gabriel/BASE-EFI-INTEL-DESKTOP-2NDGEN-SANDY-BRYDGE

А для macOS Monterey 12.3 и новее вроде бы есть специальный кекст вместо патчей, который исправляет проблему с функциями энергосбережения на Sandy Bridge:
- https://www.insanelymac.com/forum/topic/346988-sandy-bridge-e-power-management-big-sur-1121-big-sur-114/?do=findComment&comment=2790711
- https://www.reddit.com/r/hackintosh/comments/u3a4ry/is_power_management_configurable_on_monterey_for/
- https://github.com/dortania/OpenCore-Legacy-Patcher/commit/f7f890d37e01b79d0926824ac424da897762431b
- ACPI_SMC_PlatformPlugin Override (ASPP-Override)
  - https://github.com/dortania/OpenCore-Legacy-Patcher/tree/main/payloads/Kexts/Misc
    - https://github.com/dortania/OpenCore-Legacy-Patcher/blob/main/payloads/Kexts/Misc/ASPP-Override-v1.0.1.zip
  - https://github.com/axivo/opencore/tree/main/files
    - https://github.com/axivo/opencore/blob/main/files/ASPP-Override-1.0.1-DEBUG.zip
    - https://github.com/axivo/opencore/blob/main/files/ASPP-Override-1.0.1-RELEASE.zip

При использовании патчей ядра и macOS 12.3 или новее, вроде как нужно использовать только 3 патча:
```
751AB9 to EB11B9

EB064531E4 to EB034531E4

3E7539 to 3E9090
```
Обычно эти патчи представляются в виде hex-значений, то есть так как они отображаются в PlistEdit Pro и OpenCore Configurator, но в `.plist` файлах эти значения хранятся в виде Base64 кода и прежде чем их записывать вручную в конфиг-файл их нужно конвертировать из hex в base64.

В конфиге OpenCore их нужно добавить в `Kernel -> Patch`. Это будет выглядеть так:
```
<dict>
    <key>Arch</key>
    <string>Any</string>
    <key>Base</key>
    <string></string>
    <key>Comment</key>
    <string>Power Management Patch #1</string>
    <key>Count</key>
    <integer>0</integer>
    <key>Enabled</key>
    <true/>
    <key>Find</key>
    <data>PnU5</data>
    <key>Identifier</key>
    <string>com.apple.driver.AppleIntelCPUPowerManagement</string>
    <key>Limit</key>
    <integer>0</integer>
    <key>Mask</key>
    <data></data>
    <key>MaxKernel</key>
    <string></string>
    <key>MinKernel</key>
    <string>21.4.0</string>
    <key>Replace</key>
    <data>PpCQ</data>
    <key>ReplaceMask</key>
    <data></data>
    <key>Skip</key>
    <integer>0</integer>
</dict>
<dict>
    <key>Arch</key>
    <string>Any</string>
    <key>Base</key>
    <string></string>
    <key>Comment</key>
    <string>Power Management Patch #2</string>
    <key>Count</key>
    <integer>0</integer>
    <key>Enabled</key>
    <true/>
    <key>Find</key>
    <data>dRO5</data>
    <key>Identifier</key>
    <string>com.apple.driver.AppleIntelCPUPowerManagement</string>
    <key>Limit</key>
    <integer>0</integer>
    <key>Mask</key>
    <data></data>
    <key>MaxKernel</key>
    <string></string>
    <key>MinKernel</key>
    <string>21.4.0</string>
    <key>Replace</key>
    <data>6xO5</data>
    <key>ReplaceMask</key>
    <data></data>
    <key>Skip</key>
    <integer>0</integer>
</dict>
<dict>
    <key>Arch</key>
    <string>Any</string>
    <key>Base</key>
    <string></string>
    <key>Comment</key>
    <string>Power Management Patch #3</string>
    <key>Count</key>
    <integer>0</integer>
    <key>Enabled</key>
    <true/>
    <key>Find</key>
    <data>6wZFMeQ=</data>
    <key>Identifier</key>
    <string>com.apple.driver.AppleIntelCPUPowerManagement</string>
    <key>Limit</key>
    <integer>0</integer>
    <key>Mask</key>
    <data></data>
    <key>MaxKernel</key>
    <string></string>
    <key>MinKernel</key>
    <string>21.4.0</string>
    <key>Replace</key>
    <data>6wNFMeQ=</data>
    <key>ReplaceMask</key>
    <data></data>
    <key>Skip</key>
    <integer>0</integer>
</dict>
```

Эти патчи я не проверял, у меня процессор поколения Ivy Bridge и для него не нужны все эти патчи.