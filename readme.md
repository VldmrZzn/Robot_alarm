# Архитектура системы робота-звонка

### Структурная схема подключения устройств

| Компонент | Тип | Интерфейс подключения | ROS пакет/драйвер | Назначение |
|-----------|-----|----------------------|-------------------|------------|
| **Датчики** | | | | |
| LIDAR Velodyne VLP-16 | Лидар | 1Gbit Ethernet | `velodyne_driver` | Обнаружение людей в зоне |
| Курсовертикаль МК1 | Инерциальный блок | SPI | `xsens_driver` | Определение ориентации |
| **ROS 2 Ноды** | | | | |
|  lidar_node | Нода | - | `robot_alarm_system` | Обработка данных лидара |
|  timer_node | Нода | - | `robot_alarm_system` | Слежение за временем |
|  alarm_node | Нода | - | `robot_alarm_system` | Управление оповещением |
| **Исполнительные устройства** | | | | |
| Динамик | Аудио | Audio Jack/HDMI | `sound_play`/ALSA | Воспроизведение сигнала |
| Двигатели (ODrive) | Привод | CAN bus | `ros2_control` | Перемещение робота |
| **Рабочая станция** | Ноутбук/ПК | SSH/ROS_DOMAIN_ID | - | Удаленное управление |
| Rviz2 | Визуализация | - | `rviz2` | 3D визуализация данных |
| RQt | Инструменты | - | `rqt` | Графический интерфейс |
| Teleop | Управление | - | `teleop_twist_keyboard` | Ручное управление |

### Зависимости пакетов ROS

| Пакет ROS | Назначение | Установка |
|-----------|------------|-----------|
| `velodyne` | Драйвер лидара | `sudo apt install ros-humble-velodyne` |
| `ros2_control` | Управление приводами | `sudo apt install ros-humble-ros2-control` |
| `audio_common` | Работа со звуком | `sudo apt install ros-humble-audio-common` |
| `navigation2` | Навигация (опционально) | `sudo apt install ros-humble-navigation2` |
| `teleop_twist_keyboard` | Ручное управление | `sudo apt install ros-humble-teleop-twist-keyboard` |

#Создание docker 
* Предварительно создав образ в docker hub, загружаем его
```
docker pull vldmrzzn/ros2:tagname
```
И сразу создаем для удобства docker-compose, где есть автоматически подгруженная папка и x11 для удобства
```
version: '3.8'

services:
  ros2-gui:
    image: vldmrzzn/ros2:lasted
    container_name: ros2_x11_container
    
    # Переменные окружения для X11
    environment:
      - DISPLAY=host.docker.internal:0  # Проброс дисплея
      - QT_X11_NO_MITSHM=1              # Для Qt приложений
      - LIBGL_ALWAYS_INDIRECT=1         # Для OpenGL
    
    # (папки)
    volumes:
      - /tmp/.X11-unix:/tmp/.X11-unix:rw  # X11 сокет
      - /home/user/ddoker:/mnt/docker:rw  # папка с проектами
      - /home/user/ddoker:/root:rw
    
    # Сеть
    network_mode: host  # Использует сеть хоста
    
    # Права
    privileged: true    # Дает больше прав для графики
    
    # Интерактивный режим
    stdin_open: true    # -i
    tty: true          # -t
       
    
    # Перезапуск (опционально)
    restart: unless-stopped
```

#Установка ssh для иашыны не в docker 
```
#!/bin/bash

set -e
sudo apt update
sudo apt install -y openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh
echo "SSH сервер запущен и настроен."
```

