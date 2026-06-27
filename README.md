# ROS2 ORB SLAM3 V1
Merupakan repository ORB SLAM3 yang digunakan pada ROS2 Jazzy.

## Prerequisites
### Eigen 3
```bash
sudo apt install libeigen3-dev
```

### Pangolin
```bash
cd ~/Documents
git clone https://github.com/stevenlovegrove/Pangolin
cd Pangolin
./scripts/install_prerequisites.sh --dry-run recommended # Check what recommended softwares needs to be installed
./scripts/install_prerequisites.sh recommended # Install recommended dependencies
cmake -B build
cmake --build build -j4
sudo cmake --install build
```

### OpenCV
OpenCV yang kompatibel utk digunakan adalah versi di atas 4.2, cek terlebih dahulu versi OpenCV yang terinstall
```bash
python3 -c "import cv2; print(cv2.__version__)" 
```

## Instalasi
```bash
cd ~
mkdir -p ~/ros2_test/src
cd ~/ros2_test/src
git clone https://github.com/ROBOTICS-STEI-ITB/ros2_orb3-V1.git
cd .. # make sure you are in ~/ros2_ws root directory
rosdep install -r --from-paths src --ignore-src -y --rosdistro humble
source /opt/ros/humble/setup.bash
colcon build --symlink-install
```

## Cara Mengoperasikan
### Stereo Example
Pada contoh ini, digunakan kamera Intel Realsense D455. Terlebih dahulu buka terminal dan masukan command seperti di bawah ini,
command ini digunakan untuk mengaktifkan ORB SLAM3 dan memiliki waktu setup yang lebih lambat dibanding kamera.
Jika ingin menggunakan kamera berjenis lain, sesuaikan nama dan path dari file .yaml yang digunakan
```bash
cd ~/Dev/ros2_test
source install/setup.bash

ros2 run ros2_orb_slam3 stereo_node_cpp \
src/ros2_orb_slam3/orb_slam3/Vocabulary/ORBvoc.txt \
src/ros2_orb_slam3/orb_slam3/config/Stereo/RealSense_D455.yaml
```

Kemudian buka Terminal baru dan jalankan command di bawah ini, command digunakan untuk mengaktifkan kamera. Apabila ingin menggunakan
ukuran atau FPS yang berbeda, command bisa disesuaikan dengan kebutuhan namun perubahan yang sama harus dilakukan pada file konfigurasi
kamera .yaml. Hal ini dilakukan agar sistem ORB SLAM3 dan kamera menjalankan konfigurasi yang sama dan selaras
```bash
cd ~/Dev/ros2_test
source install/setup.bash

ros2 launch realsense2_camera rs_launch.py \
  enable_infra1:=true \
  enable_infra2:=true \
  enable_depth:=false \
  enable_color:=false \
  enable_sync:=true \
  infra_width:=848 \
  infra_height:=480 \
  infra_fps:=30 
```

### EuRoC
Terdapat perbedaan nama variabel pada file konfigurasi EuRoC, oleh karena itu untuk dapat menjalankankannya diperlukan command seperti
di bawah ini
```bash
cd ~/Dev/ros2_test
source install/setup.bash

ros2 run ros2_orb_slam3 stereo_node_cpp \
src/ros2_orb_slam3/orb_slam3/Vocabulary/ORBvoc.txt \
src/ros2_orb_slam3/orb_slam3/config/Stereo/EuRoC.yaml \
--ros-args -p use_sim_time:=true \
-r /camera/camera/infra1/image_rect_raw:=/cam0/image_raw \
-r /camera/camera/infra2/image_rect_raw:=/cam1/image_raw
```
Pada terminal lain, jalankan command ini untuk menjalankan simulasi dari datasetnya. Jangan lupa untuk menyesuaikan
path dan nama dataset EuRoC yang ingin digunakan
```bash
ros2 bag play [DATASET_PATH] --clock
```

### Komunikasi (Optional)
Command ini hanya dijalankan apabila pengguna ingin mengamati hasil pembacaan ORB SLAM3 beserta trajektori alat.Terdapat 3 file yang 
harus dijalankan untuk membentuk sistem komunikasi ini, yaitu server, pose_sender, dan index. Ketiganya bisa dijalankan pada 3 device
yang berbeda maupun dijalankan pada device yang sama.
Pada device yang dijadikan sebagai server (backend), jalankan command berikut pada cmd tempat file tersimpan
```bash
python server.py
```
Command di atas dijalankan pada laptop dengan OS Windows, namun jika menggunakan ubuntu bisa juga dengan command sebagai berikut
```bash
python3 ~/[FILE_PATH]/server.py 
```

Jalankan command di bawah pada device yang diintegrasikan dengan ORB SLAM3
```bash
python3 ~/[FILE_PATH]/pose_ws_sender.py 
```
Apabila menggunakan virtual environment (venv) bisa juga dengan menjalankan command berikut
```bash
python3 -m venv .venv
source .venv/bin/activate
python ~/[FILE_PATH]/pose_ws_sender.py
```
Index merupakan kode frontend berupa website HTML, untuk menjalankannya hanya perlu membuka filenya pada browser saja.





