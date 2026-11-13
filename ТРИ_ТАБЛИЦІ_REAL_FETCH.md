# Real Fetch 36 — порівняння 15 мов / 18 варіантів

**Дата:** 13.11.2026 · **Linux x86-64** · **36 реальних системних полів** · **38–39 вихідних файлів на мову**.

**Важливо:** Це одна і та сама програма, що читає Linux `/proc` і `/etc/os-release`. Це **не** повний порт Quazifetch/Macchina й не повноцінний стрес-тест великих кодових баз. Кожна реалізація має 36 окремих функціональних модулів-провайдерів, спільний модуль доступу до файлів і точку входу.

**Коректність:** усі 18 варіантів успішно виконалися. Усі вивели 36 непорожніх полів; 16 стабільних полів (дистрибутив, ядро, hostname, CPU, загальна RAM, PID limit та ін.) порівняно між реалізаціями без розбіжностей. Динамічні показники, як-от uptime і free RAM, можуть природно змінюватись між запусками.

**Виправлення QZI:** QZI підготовлено окремо (`qz build -i`, три повні збірки без кешу; медіана **605.783 мс**). У таблиці виконання — п’ять запусків **самого `real_fetch.qzi`** через `qz run --jit`; медіана **11.057 мс**. Розмір артефакту — **49195 байтів (48.04 КіБ)**. Перевірені всі 36 полів.

**Метод:** GCC/Clang/G++/Clang++/Fortran `-O0`; Rust `-C opt-level=0`; Zig `Debug`; Swift `-Onone`; Quazi стандартний native backend з `--no-incremental` і `-s`; Go/Java/Kotlin у штатних режимах. Три повні виклики компілятора для кожної компільованої реалізації, п’ять окремих запусків, у таблицях **медіана повторів конкретно цієї програми**. Результати залежать від контейнера та прогріву файлового кешу.

## Таблиця 1 — час повної компіляції

| Варіант | Компіляція, мс | Повторів | Примітка |
|---|---:|---:|---|
| Quazi AOT | 552.550 | 3 | AOT → executable |
| GCC C -O0 | 570.292 | 3 | AOT → executable |
| Clang C -O0 | 642.308 | 3 | AOT → executable |
| G++ -O0 | 586.561 | 3 | AOT → executable |
| Clang++ -O0 | 652.630 | 3 | AOT → executable |
| Rust -O0 | 90.574 | 3 | AOT → executable |
| Zig Debug | 455.164 | 3 | AOT → executable |
| Go | 148.412 | 3 | AOT → executable |
| Java | 556.768 | 3 | Побудова всіх .class, без запуску JVM |
| Kotlin/JVM | 4 829.903 | 3 | Побудова .jar із stdlib |
| Swift -Onone | 4 299.318 | 3 | -static-stdlib, але ELF динамічний |
| Fortran -O0 | 676.583 | 3 | AOT → executable |
| Quazi JIT | 605.783 | 3 | Окрема компіляція `.qz` → **`.qzi`**; не входить до часу запуску |
| Python | — | — | Немає окремої AOT-компіляції; час запуску в таблиці 2 |
| Node.js | — | — | Немає окремої AOT-компіляції; час запуску в таблиці 2 |
| Ruby | — | — | Немає окремої AOT-компіляції; час запуску в таблиці 2 |
| PHP | — | — | Немає окремої AOT-компіляції; час запуску в таблиці 2 |
| Perl | — | — | Немає окремої AOT-компіляції; час запуску в таблиці 2 |

## Таблиця 2 — час повного запуску та роботи програми

