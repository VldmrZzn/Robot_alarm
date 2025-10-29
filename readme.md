## Архитектура системы робота-звонка

### Структурная схема подключения устройств

| Компонент | Тип | Интерфейс подключения | ROS пакет/драйвер | Назначение |
|-----------|-----|----------------------|-------------------|------------|
| **Вычислитель** | Raspberry Pi 4 | - | - | Основной контроллер системы |
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
| STT/TTS модуль | Голосовой | USB/UART | (опционально) | Речевое взаимодействие |
| **Сетевое взаимодействие** | | | | |
|  WiFi/Ethernet | Сеть | Ethernet/WiFi | - | Связь с рабочей станцией |
| **Рабочая станция** | Ноутбук/ПК | SSH/ROS_DOMAIN_ID | - | Удаленное управление |
| ↳ Rviz2 | Визуализация | - | `rviz2` | 3D визуализация данных |
| ↳ RQt | Инструменты | - | `rqt` | Графический интерфейс |
| ↳ Teleop | Управление | - | `teleop_twist_keyboard` | Ручное управление |

### Зависимости пакетов ROS

| Пакет ROS | Назначение | Установка |
|-----------|------------|-----------|
| `velodyne` | Драйвер лидара | `sudo apt install ros-humble-velodyne` |
| `ros2_control` | Управление приводами | `sudo apt install ros-humble-ros2-control` |
| `audio_common` | Работа со звуком | `sudo apt install ros-humble-audio-common` |
| `navigation2` | Навигация (опционально) | `sudo apt install ros-humble-navigation2` |
| `teleop_twist_keyboard` | Ручное управление | `sudo apt install ros-humble-teleop-twist-keyboard` |

Эта табличная структура будет хорошо смотреться в README.md и четко покажет архитектуру вашей системы!