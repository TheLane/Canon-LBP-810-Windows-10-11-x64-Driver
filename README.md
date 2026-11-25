# Canon LBP-810 driver for Windows 10 / 11 (64-bit, x64) — Canon CAPT

[English](#english) · [Русский](#русский) · [Downloads / Скачать](https://github.com/TheLane/Canon-LBP-810-Windows-10-11-x64-Driver/releases/tag/v0.0.59-r4) · [Report a problem / Сообщить о проблеме](https://github.com/TheLane/Canon-LBP-810-Windows-10-11-x64-Driver/issues)

Independent Windows x64 print bridge for the legacy Canon LASER SHOT LBP-810 USB laser printer. Нативный драйвер печати Canon LBP-810 для Windows 10 и Windows 11 x64, без виртуальной машины.

> **Technical preview v0.0.59-r4:** Native printing is verified on Windows 10 Pro 22H2 x64. The standalone installer, clean Windows 10/11 installation, update and uninstall are not yet fully validated. The executable is unsigned. Do not treat this as a stable production release.
>
> **Предварительная версия v0.0.59-r4:** Нативная печать подтверждена на Windows 10 Pro 22H2 x64. Чистая установка, обновление и удаление через EXE ещё не прошли полную приёмку, Windows 11 не проверена. Установщик не подписан. Не используйте как стабильный релиз.

## English

### Download Canon LBP-810 Windows x64 driver

**[Download CanonCAPTDriver-Setup-x64.exe](https://github.com/TheLane/Canon-LBP-810-Windows-10-11-x64-Driver/releases/download/v0.0.59-r4/CanonCAPTDriver-Setup-x64.exe)** — 518,656 bytes (~507 KiB).

[Release notes and checksum](https://github.com/TheLane/Canon-LBP-810-Windows-10-11-x64-Driver/releases/tag/v0.0.59-r4) · [SHA-256](SHA256SUMS.txt)

This is an unofficial, independently developed alternative for a legacy Canon printer. It is not a Canon product or an official Canon driver. The download contains a Windows installer, not a Linux/CUPS driver, and not a conventional vendor INF driver.

### Compatibility and test status

| Printer | Status in this Windows build |
| --- | --- |
| **Canon LBP-810 / LBP810** | **Hardware-tested** via USB, VID:PID 04A9:260A |
| Canon LBP-800 / LBP800 | CAPT v1 family candidate; not enabled or tested |
| Canon LBP-1120 / LBP1120 | CAPT v1 family candidate; not enabled or tested |
| Canon LBP-1210 / LBP1210 | CAPT v1 family candidate; not enabled or tested |
| Canon LBP-3200 / LBP3200 | CAPT v1 family candidate; not enabled or tested |

The underlying [libcapt](https://github.com/darkvision77/libcapt) protocol library targets these five CAPT v1 models. This **does not mean the current Windows installer supports all of them**. Model-specific USB identification, raster profiles and physical validation are required before enabling the other devices. LBP-2900, LBP-3000, LBP-8100 and other CAPT generations are outside the current build's scope.

| Platform | Status |
| --- | --- |
| Windows 10 Pro 22H2 x64 (build 19045) | Native service and physical printing tested on an existing Windows installation |
| Clean Windows 10 x64 installation | Standalone setup validation pending |
| Windows 11 x64 | Intended target, not yet validated |
| Windows on ARM, x86, macOS, Linux | Not supported by this installer |

Verified on the LBP-810: A4 monochrome printing at 600 dpi, a two-page document followed by a separate one-page document in FIFO order, automatic paper-out recovery after paper is loaded, and automatic service restart after an idle crash without repeating completed jobs. The native binaries passed 78 automated regression tests; an offline integration test processed 101 + 1 pages. This is not a claim of unlimited hardware throughput or all possible failure scenarios being tested.

### Requirements

- Full Windows 10/11 x64 with the Windows Print Spooler, Microsoft IPP Class Driver and directed IPP discovery provider available; the installer uses the Windows .NET Framework.
- Canon LBP-810 connected directly via USB with Windows USB Printing Support (usbprint). Do not pass the printer through to a running virtual machine.
- Administrator rights for installation and maintenance. No separate Node.js, PowerShell, virtual machine or external Visual C++ runtime installation is required.
- A clean CAPT installation: this preview will refuse to overwrite an older unmanaged experimental installation.

Stripped-down Windows builds may lack essential print components; the installer does not bundle or replace Microsoft system DLLs. Use a complete, supported Windows installation. If security policy blocks the unsigned installer, do not disable security protections; wait for a signed build.

### Install

1. Download the EXE from the release page. Compare its SHA-256 with the published checksum. This checks integrity, not publisher identity.
2. Close print jobs and stop any VM using the printer. Connect the printer by USB.
3. Run the installer and review the normal Windows administrator (UAC) prompt. Choose **Install** in the confirmation dialog.
4. Open **Settings → Printers & scanners**. Find **Canon LBP-810** and select it from the normal Print dialog in an application.
5. Print a one-page A4 test document first. If paper runs out, load paper and allow the active job to resume. Do not repeatedly resubmit an uncertain job.

The installer is a technical preview: if setup fails, record the exact stage and error from its dialog. It retains a protected setup log under ProgramData. Do not repeatedly run setup over partially installed components.

### Update or uninstall

For an installation created successfully by this managed setup revision:

1. Finish printing and make sure the queue is empty.
2. Run the downloaded installer again with administrator approval.
3. In the maintenance dialog, select **Yes** for update/repair, **No** for uninstall, or **Cancel** to exit. Confirm the operation. The EXE also accepts `--update` or `--uninstall`.
4. On removal, the program is designed to remove only the Canon CAPT queue and its service, leaving the shared Windows IPP class driver installed. Protected archives of code and terminal job journals are retained. Updates retain a backup of the previous code and attempt rollback on a verified safe failure.

The maintenance paths are implemented and passed file-based validation tests but have not yet been tested end-to-end on a clean system. They refuse pending or uncertain jobs and unmanaged installations. There is not yet an entry in Windows Installed apps. Keep the installer for maintenance. If a maintenance operation fails, preserve its diagnostic report and do not delete files or shared Windows components manually.

### How it works

```text
Windows application (Word, PDF viewer, etc.)
    -> Windows Print Spooler / Microsoft IPP Class Driver
    -> local C++ IPP endpoint (127.0.0.1:18631)
    -> protected disk-backed FIFO queue / PWG Raster
    -> native C++ CAPT v1 and SCoA worker (libcapt)
    -> Windows USB Printing Support -> Canon LBP-810
```

The printer is not an IPP printer itself. The bridge runs as a dedicated Windows service with a virtual service account. The local endpoint listens only on loopback; the native worker handles the CAPT protocol and USB communication. Documents are streamed through disk-backed storage; there is no intentional fixed total page or job count limit. The service journals dispatch intent and page confirmations, waits for recoverable mechanical conditions and does not blindly replay uncertain physical output. Physical jams still require manual clearance.

Windows manages the IPP port; the printer properties may show a WSD-* port name and Microsoft IPP Class Driver as the model. This is expected. There is no Node.js in the native runtime.

### Support

Please report installation, print, recovery or compatibility issues under [Issues](https://github.com/TheLane/Canon-LBP-810-Windows-10-11-x64-Driver/issues). Include the installer version, printer model, Windows version/build and architecture, exact error, and whether pages physically printed. Do not post document contents, usernames, USB serial numbers, or unredacted logs. A model request is welcome but is not a claim of current support.

### License and acknowledgements

Binary distribution only; application source code is not published in this repository. The CAPT v1 and SCoA implementation uses [libcapt](https://github.com/darkvision77/libcapt) by Andrey Antufyev, licensed under BSD 2-Clause. Its full copyright and license notice is included in [licenses/libcapt-LICENSE.txt](licenses/libcapt-LICENSE.txt) and the installer. Canon and LASER SHOT are names of their respective owner. No affiliation with or endorsement by Canon is implied.

---

## Русский

### Скачать драйвер Canon LBP-810 для Windows 10 / 11 x64 (64 бит)

**[Скачать CanonCAPTDriver-Setup-x64.exe](https://github.com/TheLane/Canon-LBP-810-Windows-10-11-x64-Driver/releases/download/v0.0.59-r4/CanonCAPTDriver-Setup-x64.exe)** — 518 656 байт (~507 КиБ).

[Страница версии и контрольная сумма](https://github.com/TheLane/Canon-LBP-810-Windows-10-11-x64-Driver/releases/tag/v0.0.59-r4) · [SHA-256](SHA256SUMS.txt)

Неофициальный независимый драйвер-мост для старого лазерного USB-принтера Canon LASER SHOT LBP-810. Печать из обычных приложений Windows без виртуальной машины, Node.js и ручного преобразования документов. Это не официальный драйвер Canon и не обычный INF-пакет производителя.

### Совместимость

| Принтер | Состояние в этой Windows-версии |
| --- | --- |
| **Canon LBP-810 / LBP810** | **Проверен на реальном устройстве**, USB 04A9:260A |
| Canon LBP-800 / LBP800 | Кандидат семейства CAPT v1, не включён и не испытан |
| Canon LBP-1120 / LBP1120 | Кандидат семейства CAPT v1, не включён и не испытан |
| Canon LBP-1210 / LBP1210 | Кандидат семейства CAPT v1, не включён и не испытан |
| Canon LBP-3200 / LBP3200 | Кандидат семейства CAPT v1, не включён и не испытан |

Библиотека [libcapt](https://github.com/darkvision77/libcapt) рассчитана на эти пять моделей CAPT v1. Это не означает, что все они работают с текущим установщиком Windows. Для остальных моделей необходимы проверка USB-идентификаторов, настройка профилей растра и испытание на физическом устройстве. LBP-2900, LBP-3000, LBP-8100 и другие поколения CAPT пока не поддерживаются.

| Система | Состояние |
| --- | --- |
| Windows 10 Pro 22H2 x64 (19045) | Нативная служба и физическая печать проверены на существующей системе |
| Чистая Windows 10 x64 | Приёмка самостоятельной установки ещё не завершена |
| Windows 11 x64 | Целевая система, ещё не испытана |
| Windows ARM, x86, macOS, Linux | Не поддерживаются этим установщиком |

Подтверждены монохромная печать A4 при 600 dpi, печать двухстраничного и следом одностраничного документов, автоматическое продолжение после загрузки бумаги, автоматический перезапуск службы после сбоя в простое без повторения завершённых заданий. Нативные файлы прошли 78 автоматических регрессионных тестов; офлайн-тест обработал 101 + 1 страницу. Все аппаратные сбои и чистая установка ещё не проверены.

### Системные требования

- Полноценная Windows 10/11 x64 со штатной службой печати, Microsoft IPP Class Driver и системным провайдером направленного IPP-обнаружения; установщик использует штатный .NET Framework.
- Canon LBP-810, подключённый по USB через Windows USB Printing Support (usbprint). Принтер не должен быть захвачен запущенной виртуальной машиной.
- Права администратора для установки и обслуживания. Отдельно ставить Node.js, PowerShell, виртуальную машину или Visual C++ Runtime не требуется.
- Отсутствие старой неуправляемой экспериментальной установки CAPT: эта версия не будет её перезаписывать.

Урезанные сборки Windows могут не содержать необходимые компоненты печати. Установщик не заменяет системные DLL Microsoft. Используйте полноценную поддерживаемую Windows. Если политика безопасности блокирует неподписанный установщик, не отключайте защиту; дождитесь подписанного выпуска.

### Установка

1. Скачайте EXE со страницы версии и сравните SHA-256 с опубликованной контрольной суммой. Она проверяет целостность, но не подтверждает личность издателя.
2. Дождитесь завершения заданий, остановите виртуальную машину, если она использует принтер, и подключите принтер по USB.
3. Запустите установщик. Проверьте штатный запрос UAC и подтвердите установку.
4. Откройте **Параметры → Принтеры и сканеры**. В списке должен появиться **Canon LBP-810**. Выберите его в стандартном диалоге печати.
5. Для начала распечатайте одну страницу A4. При отсутствии бумаги загрузите её и дождитесь продолжения текущего задания. Не отправляйте повторно документ с неизвестным результатом печати.

Если установка завершится ошибкой, сохраните точный этап, сообщение и путь к защищённому журналу, показанные в диалоге. Не запускайте установку повторно поверх неизвестного частично установленного состояния.

### Обновление и удаление

Для установки, успешно созданной этой редакцией управляемого установщика:

1. Дождитесь окончания печати и убедитесь, что очередь пуста.
2. Повторно запустите скачанный EXE с подтверждением прав администратора.
3. В диалоге обслуживания **Yes (Да)** — обновление или восстановление установки, **No (Нет)** — удаление, **Cancel (Отмена)** — выход. Подтвердите выбранное действие. Также поддерживаются аргументы `--update` и `--uninstall`.
4. При удалении предусмотрено удаление только очереди CAPT и соответствующей службы. Общий драйвер Microsoft IPP сохраняется. Код и журналы завершённых заданий остаются в защищённых архивах. При обновлении сохраняется предыдущая версия для безопасного отката.

Обслуживание реализовано и проверено файловыми тестами, но реальные обновление и удаление ещё не испытаны на чистой Windows. Неопределённые или незавершённые задания блокируют обслуживание. Записи в списке установленных приложений пока нет — сохраните установщик. При ошибке не удаляйте системные компоненты и файлы вручную.

### Принцип работы

```text
Приложение Windows
    -> штатная очередь печати / Microsoft IPP Class Driver
    -> локальный IPP-сервер C++ (127.0.0.1:18631)
    -> защищённая дисковая очередь FIFO / PWG Raster
    -> нативное ядро C++ CAPT v1 и сжатие SCoA (libcapt)
    -> Windows USB Printing Support -> Canon LBP-810
```

Сам принтер не поддерживает IPP. Мост работает как отдельная системная служба с виртуальной учётной записью. IPP-сервер слушает только локальный интерфейс. Документы обрабатываются потоково с дисковой очередью без искусственного общего лимита числа страниц или заданий. Подтверждения страниц и намерение передачи задания сохраняются в журнале. При восстановимой механической ошибке драйвер ожидает устранения причины; при неизвестном результате физической печати слепого повторения нет. Замятие бумаги устраняет человек.

Windows управляет IPP-портом, поэтому имя WSD-* и модель Microsoft IPP Class Driver в свойствах очереди нормальны. Node.js в нативном драйвере не используется.

### Обратная связь

[Создайте Issue](https://github.com/TheLane/Canon-LBP-810-Windows-10-11-x64-Driver/issues), если обнаружили ошибку установки, печати, восстановления или хотите предложить модель для проверки. Укажите версию установщика, модель принтера, выпуск и номер сборки Windows, архитектуру, точную ошибку и факт физической печати. Не публикуйте содержимое документов, имена пользователей, серийные номера или неочищенные журналы.

### Лицензия и благодарности

Репозиторий предназначен для бинарного распространения; исходники приложения не публикуются. Реализация CAPT v1 и SCoA использует [libcapt](https://github.com/darkvision77/libcapt) Андрея Антуфьева, лицензия BSD 2-Clause. Полный текст уведомления о правах и лицензии включён в [licenses/libcapt-LICENSE.txt](licenses/libcapt-LICENSE.txt) и в установщик. Canon и LASER SHOT — обозначения соответствующего правообладателя. Проект не связан с Canon и не одобрен компанией.
