# Инструкция

Чистка и анализ изображений подразумевают использование как специального софта, разработанного сотрудниками СРГ (в частности, python-скрипты), так и пакеты програмного обеспечения более глобального уровня ("CASA"). Необходимо понимать, что это накладывает ограничения на используемые операционные системы (unix-подобные), в частности мы можем использовать ОС с ядром Linux (предпочтительно - из ветки RedHat) и macOS. Данная инструкция рассматривает установку необходимого ПО на Linux дистрибутив Fedora.

## 1. Подготовка

_Все описанные манипуляции производились на OC Windows_
- Переходим на [официальный сайт](https://getfedora.org) проекта Fedora. Выбираем вариант Fedora Workstation, далее - скачиваем x86_64 Live образ ISO
- Находим флешку, которую можно будет отформатировать _(все данные будут удалены!)_
- Скачиваем программу Rufus [c официального сайта проекта](https://rufus.ie/ru/). Можно скачать portable-version (не требует установки, нужно лишь запустить исполняемый .exe файл)
- Решаем вопрос с местом установки - куда мы поставим новую систему. Для этого нужно создать новый логический диск (ПКМ по иконке Пуск -> Управление дисками -> сжать том -> указать необходимые размеры). 
> При установке на другой физический диск могут возникнуть проблемы с настройкой grub (либо придется каждый раз менять порядок загрузки OC через BIOS), решение этих проблем в данной инструкции описано не будет
- Запускаем Rufus, в разделе "устройство" выбираем наш USB-накопитель, в разделе "метод загрузки" нажимаем на кнопку "выбрать" и указываем путь к скачанному ISO образу Fedora. Запускаем процесс создания загрузочной флешки.
- По окончании предыдущего этапа перезагружаемся в BIOS, меняем порядок загрузки, указывая USB-флешку приорететной, и сохраняя настройки перезапускаемся на нее.

## 2. Установка Fedora

- Выбираем в вариантах загрузки федору
- Выбираем в доп меню установить на хард драйв
- Выбираем язык, настраиваем дату и время
- Заходим в раздел "Место установки", далее выбираем необходимый диск и в разделе "Конфигурация устройств хранения" ставим чек-поинт на вариант "Дополнительно", после чего нажимаем на кнопку "Готово" слева вверху. После данного действия откроется среда BLIVET разбиения диска на разделы
- В открывшемся меню ставим выбор на нужный логический раздел диска, после чего с помощью ПКМ форматируем в новый формат ext4 и ставим точку монтирования "/", опять нажимаем "Готово", соглашаясь с диалоговым окном о принятии изменений.
- В случае возниовения ошибки ля ля нужно прописать /boot/efi/ ля ля
- Произвдим установку системы и перезагружаем компьютер, попутно вынимая флешку. 
- Из появившегося окна grub выбраем запуск Linux Fedora
- Производим первичную настройку системы: подключение к Wi-Fi, отключение аналитики (по желанию), включение стронних репозиториев, ввод имени пользователя и пароля.
> Чистая система после установки обновлений весит порядка 7 - 8 ГБ.

> Для установки VSCode последовательно выполнить:
> ``` bash
> sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
> sudo sh -c 'echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/vscode.repo'
> sudo dnf check-update
> sudo dnf -y install code
> ```

> Для установки Opera последовательно выполнить:
> ``` bash
> sudo dnf config-manager --add-repo https://rpm.opera.com/rpm
> sudo rpm --import https://rpm.opera.com/rpmrepo.key
> sudo dnf upgrade --refresh
> sudo dnf install opera-stable
> ```


## 3. Установка нужной версии Python

К сожалению, CASA не поддерживает версии Python выше 3.8, наиболее удобный вариант - запускать все необходимое в виртуальном окружении conda.
Выполняем в терминале:
``` bash
$ conda
```
а затем соглашаемся с предложением установить недостающий пакет и создаем виртуальное окружение с помощью команды:
``` bash
$ conda create --name casa python=3.8 pip spyder
```
После этого запускаем 
``` bash
$ conda init bash
```
и перезагружаем окно консоли. 
Проверяем виртуальное окружение с помощью 
``` bash
$ conda activate casa
```
a также работу spyder с помощью
``` bash
$ spyder
```
Консоль должна изменяться с ```(base)[username@fedora ~]$``` на ```(casa)[username@fedora ~]$``` при активации окружения.
Для выхода из spyder используем ```ctrl + z```, для выхода из виртуального окружения используем 
``` bash 
$ conda deactivate
```

## 4. Установка (сборка) casacore из исходников

Скачиваем исходный код проекта:
``` bash
git clone https://github.com/casacore/casacore
```
Устанавливаем зависимости:
``` bash
sudo yum install cmake cmake-gui gcc-gfortran gcc-c++ flex bison blas blas-devel  lapack lapack-devel cfitsio cfitsio-devel wcslib wcslib-devel ncurses ncurses-devel readline readline-devel python-devel boost boost-devel fftw fftw-devel hdf5 hdf5-devel numpy
```
Затем во избежание получения ошибки ```FindGSL.cmake: gsl-config not found``` последовательно выполняем:
``` bash
wget ftp://ftp.gnu.org/gnu/gsl/gsl-2.7.tar.gz
tar -zxvf gsl-2.7.tar.gz
cd gsl-2.7
mkdir /home/username/gsl
./configure --prefix=/home/username/gsl
make
make check
make install
gsl-config
```
Открываем папку с исходным кодом:
``` bash
cd casacore
```
Производим сборку (процесс достаточно длительный, от часа и дольше):
``` bash
mkdir build
cd build
cmake ..
make 
sudo make install
```

