# This  repositories is a version of HKUST-Aerial-Robotics/VINS-Mono without ROS mechanism. The project can be passed on the euroc dataset so far. [EuRoc](http://projects.asl.ethz.ch/datasets/doku.php?id=kmavvisualinertialdatasets) ASL data format, this version can run both on 32bit OS and 64 bit OS platform.

# thanks to the reference program： 
# [HKUST-Aerial-Robotics/VINS-Mono](https://github.com/HKUST-Aerial-Robotics/VINS-Mono)
# What need to be installed
```
  1. Ceres Solver
     sudo apt-get install cmake
     sudo apt-get install libgoogle-glog-dev
     sudo apt-get install libatlas-base-dev
     sudo apt-get install libsuitesparse-dev
     mkdir ceres-bin
     cd ceres-bin
     wget http://ceres-solver.org/ceres-solver-1.14.0.tar.gz
     tar zxf ceres-solver-1.14.0.tar.gz
     mkdir ceres-bin
     cd ceres-bin/
     cmake ../ceres-solver-1.14.0
     make -j3
     make test
     sudo make install

  2. VTK
     # vins_estimator requires the library libopencv_viz.so, which Opencv compiler will generate only if VTK is installed. 
     sudo apt-get install libvtk5-qt4-dev

  3. GTK (needed for opencv)
     sudo apt-get install libgtk2.0
     sudo apt-get install libgtk3.0
     Remark: when doing opencv make, enable flags WITH_GTK and WITH_QT.

  3. Opencv 3.1
     mkdir opencv345
     cd opencv345
     git clone -b 3.4.5 https://github.com/opencv/opencv
     cd opencv ; git checkout 3.1.0 ; cd ..
     git clone -b 3.4.5 https://github.com/opencv/opencv_contrib
     cd opencv_contrib ; git checkout 3.1.0 ; cd ..
     # Manually fix the sign() issue which causes compile-time error (persist in version 3.1.0):     
         Refer: https://github.com/opencv/opencv_contrib/commit/395db9e7211d58473beafa6a9643d3cd49ad4eb4
         Remove the following line from file modules/tracking/include/opencv2/tracking/onlineMIL.hpp
             #define  sign(s)  ((s > 0 ) ? 1 : ((s<0) ? -1 : 0))
         Add the above line to file modules/tracking/src/onlineMIL.cpp
     # Manually fix the HDF5 library issue:
         Refer: https://github.com/opencv/opencv/issues/6050
         Add the following lines to the bottom of the file modules/python/common.cmake
             find_package(HDF5)
             include_directories(${HDF5_INCLUDE_DIRS})
     mkdir build
     cd build/
     cmake -DOPENCV_ENABLE_NONFREE:BOOL=ON -DOPENCV_EXTRA_MODULES_PATH=../opencv_contrib/modules -DWITH_GTK=ON -DWITH_QT=ON ../opencv
     # Ensure that QT, GTK, and VTK in the GUI section are all enabled before continuing.
     make -j8
     sudo make install
     sudo ldconfig
  
  4. Eigen 3.2.0
  
  5. boost
     sudo apt-get install libboost*
     
  6. Pangolin
  
To build with tensorflow environment:
  
  1. Build environment following Lilian's flow.
     References: https://wiki.mediatek.inc/pages/viewpage.action?pageId=603491233
     
  2. When building tensorflow (before running ./configure), please check if bazel version is 0.26.1.
     Command to check: bazel version
     If version is not, please update bazel.
     sudo rm -rf /usr/bin/bazel /etc/bazelrc /usr/lib/bazel /usr/local/bin/bazel /usr/local/lib/bazel ~/bin/bazel
     cd ~/volumes/bazel
     wget https://github.com/bazelbuild/bazel/releases/download/0.26.1/bazel-0.26.1-installer-linux-x86_64.sh
     chmod +x bazel-0.26.1-installer-linux-x86_64.sh
     ./bazel-0.26.1-installer-linux-x86_64.sh --user
     export PATH="$PATH:$HOME/bin"
     
     

```

# How to build:
```
  1. mkdir VINS_Workspace
  2. cd VINS_Workspace
  3. git clone git@github.com:heguixiang/Remove_ROS_VINS.git
  4. mv Remove_ROS_VINS src
  5. ./generate.sh
``` 
# How to run:
```
  1. cd VINS_Workspace
  2. mkdir -p data/image/MH_01_easy
  3. mkdir -p data/imu/
  4. cd VINS_Workspace/data & git clone git@github.com:heguixiang/EuRoc-Timestamps.git
  5. download the EuRoc dataset(e.g. Machine Hall 01)
  6. cp the MH_01_easy/mav0/cam0/data to data/image/MH_01_easy
  7. cp the MH_01_easy/mav0/imu0/data.csv to data/imu/
  8. cd VINS_Workspace
  9. ./src/vins_estimator/build/vins_estimator ./src/config/euroc/euroc_config.yaml ./data/image/MH_01_easy/data/ ./data/EuRoc-Timestamps/MH01.txt ./data/imu/data.csv
```  
