ROS Continuous Integration Tools
================================

This installs a minial ROS shadow-fixed system on Ubuntu Linux.

### Travis Examples

#### Isolated Catkin Build

```yml
language:
  - cpp
  - python
python:
  - "2.7"
compiler:
  - gcc
before_install:
  # Define some config vars
  - export ROS_DISTRO=hydro
  - export CI_SOURCE_PATH=$(pwd)
  # Bootstrap a minimal ROS installation
  - git clone https://github.com/jhu-lcsr/ros_ci_tools /tmp/ros_ci_tools && export PATH=/tmp/ros_ci_tools:$PATH
  - ros_ci_bootstrap
  # Create isolated workspace based on the ros distro
  - source /opt/ros/$ROS_DISTRO/setup.bash
  - mkdir -p ~/ws_isolated/src
  - cd ~/ws_isolated
  - ln -s $CI_SOURCE_PATH src/
  # Install dependencies for source repos
  - rosdep install --from-paths src --ignore-src --rosdistro $ROS_DISTRO -y > /dev/null

install:
  # Build in an isolated catkin workspace
  - export EXTRA_CMAKE_ARGS="-DENABLE_TESTS=ON -DENABLE_CORBA=ON -DCORBA_IMPLEMENTATION=OMNIORB"
  - catkin_make_isolated --install -j1 --cmake-args $EXTRA_CMAKE_ARGS

script:
  # Run tests
  - pushd build_isolated/rtt && make check && popd
```