| Варіант | Час, мс | Повторів | Враховано |
|---|---:|---:|---|
| Quazi AOT | 4.030 | 5 | запуск процесу + усі 36 читань Linux API |
| GCC C -O0 | 1.159 | 5 | запуск процесу + усі 36 читань Linux API |
| Clang C -O0 | 1.301 | 5 | запуск процесу + усі 36 читань Linux API |
| G++ -O0 | 1.240 | 5 | запуск процесу + усі 36 читань Linux API |
| Clang++ -O0 | 1.180 | 5 | запуск процесу + усі 36 читань Linux API |
| Rust -O0 | 1.754 | 5 | запуск процесу + усі 36 читань Linux API |
| Zig Debug | 1.799 | 5 | запуск процесу + усі 36 читань Linux API |
| Go | 3.243 | 5 | запуск процесу + усі 36 читань Linux API |
| Java | 63.477 | 5 | запуск JVM + читання |
| Kotlin/JVM | 122.423 | 5 | запуск JVM + читання |
| Swift -Onone | 7.154 | 5 | запуск процесу + усі 36 читань Linux API |
| Fortran -O0 | 2.561 | 5 | запуск процесу + усі 36 читань Linux API |
| Quazi JIT | 11.057 | 5 | `qz run --jit real_fetch.qzi`; frontend `.qz` не запускається |
| Python | 702.564 | 5 | запуск інтерпретатора + імпорти 36 модулів + читання |
| Node.js | 37.015 | 5 | запуск інтерпретатора + імпорти 36 модулів + читання |
| Ruby | 56.600 | 5 | запуск інтерпретатора + імпорти 36 модулів + читання |
| PHP | 13.190 | 5 | запуск інтерпретатора + імпорти 36 модулів + читання |
| Perl | 13.699 | 5 | запуск інтерпретатора + імпорти 36 модулів + читання |

## Таблиця 3 — розмір артефактів

| Варіант | До strip, КіБ | Після strip, КіБ | Скрипти, КіБ | Тип |
|---|---:|---:|---:|---|
| Quazi AOT | 68.27 | 68.27 | — | Static ELF; built-in -s; GNU strip cannot remove sections |
| GCC C -O0 | 751.20 | 668.44 | — | Static ELF (GNU strip) |
| Clang C -O0 | 759.35 | 676.54 | — | Static ELF (GNU strip) |
| G++ -O0 | 751.91 | 668.44 | — | Static ELF (GNU strip) |
| Clang++ -O0 | 760.05 | 676.54 | — | Static ELF (GNU strip) |
| Rust -O0 | 5 377.11 | 1 186.47 | — | Static ELF (GNU strip) |
| Zig Debug | 13 503.65 | 3 368.49 | — | Static ELF (GNU strip) |
| Go | 2 258.75 | 1 475.55 | — | Static ELF (GNU strip) |
| Java | 17.91 | 17.91 | — | JVM .class bundle (all classes; excludes JVM) |
| Kotlin/JVM | 4 849.69 | 4 849.69 | — | JVM fat JAR (bundled Kotlin stdlib; excludes JVM) |
| Swift -Onone | 67 617.02 | 55 123.20 | — | Dynamic ELF (-static-stdlib is not full static) |
| Fortran -O0 | 1 255.90 | 1 128.95 | — | Static ELF (GNU strip) |
| Quazi JIT | 48.04 | 48.04 | — | **Portable QZI v14**, без розміру runtime `qz`; до QZI `strip` не застосовується |
| Python | — | — | 5.84 | Source files only; runtime/compiler excluded |
| Node.js | — | — | 6.25 | Source files only; runtime/compiler excluded |
| Ruby | — | — | 5.25 | Source files only; runtime/compiler excluded |
| PHP | — | — | 7.40 | Source files only; runtime/compiler excluded |
| Perl | — | — | 7.63 | Source files only; runtime/compiler excluded |

**Обмеження таблиці 3:** для **Quazi JIT** показано розмір реально скомпільованого **QZI** (не ELF), без runtime `qz`. Для Python/Node/Ruby/PHP/Perl показано лише сумарний розмір джерел без інтерпретаторів. Для JVM — `.class` або `.jar` **без розміру встановленої JVM**. `Swift -static-stdlib` не створив повністю статичний ELF. Quazi-бінарник статичний і не має таблиці секцій, GNU `strip` його не зменшує; власний `qz build -s` використано під час збірки. Отже, розміри Java і скриптів **не можна напряму зіставляти** з автономними ELF.

## Що робить Real Fetch

