# Инструкция

Чистка и анализ изображений подразумевают использование как специального софта, разработанного сотрудниками СРГ (в частности, python-скрипты), так и другие пакеты програмного обеспечения ("CASA"). Необходимо понимать, что это накладывает ограничения на используемые операционные системы (unix-подобные), в частности мы можем использовать ОС с ядром Linux (предпочтительно - из ветки RedHat) и macOS. Данная инструкция рассматривает установку необходимого ПО на Linux дистрибутив Fedora.

## 1. Подготовка

_Все описанные манипуляции производились на OC Windows_

- Переходим на [официальный сайт](https://getfedora.org) проекта Fedora. Выбираем вариант Fedora Workstation, далее - скачиваем x86_64 Live образ ISO
- Находим флешку, которую можно будет отформатировать _(все данные будут удалены!)_
- Скачиваем программу Rufus [c официального сайта проекта](https://rufus.ie/ru/). Можно скачать portable-version (не требует установки, нужно лишь запустить исполняемый .exe файл)
- Решаем вопрос с местом установки - куда мы поставим новую систему. Для этого нужно создать новый логический диск (ПКМ по иконке Пуск -> Управление дисками -> сжать том -> указать необходимые размеры).

> При установке на другой физический диск могут возникнуть проблемы с настройкой grub (либо придется каждый раз менять порядок загрузки OC через BIOS), решение этих проблем в данной инструкции описано не будет

- Запускаем Rufus, в разделе "устройство" выбираем наш USB-накопитель, в разделе "метод загрузки" нажимаем на кнопку "выбрать" и указываем путь к скачанному ISO образу Fedora. Запускаем процесс создания загрузочной флешки.
- По окончании предыдущего этапа перезагружаемся в BIOS, меняем порядок загрузки, указывая USB-флешку приорететной, и сохраняя настройки перезапускаемся на нее.

## 2. Установка Fedora параллельно с Windows

- Выбираем в вариантах загрузки Grub пункт Fedora
- Выбираем в допоолнительном меню пункт "установить на Hard Drive"
- Выбираем язык, настраиваем дату и время
- Заходим в раздел "Место установки", далее выбираем необходимый диск и в разделе "Конфигурация устройств хранения" ставим чек-поинт на вариант "Дополнительно", после чего нажимаем на кнопку "Готово" слева вверху. После данного действия откроется среда BLIVET разбиения диска на разделы
- В открывшемся меню ставим выбор на нужный логический раздел диска, после чего с помощью ПКМ форматируем в новый формат ext4 и ставим точку монтирования "/", опять нажимаем "Готово", соглашаясь с диалоговым окном о принятии изменений.
- Далее нужно выбрать (скорее всего, первый, размером в ~100 МБ) загрузочный раздел диска и прописать через ПКМ ему точку монтирования ```/boot/efi```

- Произвoдим установку системы и перезагружаем компьютер, попутно вынимая флешку

> Чистая система после установки обновлений весит порядка 8 ГБ.

- Из появившегося окна grub выбраем запуск Linux Fedora
- Производим первичную настройку системы: подключение к Wi-Fi, отключение аналитики (по желанию), включение стронних репозиториев, ввод имени пользователя и пароля.

> Если вы пользуетесь теми же приложениями, что и автор инструкции, можете воспользоваться следующими командами
>
>Для установки VSCode последовательно выполнить:
>
> ``` bash
> sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
> sudo sh -c 'echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/vscode.repo'
> sudo dnf check-update
> sudo dnf -y install code
>```
>
> Для установки Opera последовательно выполнить:
>
> ``` bash
> sudo dnf config-manager --add-repo https://rpm.opera.com/rpm
> sudo rpm --import https://rpm.opera.com/rpmrepo.key
> sudo dnf upgrade --refresh
> sudo dnf install opera-stable
> ```

<!-- ## 3. Easy install / Легкая установка

Скопируйте команду, которая объединяет все дальнейшие шаги, в консоль, запаситесь терпением и ждите скачивания и установки необходимых компонентов

``` bash
sudo dnf install conda -y && wget https://raw.githubusercontent.com/astronom-v-cube/howtostartSRHanalise/main/puppies.yml && sudo dnf install libnsl && sudo conda env create -f puppies.yml && conda activate casa && python3 -m casatools --update-user-data && rm puppies.yml && wget ftp://ftp.astron.nl/outgoing/Measures/WSRT_Measures.ztar --show-progress && mkdir WSRT_Measures/ && tar -xzvf WSRT_Measures.ztar -C WSRT_Measures/ && echo measures.observatory.directory: /home/$USER/WSRT_Measures/geodetic > .casarc && export CASARCFILES=“home/$USER/.casarc” && rm WSRT_Measures.ztar && sudo dnf install gcc gcc-c++ && pip install git+https://github.com/maria-globa/srhdata.git
``` -->

## 4. Установка нужной версии Python

Ранее CASA не поддерживала версии Python выше 3.8, и конфигурационный файл был создан для того случая. Сейчас совместимая версия поднялась до 3.10. 
Если вам необходимо исользовать Python 3.8, то файл конфигурации лежит в папке ```old_files```. Остальные шаги эквивалентны.

Наиболее удобный вариант - запускать все необходимое в виртуальном окружении conda. (Подробнее о возможностях conda хорошо написано [тут](https://python.ivan-shamaev.ru/guide-conda-environments-anaconda-python-data-science-platform/#Conda))

Можно использовать готовый конфигурационный файл ```conda_env_py310.yaml``` (находится в этом репозитории) следующим образом:

``` bash
wget https://raw.githubusercontent.com/astronom-v-cube/howtostartSRHanalise/main/conda_env_py310.yaml
```
затем
``` bash
conda env create -f conda_env_py310.yaml
```

Команда создаст виртуальное окружение с именем srh
В этом случае команды, расположенные между двумя горизонтальными чертами ниже, можно опустить.

***
Выполняем в терминале:

``` bash
sudo dnf install conda
```

и создаем виртуальное окружение с помощью команды:

``` bash
conda create --name srh python=3.10 pip spyder
```

После этого указываем

``` bash
conda init bash
```

и перезагружаем окно консоли. Устанавливаем библиотеки:

``` bash
pip install astropy matplotlib sunpy casatasks casatools numpy sympy python-casacore ephem scipy scikit-image 
```

***
Запускаем виртуальное окружение с помощью

``` bash
conda activate casa
```

Консоль должна изменяться с `(base)[username@fedora ~]$` на `(srh)[username@fedora ~]$` при активации окружения.
Для выхода из виртуального окружения используем

``` bash
conda deactivate
```

## 5. Установка (сборка) casacore из исходников

**Внимание! Этот шаг выполнять теперь не нужно!**

```casacore``` использовалась ранее для работы скриптов и программ, однако на данный момент она не нужна для их запуска. Тем не менее, этот пункт инструкции было решено оставить, так как найти эту информацию в интернете не так просто, а мало ли, что в жизни случится... :)

Скачиваем исходный код проекта:

``` bash
git clone https://github.com/casacore/casacore
```

Устанавливаем зависимости:

``` bash
sudo dnf install cmake cmake-gui gcc-gfortran gcc-c++ flex bison blas blas-devel lapack lapack-devel cfitsio cfitsio-devel wcslib wcslib-devel ncurses ncurses-devel readline readline-devel python-devel boost boost-devel fftw fftw-devel hdf5 hdf5-devel numpy
```

Затем во избежание получения ошибки ```FindGSL.cmake: gsl-config not found``` последовательно выполняем команды ниже (полный текст инструкции, в случае необходимости, можно найти [тут](https://coral.ise.lehigh.edu/jild13/2016/07/11/hello/)):

``` bash
wget ftp://ftp.gnu.org/gnu/gsl/gsl-2.7.tar.gz
tar -zxvf gsl-2.7.tar.gz
cd gsl-2.7
mkdir /home/$USER/gsl
./configure --prefix=/home/$USER/gsl
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
<!-- 
## 6. Установка дополнительных пакетов и библиотек

``` bash
sudo dnf install libnsl
npm install nsl
``` -->

## 7. Обновление данных обсерваторий

_Выполняем, находясь в виртуальном окружении conda srh:_

``` bash
pip install casaconfig && python -m casaconfig --update-all
```

## 8. Установка Python библиотеки srhdata

<!-- ``` bash
sudo dnf install gcc gcc-c++ 
``` -->

_Выполняем, находясь в виртуальном окружении conda srh:_

``` bash
pip install git+https://github.com/maria-globa/srhdata.git
```

## 9. Решение известных ошибок

**1. libGL error:**

``` bash
libGL error: failed to load driver: nouveau libGL
error: MESA-LOADER: failed to open swrast
error: failed to load driver: swrast
```

_Решение_: ```conda install -c conda-forge libstdcxx-ng```

Если указанное выше не помогло, следует найти расположение данной библиотеки в системе с помощью

``` bash
find / -name libstdc++.so.6 2>/dev/null
```

Затем любой из вариантов расположения (например, в ```/usr/lib64/```) добавить в файл ```.bashrs``` или соответсвующий файл другого интерпретатора терминала в папке ```home``` в следующем виде:

``` bash
export LD_PRELOAD=/usr/lib64/...path_to_file...
```

**2. Черный экран при запуске скриптов**

_Решение_: Spyder -> Tools -> Preferences -> IPython console -> Graphics -> Grapics backend -> Qt 5

**3. Gnome error**

``` bash
Warning: Ignoring XDG_SESSION_TYPE=wayland on Gnome. 
Use QT_QPA_PLATFORM=wayland to run on Wayland anyway
```

_Решение_:

1. Расскоментировать строку ```WaylandEnable=false``` в файле ```/etc/gdm3/custom.conf```
2. Добавить ```Add QT_QPA_PLATFORM=xcb``` в ```/etc/environment```

**4. В процессе работы появляется много кэша индексации файлов ```tracker3```**

Очистка кэша осуществляется так:
``` bash
tracker3 reset --filesystem
```

Рекомендуется прописать в начале скрипта отключение данной службы, а в конце - включение

**5. Любая другая ошибка**

Просьба создавать issues, если не знаете как решить, или pull requests если знаете решение 😃
