ARG BASE_IMAGE

FROM ${BASE_IMAGE}

ENV NVIDIA_DRIVER_CAPABILITIES ${NVIDIA_DRIVER_CAPABILITIES:-compute,utility}
ENV DEBIAN_FRONTEND=noninteractive

RUN apt update && \
    apt install -y \
        wget build-essential zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev \
        libreadline-dev libffi-dev libsqlite3-dev libbz2-dev liblzma-dev && \
    apt install -y \
        build-essential apt-utils ca-certificates wget git vim libssl-dev curl unzip unrar openssh-client openssh-server \
        dbus distro-info-data dmsetup file gir1.2-glib-2.0 krb5-locales libapparmor1 \
        systemd systemd-sysv systemd-timesyncd tzdata ucf xauth xdg-user-dirs \
        python3-urllib3 python3.8 python3.8-minimal shared-mime-info ssh-import-id \
        python3-minimal python3-pkg-resources python3-requests python3-six \
        python3-chardet python3-dbus python3-distro python3-gi python3-idna \
        ncurses-term networkd-dispatcher openssh-sftp-server python3 python3-certifi \
        libxau6 libxcb1 libxdmcp6 libxext6 libxml2 libxmuu1 lsb-release mime-support \
        libpython3.8-minimal libpython3.8-stdlib libwrap0 libx11-6 libx11-data \
        libmagic1 libmpdec2 libnss-systemd libpam-systemd libpython3-stdlib \
        libk5crypto3 libkeyutils1 libkmod2 libkrb5-3 libkrb5support0 libmagic-mgc \
        libglib2.0-0 libglib2.0-data libgssapi-krb5-2 libicu66 libip4tc2 libjson-c4 \
        libdevmapper1.02.1 libedit2 libexpat1 libfido2-1 libgirepository-1.0-1 \
        libargon2-1 libbsd0 libcap2 libcbor0.6 libcryptsetup12 libdbus-1-3 \
        software-properties-common && \
    apt clean && \
    rm -rf /var/lib/apt/lists/*

ARG PYTHON_VERSION

RUN cd /tmp && \
    wget https://www.python.org/ftp/python/${PYTHON_VERSION}/Python-${PYTHON_VERSION}.tgz && \
    tar -xvf Python-${PYTHON_VERSION}.tgz && \
    cd Python-${PYTHON_VERSION} && \
    ./configure --enable-optimizations && \
    make && make install && \
    cd .. && rm Python-${PYTHON_VERSION}.tgz && rm -r Python-${PYTHON_VERSION} && \
    ln -s /usr/local/bin/python3 /usr/local/bin/python && \
    ln -s /usr/local/bin/pip3 /usr/local/bin/pip && \
    python -m pip install --upgrade pip && \
    rm -r /root/.cache/pip

ARG PYTORCH_VERSION
ARG PYTORCH_VERSION_SUFFIX
ARG TORCHVISION_VERSION
ARG TORCHVISION_VERSION_SUFFIX
ARG TORCHAUDIO_VERSION
ARG TORCHAUDIO_VERSION_SUFFIX
ARG PYTORCH_DOWNLOAD_URL
ARG SM35_PYTORCH_DOWNLOAD_URL

RUN if [ ! $TORCHAUDIO_VERSION ]; \
    then \
        TORCHAUDIO=; \
    else \
        TORCHAUDIO=torchaudio==${TORCHAUDIO_VERSION}${TORCHAUDIO_VERSION_SUFFIX}; \
    fi && \
    if [ ! $PYTORCH_DOWNLOAD_URL ]; \
    then \
        pip install \
            torch==${PYTORCH_VERSION}${PYTORCH_VERSION_SUFFIX} \
            torchvision==${TORCHVISION_VERSION}${TORCHVISION_VERSION_SUFFIX} \
            ${TORCHAUDIO}; \
    else \
        pip install \
            torch==${PYTORCH_VERSION}${PYTORCH_VERSION_SUFFIX} \
            torchvision==${TORCHVISION_VERSION}${TORCHVISION_VERSION_SUFFIX} \
            ${TORCHAUDIO} \
            -f ${PYTORCH_DOWNLOAD_URL}; \
    fi && \
    if [ $SM35_PYTORCH_DOWNLOAD_URL ]; \
    then \
        pip install --upgrade --force-reinstall\
            torch==${PYTORCH_VERSION}${PYTORCH_VERSION_SUFFIX} \
            -f ${SM35_PYTORCH_DOWNLOAD_URL}; \
    fi && \
    rm -r /root/.cache/pip

WORKDIR /workspace
RUN pip config set global.index-url https://mirrors.bfsu.edu.cn/pypi/web/simple
RUN sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list
RUN apt-get clean
