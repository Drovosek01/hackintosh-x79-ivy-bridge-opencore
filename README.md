# Хакинтош на x79 с процессором Ivy Bridge

Язык: Русский | [English](english-version.md)

## Содержание:
- [Железо](#Железо)
- [Что установлено](#Что-установлено)
- [Настройки BIOS/UEFI](bios-uefi-settings.md)
- [О комплектующих компьютера](about-hardware.md)
- [Результаты тестов в бенчмарках](benchmarks.md)
- [О работе операционных систем на этом хакинтоше](about-work-systems.md)
- [О хакинтоше на x79 по гайду с dortania](dortania-and-x79.md)
- [Процессоры Sandy Bridge на macOS Monterey](sandy-bridge-monterey.md)
- [О maOS Ventura на x79](about-ventura-on-x79.md)
- [Источники информации о хакинтоше](hackintosh-resources.md)
- [Небольшая предыстория и краткая история моей установки хакинтош](some-intro.md)
- [Настройки папки EFI и config.plist](efi-and-config-settings.md)
- [Процесс установки и настройки хакинтоша](setup-configure-hackintosh.md)

## Железо:
- Процессор: Intel Xeon E5-2670 v2 (Ivy Bridge-EP)
- Видеокарта: Sapphire RX580 Nitro+ 4Gb
- ОЗУ: 64GB (16 x 4) 1600 MHz Samsung ECC Reg
- Материнская плата: QIYIDA x79 v2.82 LGA2011
  - Чипсет: Intel Patsburg C600/x79
  - Сетевая карта: Realtek RL8168/8111 Ethernet
  - Аудио чип: ALC662
- Wifi + Bluetooth: Broadcom BCM4331 (BCM94331) 802.11n
- Накопители:
  - WD Black SN730 1TB - SSD NVMe
  - 2 Western Digital HDD
- Блок питания: Aerocool VX Plus 500
- Мониторы:
  - EnVision LCD2361 (1920x1080) - по DVI-D
  - HP Z23n (1920x1080) - по DisplayPort

Подробнее о этих комплектующих можете почитать в [отдельном файле](about-hardware.md), а также в [отчете AIDA64](files/Report.htm)


## Что установлено:

- macOS Monterey 12.6.1
  - [есть нюансы](about-work-systems.md)
- macOS Mojave 10.14.6
- Windows 10 22H2
  - [есть нюансы](about-work-systems.md)
- Ubuntu 22

> Все эти операционные системы установлены и работают на 1 SSD NVMe


## Что проверено и работает:

Я не нашел информации о том, что вообще может не работать в хакинтоше и что конкретно нужно проверять, поэтому информация по аналогии из гайдов-отчетов других пользователей

1. SpeedStep и Turbo Boost
   - В macOS Monterey и macOS Mojave в приложении Intel Power Gadget частота процессора меняется в диапазоне от 1.2 ГГц, до 3.3 ГГц
   - В Windows 10 в приложениях HWiNFO64 и CPUID HWMonitor частота ядер тоже меняется в этом диапазоне
     - Частота 3.3 ГГц это максимальная нагрузка на 1 ядро, при максимальной нагрузке на все ядра, частота на них составляет 2.9 ГГц
1. Аппаратное кодирование и декодирование кодеков H.264 и H.265 на видеокарте AMD RX 580
2. Wifi и Bluetooth в комбо модуле BCM4331
   - Насколько мне известно для macOS Mojave BCM4331 это нативный модуль и в этой macOS он работает без каких либо кекстов
   - В macOS Monterey пришлось использовать дополнительный кекст
   - В Windows использовал драйвера из дайверпака SDI
3. Все USB 2.0 и USB 3.0
   - Они заработали даже без кекста для USB Mapping
4. FaceTime и iMassage я просто запустил и эти приложения запустились без проблем, не знаю является ли их запуск показателем их корректной работы. У меня нет Apple устройств и эти приложения я не планирую использовать, так что мне все равно на то работают ли они
5. Trim для SSD
6. Банальные функции
   - Интернет через Ethernet
   - Звук через аудио разъем на задней части корпуса
   - 2 Full HD монитора. 1 подключен по DVI-D, другой по Display Port

## Что проверено и НЕ работает:

1. Сон
    - Это стандартная "болезнь" всех китайских плат на платформе x79. Компьютер засыпает без проблем, но просыпается с черными экранами, т.е. мониторы не выходят из сна
    - В macOS не работает ни сон ни гибернация (hubernationmode 25). В Windows 10 гибернация работает
2. Звук на передней панели корпуса
    - Для того чтобы появился стандартный значок Realtek HD Audio в трее Windows 10 мне пришлось ее переустановить. После этого я смог в этом приложении поставить галку 123 и звук заработал из порта на передней панели корпуса
    - В macOS звук не работает при подключении аудио устройств к аудио порту на передней панели корпуса. С кекстом AppleALC я перепробовал [все лэйауты](https://github.com/acidanthera/AppleALC/wiki/Supported-codecs) для кодека ALC662 (и даже дополнительные), но звук из аудио порта на передней панели так и не заработал.
    - С помощью VoodooHDA 3.01, утилиты `getdump` и подсказок `@slice` на applelife, звук с передней панели все таки заработал, но VoodooHDA не получится использовать в папке EFI
1. ASPM на комбомодуле BCM4331
    - но это в принципе не важно, ведь это хакинтош на ПК, а не на ноутбуке и на ПК энергосбережение не критично
2. Windows стабильно запускается только при запуске из меню BIOS/UEFI, а macOS иногда глючит. Об этом в [отдельном файле](about-work-systems.md) про стабильность системы