1. **Hostname** — `/proc/sys/kernel/hostname`
2. **Kernel** — `/proc/sys/kernel/osrelease`
3. **OS type** — `/proc/sys/kernel/ostype`
4. **Distribution** — `/etc/os-release` (`PRETTY_NAME=`)
5. **Distro ID** — `/etc/os-release` (`ID=`)
6. **Distro version** — `/etc/os-release` (`VERSION_ID=`)
7. **CPU model** — `/proc/cpuinfo` (`model name	:`)
8. **CPU vendor** — `/proc/cpuinfo` (`vendor_id	:`)
9. **CPU family** — `/proc/cpuinfo` (`cpu family	:`)
10. **CPU stepping** — `/proc/cpuinfo` (`stepping	:`)
11. **CPU MHz** — `/proc/cpuinfo` (`cpu MHz		:`)
12. **CPU threads** — `/proc/cpuinfo` (`processor	:`)
13. **CPU model ID** — `/proc/cpuinfo` (`model		:`)
14. **CPU microcode** — `/proc/cpuinfo` (`microcode	:`)
15. **RAM total** — `/proc/meminfo` (`MemTotal:`)
16. **RAM available** — `/proc/meminfo` (`MemAvailable:`)
17. **RAM free** — `/proc/meminfo` (`MemFree:`)
18. **RAM buffers** — `/proc/meminfo` (`Buffers:`)
19. **RAM cache** — `/proc/meminfo` (`Cached:`)
20. **RAM active** — `/proc/meminfo` (`Active:`)
21. **RAM inactive** — `/proc/meminfo` (`Inactive:`)
22. **Swap total** — `/proc/meminfo` (`SwapTotal:`)
23. **Swap free** — `/proc/meminfo` (`SwapFree:`)
24. **System uptime** — `/proc/uptime`
25. **Load average** — `/proc/loadavg`
26. **Boot timestamp** — `/proc/stat` (`btime `)
27. **Process forks** — `/proc/stat` (`processes `)
28. **Context switches** — `/proc/stat` (`ctxt `)
29. **Mounts** — `/proc/mounts`
30. **Net interfaces** — `/proc/net/dev`
31. **TCP sockets** — `/proc/net/tcp`
32. **UDP sockets** — `/proc/net/udp`
33. **PID maximum** — `/proc/sys/kernel/pid_max`
34. **Thread maximum** — `/proc/sys/kernel/threads-max`
35. **Random entropy** — `/proc/sys/kernel/random/entropy_avail`
36. **Filesystem types** — `/proc/filesystems`

## Відтворення

В архіві `src/<language>/` містяться всі вихідні коди. Приклади компіляції:

```bash
# Quazi (встанови QUAZI_STD_ROOT і QUAZI_PRELUDE_ROOT)
qz build src/qz/main.qz --no-incremental -s -o fetch-qz
# Quazi: попередня збірка QZI та JIT-запуск саме QZI
qz build src/qz/main.qz --no-incremental -i -o real_fetch.qzi
qz run --jit real_fetch.qzi
# Rust, без оптимізацій, статична libc
rustc -C opt-level=0 -C debuginfo=0 -C panic=abort -C target-feature=+crt-static src/rust/main.rs -o fetch-rust
# C, усі модулі, статично
gcc -O0 -static src/c/*.c -o fetch-c
# Zig 0.17.1, Debug, статично
zig build-exe -O Debug -lc -static -femit-bin=fetch-zig src/zig/main.zig
```

Файл `bench.py` містить команди для всіх реалізацій; `fix_qzi.py` збирає QZI, заміряє JIT-запуски та перевіряє 36 полів. Шляхи до qz, rustc, zig і стандартної бібліотеки у скрипті встановлені під поточний контейнер. Їх потрібно адаптувати для іншої машини.

**Обмеження порівняння:** кількість файлів сама по собі не робить проєкт еквівалентним повному Quazifetch; більшість провайдерів невеликі, а парсинг файлових даних винесено в спільний модуль. Rust `-O0` та Quazi default — це різні моделі оптимізації; результат не є загальним рейтингом швидкості мов. Вимірюється лише цей Linux fetch.
