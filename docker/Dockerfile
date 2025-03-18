FROM osrf/ros:humble-desktop-jammy

#important, otherwise may cause poor priviledge
USER root

COPY sources.list /etc/apt/sources.list
RUN apt update

# install ros2 packages
RUN apt install -y --no-install-recommends \
    ros-humble-desktop-full

RUN mkdir -p /home/teleophost/.pip
COPY --chown=teleophost pip.conf /home/teleophost/.pip/pip.conf

RUN apt install -y wget zlib1g-dev libssl-dev libncurses5-dev libsqlite3-dev libreadline-dev libtk8.6 libgdm-dev libdb4o-cil-dev libpcap-dev

WORKDIR  /home/teleophost
RUN wget https://mirrors.huaweicloud.com/python/3.8.19/Python-3.8.19.tar.xz && tar -xvf Python-3.8.19.tar.xz
WORKDIR  /home/teleophost/Python-3.8.19

RUN ./configure --prefix=/usr/local && make && make install

WORKDIR /usr/local/bin/
RUN ln -s pip3 pip



RUN rm /usr/lib/x86_64-linux-gnu/libEGL_mesa.so.0 /usr/lib/x86_64-linux-gnu/libEGL_mesa.so.0.0.0 /usr/share/glvnd/egl_vendor.d/50_mesa.json

COPY ./nvidia_icd.json /usr/share/vulkan/icd.d/nvidia_icd.json
COPY ./10_nvidia.json /usr/share/glvnd/egl_vendor.d/10_nvidia.json

RUN apt-get update && \
    apt-get install -y --no-install-recommends software-properties-common && \
    add-apt-repository universe && \
    apt-get update && \
    apt-get install -y --no-install-recommends \
        libxcursor-dev \
        libxrandr-dev \
        libxinerama-dev \
        libxi-dev \
        mesa-common-dev \
        zip \
        unzip \
        make \
        # vulkan-utils \
        mesa-vulkan-drivers \
        pigz \
        git \
        libegl1

RUN apt-get install -y \
        mesa-utils \
        vulkan-tools

# 创建匹配宿主机用户
RUN if ! getent group teleophost >/dev/null; then \
    groupadd -g 1000 teleophost || groupadd teleophost; \
    fi

RUN if ! getent passwd teleophost >/dev/null; then \
    useradd -create-home -u 1000 -g teleophost -m teleophost || useradd -create-home -g teleophost -m teleophost; \
    fi


RUN apt install -y usbutils vim udev
RUN /lib/systemd/systemd-udevd --daemon


####################################
# adb devices
# * daemon not running; starting now at tcp:5037
# * daemon started successfully
# List of devices attached
# 2G0YC5ZGC900XR  unauthorized
####################################

RUN mkdir -p /home/teleophost/.local/lib
RUN apt install libnss3-tools

# 给 teleophost 用户 root 权限
RUN usermod -aG sudo teleophost && echo "teleophost ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
USER teleophost

#############################
# # 安装mkcert
# RUN apt-get install -y wget
# RUN wget https://github.com/FiloSottile/mkcert/releases/download/v1.4.4/mkcert-v1.4.4-linux-amd64 -O /usr/local/bin/mkcert
# RUN chmod +x /usr/local/bin/mkcert

# # 创建证书目录
# RUN mkdir -p /workspace/certification
# 生成证书（需要运行时指定实际IP）
#############################
# 替换为
#https://github.com/unitreerobotics/avp_teleoperate/issues/32

# 设置环境变量
ENV PYTHONPATH=/app:${PYTHONPATH} \
    ROS_DOMAIN_ID=0

# copy gym repo to docker
COPY --chown=teleophost . .

# install gym modules
ENV PATH="/home/teleophost/.local/bin:$PATH"
ENV NVIDIA_VISIBLE_DEVICES=all NVIDIA_DRIVER_CAPABILITIES=all


# post process
# sudo chown -R teleophost:teleophost /home/teleophost /workspace

# pip3 install --no-cache-dir cyclonedds==0.10.2 opencv-python pinocchio meshcat casadi
# # 设置工作目录
# pip install -e /workspace/unitree_sdk2_python

# cd /workspace
# pip install -r requirements.txt

#sudo vim /etc/udev/rules.d/51-android.rules
#SUBSYSTEM=="usb", ATTR{idVendor}=="2833", MODE="0666", GROUP="plugdev"

# cat /etc/udev/rules.d/51-android.rules | grep Quest
# lsusb | grep Quest
# sudo udevadm control --reload-rules
# sudo udevadm trigger
# cd /workspace/platform-tools
# sudo ./adb start-server

# [host run] ./adb devices，并且quest使用主账户登陆，才有弹窗,从而进入开发者模式
# sudo ./adb devices

# [host run] sudo ufw allow 8012 防火墙允许8012的端口
