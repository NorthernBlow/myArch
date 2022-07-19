# Команды ARCH Linux

systemctl disable удаляет из автозагрузки

systemd-analyze выводит скорость загрузки системы

systemd-analyze blame выводит скорость загрузки служб

ctrl + e в конец строки в терминале

ctrl + a - в начало строки

journalctl   показывает журнал работы системы

journalctl -b -1   за предыдущую сессию 

journactl -b -2  за две предыдущих сессии

makepkg -sir  скомпилировать файл из репозитория, установить зависимости и удалить их после установки

lspci -k  выводит список установленных драйверов переферии

pacman -Qdtq  список независимостей

## Установка драйверов AMD в ARCH Linux

В ядре два свободных драйвера: radeon для старых видеокарт AMD, и  amdgpu для новых карт AMD. Ядро, учитывая модель видеокарты,  автоматически подгружает и использует нужный драйвер.

Mesa, используя ядерный драйвер, релизует в юзерспейсе OpenGL (radeonsi) и Vulkan (radv).

Помимо реализации Vulkan, встроенной в Mesa, есть еще сторонняя свободная реализация от AMD - называется [AMDVLK](https://github.com/GPUOpen-Drivers/AMDVLK). Могу сказать, что производительность AMDVLK и RADV примерно идентична,  так что какого-то особо смысла использовать AMDVLK вместо RADV нет. Но  при желании можно установить AMDVLK рядом с Mesa и использовать при  необходимости.

Проприетарный драйвер AMDGPU-PRO содержит собственную реализацию  OpenGL и Vulkan (которая, думаю, примерно идентична AMDVLK). Также он  содержит OpenCL. AMDGPU-PRO так же, как и Mesa, использует ядерный  драйвер.

Как начать пользоваться amdgpu, имея достаточно свежую видеокарту?  Достаточно установить свежее ядро, свежие прошивки (linux-firmware) и  свежую Mesa и все будет работать. В дистрибутивах это, как правило, уже  установлено из коробки.

> 4 реализации Vulkan! Долбануться можно, 4 реализации Vulkan… А сколько реализаций OpenGL и OpenCL?

По факту, реализации Vulkan всего две: RADV и AMDVLK. RADV+ACO - это  тот же RADV, только в качестве компилятора шейдеров используется ACO  (новый, более быстрый бэкенд) вместо LLVM. Реализации OpenGL тоже две:  RadeonSI и проприетарная реализация из AMDGPU-PRO. В Mesa есть еще  реализации OpenGL для старых карт (r300, r600…), но их можно не  учитывать, если речь не о древних видеокартах.

##### Путь для конфигурации драйверов /etc/modprobe.d/

создать файлы конфигурации: 

```
/etc/modprobe.d/amdgpu.conf
options amdgpu si_support=1
options amdgpu cik_support=1
```

```
/etc/modprobe.d/radeon.conf
options radeon si_support=0
options radeon cik_support=0
```

regenerate the initramfs: mkinitcpio -p linux

To (re-)generate all existing presets, use the `-P`/`--allpresets` switch. This is typically used to regenerate all the initramfs images after a change of the global [#Configuration](https://wiki.archlinux.org/title/Mkinitcpio#Configuration): mkinitcpio -P

sudo pacman -S vulkan-tools

проверить вулкан - vulkaninfo

sudo pacman -S vulkan-radeon lib32-vulkan-radeon

```
glxinfo | grep -i opengl
clinfo | grep -i opencl
```

проверить, работает ли OpenGL и OpenCL c Mesa

