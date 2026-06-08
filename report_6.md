# Отчет по лабораторной работе 

## Автоматизация пакетирования и CI/CD релизов

## Выполнил: Артеменко Арина ИУ8-22
---

## 1. Выполнение работы и локальная сборка

### 1.1 Клонирование репозитория и настройка окружения

Рабочее окружение было инициализировано переменными среды, после чего проект из предыдущей лабораторной работы был склонирован в новую директорию:

```bash
export GITHUB_USERNAME=gest475
export GITHUB_EMAIL=maryuye@mail.ru

mkdir -p ~/${GITHUB_USERNAME}/workspace
cd ~/${GITHUB_USERNAME}/workspace
git clone https://github.com/gest475/lab05 projects/lab06
cd projects/lab06
git remote remove origin
git remote add origin https://github.com/gest475/lab06
```

### 1.2 Версионирование проекта
С помощью утилиты sed в файл CMakeLists.txt были внедрены переменные для конфигурации макросов версий пакета:

```bash
sed -i '/project(banking)/a\set(PRINT_VERSION_STRING "v\${PRINT_VERSION}")' CMakeLists.txt
sed -i '/project(banking)/a\set(PRINT_VERSION\  \${PRINT_VERSION_MAJOR}.\${PRINT_VERSION_MINOR}.\${PRINT_VERSION_PATCH}.\${PRINT_VERSION_TWEAK})' CMakeLists.txt
sed -i '/project(banking)/a\set(PRINT_VERSION_TWEAK 0)' CMakeLists.txt
sed -i '/project(banking)/a\set(PRINT_VERSION_PATCH 0)' CMakeLists.txt
sed -i '/project(banking)/a\set(PRINT_VERSION_MINOR 1)' CMakeLists.txt
sed -i '/project(banking)/a\set(PRINT_VERSION_MAJOR 0)' CMakeLists.txt
```

Подготовка сопутствующих файлов метаданных:

```bash
echo "Static C++ library for printing" > DESCRIPTION
export DATE="`LANG=en_US date +'%a %b %d %Y'`"

cat > ChangeLog.md <<EOF
* ${DATE} gest475 <maryuye@mail.ru> 0.1.0.0
- Initial RPM release
EOF

echo "MIT License" > LICENSE
```

### 1.3 Конфигурация и запуск CPack
Разработан конфигурационный файл CPackConfig.cmake для генерации архивов .tar.gz, а также системных пакетов DEB и RPM:

```bash
cat > CPackConfig.cmake <<EOF
include(InstallRequiredSystemLibraries)
set(CPACK_PACKAGE_CONTACT maryuye@mail.ru)
set(CPACK_PACKAGE_VERSION_MAJOR \${PRINT_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR \${PRINT_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH \${PRINT_VERSION_PATCH})
set(CPACK_PACKAGE_VERSION_TWEAK \${PRINT_VERSION_TWEAK})
set(CPACK_PACKAGE_VERSION \${PRINT_VERSION})
set(CPACK_PACKAGE_DESCRIPTION_FILE \${CMAKE_CURRENT_SOURCE_DIR}/DESCRIPTION)
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "static C++ library for printing")
set(CPACK_RESOURCE_FILE_LICENSE \${CMAKE_CURRENT_SOURCE_DIR}/LICENSE)
set(CPACK_RESOURCE_FILE_README \${CMAKE_CURRENT_SOURCE_DIR}/README.md)
set(CPACK_RPM_PACKAGE_NAME "print-devel")
set(CPACK_RPM_PACKAGE_LICENSE "MIT")
set(CPACK_RPM_PACKAGE_GROUP "print")
set(CPACK_RPM_CHANGELOG_FILE \${CMAKE_CURRENT_SOURCE_DIR}/ChangeLog.md)
set(CPACK_RPM_PACKAGE_RELEASE 1)
set(CPACK_DEBIAN_PACKAGE_NAME "libprint-dev")
set(CPACK_DEBIAN_PACKAGE_PREDEPENDS "cmake >= 3.0")
set(CPACK_DEBIAN_PACKAGE_RELEASE 1)
include(CPack)
EOF
```

Подключение конфигурации в CMakeLists.txt:

```bash
echo "include(CPackConfig.cmake)" >> CMakeLists.txt
```

### 1.4 Сборка и локальное пакетирование
```bash
cmake -H. -B_build
cmake --build _build
cd _build
cpack -G "TGZ"
cd ..
```

Вывод терминала при успешной сборке:

```
-- Configuring done (0.1s)
-- Generating done (0.0s)
-- Build files have been written to: /home/maryu/gest475/workspace/projects/lab06/_build
[ 33%] Building CXX object banking/CMakeFiles/banking.dir/Account.cpp.o
[ 66%] Building CXX object banking/CMakeFiles/banking.dir/Transaction.cpp.o
[100%] Linking CXX static library libbanking.a
[100%] Built target banking

CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: banking
CPack: - Install project: banking []
CPack: Create package
CPack: - package: /home/maryu/gest475/workspace/projects/lab06/_build/banking-0.1.0.0-Linux.tar.gz generated.
```
##Альтернативный метод с использованием цели package:

```bash
cmake -H. -B_build -DCPACK_GENERATOR="TGZ"
cmake --build _build --target package

mkdir artifacts
mv _build/*.tar.gz artifacts
ls -l artifacts
```

##2. Автоматизация релизов через GitHub Actions (Домашнее задание)

###2.1 Конфигурация сценария .github/workflows/release.yml

```
name: Release

on:
  push:
    tags:
      - 'v*'

jobs:
  build_packages:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v4

    - name: Install dependencies
      run: |
        sudo apt-get update
        sudo apt-get install -y rpm cmake build-essential

    - name: Configure CMake
      run: cmake -H. -B_build -DCPACK_GENERATOR="TGZ;DEB;RPM"

    - name: Build Package
      run: cmake --build _build --target package

    - name: Create GitHub Release
      uses: softprops/action-gh-release@v2
      with:
        files: |
          _build/*.tar.gz
          _build/*.deb
          _build/*.rpm
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```
## 2.2 Публикация релиза

```bash
git add .github/workflows/release.yml LICENSE
git commit -m "fix: clean release workflow and add license"
git tag v0.1.0.4
git push origin main --tags
```
**Вывод терминала:**

```
[main ef680d7] fix: clean release workflow and add license
 2 files changed, 33 insertions(+)
 create mode 100644 .github/workflows/release.yml
 create mode 100644 LICENSE
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/gest475/lab06
 * [new tag]         v0.1.0.4 -> v0.1.0.4
```
