## О maOS Ventura на x79

macOS Ventura 13 официально не поддерживается на процессорах без AVX 2.0, то есть на всех процессорах Intel Ivy Bridge, Sandy Bridge и более старых.

Но есть нюансы. Вроде как Apple оставила код, который позволяет работать без AVX 2, в версии macOS для Apple Silicon процессоров и разработчики OpenCore Legacy Patcher (OCLP) вроде бы выяснили это - https://github.com/dortania/OpenCore-Legacy-Patcher/issues/998

Также вот тут есть видео подтверждение того, что на Ivy Bridge + RX 560 можно заставить работать macOS Ventura - https://www.youtube.com/watch?v=ngTSkFuNIzA

Также в этих видео показано как можно подменить кэш и в теории заставить работать macOS Ventura на Ivy Bridge - https://www.youtube.com/watch?v=0jUsJpzgZnU и https://www.youtube.com/watch?v=tQbX2gQbsiY

---

Я узнал о кексте [CryptexFixup](https://github.com/acidanthera/CryptexFixup) и как я понял из его описания, он сам в автоматическом режиме проводит необходимые патчи для того, чтобы заставить работать macOS Ventura на процессорах без AVX2.0

Я ради эксперимента попробовал сделать вот что:
1. Из под работающей macOS Monterey создал загрузочную флешку с macOS Ventura
2. В папку EFI на своем хакинтоше (с которой у ме) добавил кекст CryptexFixup и добавил его также в конфиг
3. Установил macOS Ventura с загрузочной флешки
4. Потом на этапе создания учетной записи у меня экран отображался полностью черным

Я не стал тратить время на выяснение причин этой проблемы. Возможно я что-то сделал не так или CryptexFixup не может сам провести все патчи и кэши нужно вручную заменять воспользовавшись терминалом - мне это было не интересно. Небольшая попытка установить Ventura также, как Monterey, просто добавив 1 кекст для меня не увенчалась успехом.

---

На данный момент последняя релизная версия macOS Ventura это 13.0.1, а macOS Monterey это 12.6.1 и как человек не имеющий Apple гаджетов, я не вижу пользу, которую получу от использовани macOS Ventura, вместо macOS Monterey.

На macOS Ventura часто некорректно работает окно сохранения Photoshop, не работает пиратская активация Autocad программ на Intel и возможно есть еще какие-то негативные аспекты, которые я не вспомнил. Тем более, судя по цифрам в версии macOS Ventura, она не особо свежая, а значит не особо стабильная.