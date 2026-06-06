# Отчет по лабораторной работе №3
## ВЫполнил: Артеменко Арина ИУ8-22

## Репозиторий: https://github.com/gest475/lab003

## Цель работы
Освоить систему сборки CMake, научиться описывать статические библиотеки и приложения.

## Выполненные команды и их вывод

`cd ~/workspace`


`mkdir -p projects/lab03 && cd projects/lab03`


`git init`
*Вывод:*
[master (root-commit) 2ff4ce9] Initial commit: add README.md

1 file changed, 1 insertion(+)

create mode 100644 README.md

`mkdir -p sources include examples`


`git add .`


`git commit -m "added sources"`
*Вывод:*
[master 6e81afc] added sources

4 files changed, 33 insertions(+)


`cat > formatter_lib/CMakeLists.txt <<EOF`

`cat > formatter_ex_lib/CMakeLists.txt <<EOF`


`cat > hello_world_application/CMakeLists.txt <<EOF`


`cat > solver_lib/CMakeLists.txt <<EOF`


`cat > solver_application/CMakeLists.txt <<EOF`


`cat > CMakeLists.txt <<EOF`


`git add .`


`git commit -m "add CMakeLists.txt for all modules"`

*Вывод:*

[master 7e76299] add CMakeLists.txt for all modules

6 files changed, 52 insertions(+)

create mode 100644 CMakeLists.txt

create mode 100644 formatter_ex_lib/CMakeLists.txt

create mode 100644 formatter_lib/CMakeLists.txt

create mode 100644 hello_world_application/CMakeLists.txt

create mode 100644 solver_application/CMakeLists.txt

create mode 100644 solver_lib/CMakeLists.txt


`git remote add origin https://github.com/gest475/lab003.git`


`git push -u origin main`
*Вывод:*

Enumerating objects: 25, done.

Counting objects: 100% (25/25), done.

Delta compression using up to 8 threads

Compressing objects: 100% (16/16), done.

Writing objects: 100% (25/25), 2.40 KiB | 1.20 MiB/s, done.

Total 25 (delta 4), reused 0 (delta 0), pack-reused 0

To https://github.com/gest475/lab003.git


[new branch] main -> main

branch 'main' set up to track 'origin/main'.


`git log --oneline`
*Вывод:*

7e76299 (HEAD -> main, origin/main) add CMakeLists.txt for all modules

6e81afc added sources

2ff4ce9 Initial commit: add README.md
