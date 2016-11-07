#== Ubuntu xenial is 16.04, i.e. FROM ubuntu:16.04
# Find latest images at https://hub.docker.com/r/library/ubuntu/
# Layer size: big: 127.2 MB
#  [tiny 0~4MB, small 5~9MB, medium 10~39MB, big 40~150MB, huge >150MB]
FROM ubuntu:xenial-20161010
ENV UBUNTU_FLAVOR="xenial" \
    UBUNTU_DATE="20161010"

#== Ubuntu flavors - common
RUN  echo "deb http://archive.ubuntu.com/ubuntu ${UBUNTU_FLAVOR} main universe\n" > /etc/apt/sources.list \
  && echo "deb http://archive.ubuntu.com/ubuntu ${UBUNTU_FLAVOR}-updates main universe\n" >> /etc/apt/sources.list \
  && echo "deb http://archive.ubuntu.com/ubuntu ${UBUNTU_FLAVOR}-security main universe\n" >> /etc/apt/sources.list

MAINTAINER Team TIP <diemol+team-tip@gmail.com>

# No interactive frontend during docker build
ENV DEBIAN_FRONTEND=noninteractive \
    DEBCONF_NONINTERACTIVE_SEEN=true

# http://askubuntu.com/a/235911/134645
# Remove with: sudo apt-key del 2EA8F35793D8809A
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 2EA8F35793D8809A \
  && apt-key update -qqy
# Remove with: sudo apt-key del 40976EAF437D05B5
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 40976EAF437D05B5 \
  && apt-key update -qqy
# Remove with: sudo apt-key del 3B4FE6ACC0B21F32
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 3B4FE6ACC0B21F32 \
  && apt-key update -qqy
# Remove with: sudo apt-key del A2F683C52980AECF
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys A2F683C52980AECF \
  && apt-key update -qqy

#========================
# Miscellaneous packages
#========================
# libltdl7        0.3 MB
#   allows to run docker alongside docker
# netcat-openbsd  0.5 MB
#   inlcues `nc` an arbitrary TCP and UDP connections and listens
# pwgen           0.4 MB
#   generates random, meaningless but pronounceable passwords
# bc              0.5 MB
#   An arbitrary precision calculator language
# unzip           0.7 MB
#   uncompress zip files
# bzip2           1.29 MB
#   uncompress bzip files
# apt-utils       1.0 MB
#   commandline utilities related to package management with APT
# net-tools       0.8 MB
#   arp, hostname, ifconfig, netstat, route, plipconfig, iptunnel
# jq              1.1 MB
#   jq is like sed for JSON data, you can use it to slice and filter and map
# sudo            1.3 MB
#   sudo binary
# psmisc          1.445 MB
#   fuser – identifies what processes are using files.
#   killall – kills a process by its name, similar to a pkill Unices.
#   pstree – Shows currently running processes in a tree format.
#   peekfd – Peek at file descriptors of running processes.
# iproute2        2.971 MB
#   to use `ip` command
# iputils-ping    3.7 MB
#   ping, ping6 - send ICMP ECHO_REQUEST to network hosts
# dbus-x11        4.6 MB
#   is needed to avoid http://askubuntu.com/q/237893/134645
# wget            7.3 MB
#   The non-interactive network downloader
# curl             17 MB (real +diff when with wget: 7 MB)
#   transfer URL data using various Internet protocols
#
# Layer size: medium: 29.8 MB
# Layer size: medium: 27.9 MB (with --no-install-recommends)
#  [tiny 0~4MB, small 5~9MB, medium 10~39MB, big 40~150MB, huge >150MB]
RUN apt-get -qqy update \
  && apt-get -qqy install \
    libltdl7 \
    netcat-openbsd \
    pwgen \
    bc \
    unzip \
    bzip2 \
    apt-utils \
    net-tools \
    jq \
    sudo \
    psmisc \
    iproute2 \
    iputils-ping \
    dbus-x11 \
    wget \
    curl \
  && apt-get -qyy autoremove \
  && rm -rf /var/lib/apt/lists/*

#==============================
# Locale and encoding settings
#==============================
# TODO: Allow to change instance language OS and Browser level
#  see if this helps: https://github.com/rogaha/docker-desktop/blob/68d7ca9df47b98f3ba58184c951e49098024dc24/Dockerfile#L57
ENV LANG_WHICH en
ENV LANG_WHERE US
ENV ENCODING UTF-8
ENV LANGUAGE ${LANG_WHICH}_${LANG_WHERE}.${ENCODING}
ENV LANG ${LANGUAGE}
# Layer size: small: 8.956 MB
# Layer size: small: 8.956 MB (with --no-install-recommends)
#  [tiny 0~4MB, small 5~9MB, medium 10~39MB, big 40~150MB, huge >150MB]
RUN locale-gen ${LANGUAGE} \
  && dpkg-reconfigure --frontend noninteractive locales \
  && apt-get -qqy update \
  && apt-get -qqy --no-install-recommends install \
    language-pack-en \
  && apt-get -qyy autoremove \
  && rm -rf /var/lib/apt/lists/*

#===================
# Timezone settings
#===================
# Full list at https://en.wikipedia.org/wiki/List_of_tz_database_time_zones
#  e.g. "US/Pacific" for Los Angeles, California, USA
# e.g. ENV TZ "US/Pacific"
ENV TZ "Europe/Berlin"
# Apply TimeZone
# Layer size: tiny: 1.339 MB
#  [tiny 0~4MB, small 5~9MB, medium 10~39MB, big 40~150MB, huge >150MB]
RUN echo "Setting time zone to '${TZ}'" \
  && echo ${TZ} > /etc/timezone \
  && dpkg-reconfigure --frontend noninteractive tzdata

#========================================
# Add normal user with passwordless sudo
#========================================
# Layer size: tiny: 0.3 MB
#  [tiny 0~4MB, small 5~9MB, medium 10~39MB, big 40~150MB, huge >150MB]
RUN useradd seluser \
         --shell /bin/bash  \
         --create-home \
  && usermod -a -G sudo seluser \
  && gpasswd -a seluser video \
  && echo 'ALL ALL = (ALL) NOPASSWD: ALL' >> /etc/sudoers \
  && echo 'seluser:secret' | chpasswd

#==============================
# Java8 - OpenJDK JRE headless
# Minimal runtime used for executing non GUI Java programs
#==============================
# Regarding urandom see
#  http://stackoverflow.com/q/26021181/511069
#  https://github.com/SeleniumHQ/docker-selenium/issues/14#issuecomment-67414070
# Layer size: big: 132.2 MB
# Layer size: big: 132.2 MB (with --no-install-recommends)
#  [tiny 0~4MB, small 5~9MB, medium 10~39MB, big 40~150MB, huge >150MB]
RUN apt-get -qqy update \
  && apt-get -qqy install \
    openjdk-8-jre-headless \
  && sed -i 's/securerandom.source=file:\/dev\/urandom/securerandom.source=file:\/dev\/.\/urandom/g' \
       /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/security/java.security \
  && sed -i 's/securerandom.source=file:\/dev\/random/securerandom.source=file:\/dev\/.\/urandom/g' \
       /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/security/java.security \
  && apt-get -qyy autoremove \
  && rm -rf /var/lib/apt/lists/*

#==================
# Java8 - Oracle
#==================
# Regarding urandom see
#  http://stackoverflow.com/q/26021181/511069
#  https://github.com/SeleniumHQ/docker-selenium/issues/14#issuecomment-67414070
# Layer size: huge: 618.6 MB (with --no-install-recommends)
# Layer size: huge: 661.1 MB
#  [tiny 0~4MB, small 5~9MB, medium 10~39MB, big 40~150MB, huge >150MB]
# RUN apt-get -qqy update \
#   && apt-get -qqy --no-install-recommends install \
#     software-properties-common \
#   && echo debconf shared/accepted-oracle-license-v1-1 \
#       select true | debconf-set-selections \
#   && echo debconf shared/accepted-oracle-license-v1-1 \
#       seen true | debconf-set-selections \
#   && add-apt-repository ppa:webupd8team/java \
#   && apt-get -qqy update \
#   && apt-get -qqy install \
#     oracle-java8-installer \
#   && sed -i 's/securerandom.source=file:\/dev\/urandom/securerandom.source=file:\/dev\/.\/urandom/g' \
#        /usr/lib/jvm/java-8-oracle/jre/lib/security/java.security \
#   && sed -i 's/securerandom.source=file:\/dev\/random/securerandom.source=file:\/dev\/.\/urandom/g' \
#        /usr/lib/jvm/java-8-oracle/jre/lib/security/java.security \
#   && apt-get -qqy install \
#     oracle-java8-set-default \
#   && apt-get -qyy autoremove \
#   && rm -rf /var/lib/apt/lists/*

#==============================================
# Java blocks until kernel have enough entropy
# to generate the /dev/random seed
#==============================================
# See: SeleniumHQ/docker-selenium/issues/14
# Layer size: tiny: 0.8 MB
#  [tiny 0~4MB, small 5~9MB, medium 10~39MB, big 40~150MB, huge >150MB]
RUN apt-get -qqy update \
  && apt-key update -qqy \
  && apt-get -qqy install \
    haveged rng-tools \
  && service haveged start \
  && update-rc.d haveged defaults \
  && apt-get -qyy autoremove \
  && rm -rf /var/lib/apt/lists/*

#===================
# Get docker binary
#===================
# https://github.com/docker-library/docker/blob/master/1.12/Dockerfile#L1
# Layer size: medium: 15.67 MB (by keeping only the docker binary)
# Layer size: big...: 98.38 MB
#  [tiny 0~4MB, small 5~9MB, medium 10~39MB, big 40~150MB, huge >150MB]
ENV DOCKER_BUCKET="get.docker.com" \
    DOCKER_VERSION="1.12.3" \
    DOCKER_SHA256="626601deb41d9706ac98da23f673af6c0d4631c4d194a677a9a1a07d7219fa0f" \
    DOCKER_HOST="unix:///var/run/docker.sock"
RUN set -x \
  && curl -fSL "https://${DOCKER_BUCKET}/builds/Linux/x86_64/docker-${DOCKER_VERSION}.tgz" \
          -o docker.tgz \
  && echo "${DOCKER_SHA256} *docker.tgz" | sha256sum -c - \
  && tar -xzvf docker.tgz \
  && mv docker/docker /usr/local/bin/ \
  && rm -rf docker/ \
  && rm docker.tgz \
  && docker --version

#===================================================
# Run the following commands as non-privileged user
#===================================================
USER seluser
WORKDIR /home/seluser

#==========
# Selenium
#==========
# Layer size: medium: 21.23 MB
#  [tiny 0~4MB, small 5~9MB, medium 10~39MB, big 40~150MB, huge >150MB]
ENV SEL_MAJOR_VER="${selenium-server.major-minor.version}" \
    SEL_PATCH_LEVEL_VER="${selenium-server.patch-level.version}"
ENV SEL_VER="${SEL_MAJOR_VER}.${SEL_PATCH_LEVEL_VER}"
RUN  export SELBASE="https://selenium-release.storage.googleapis.com" \
  && export SELPATH="${SEL_MAJOR_VER}/selenium-server-standalone-${SEL_VER}.jar" \
  && wget -nv ${SELBASE}/${SELPATH}

#===========
# Make a backup of Sauce Labs capabilities, in case the online fetch does not work
#===========
RUN  export SAUCE_LABS_CAPABILITIES="https://saucelabs.com/rest/v1/info/platforms/webdriver" \
  && wget -nv ${SAUCE_LABS_CAPABILITIES} -O saucelabs_capabilities.json

#==========
# Zalenium
#==========
ENV ZAL_VER="${project.build.finalName}"
ADD entry.sh /usr/bin/
ADD zalenium.sh /home/seluser/
ADD ${ZAL_VER}.jar /home/seluser/${ZAL_VER}.jar
# https://github.com/zalando-incubator/zalenium/releases/download/v${ZAL_VER}/zalenium-release-v${ZAL_VER}.tar.gz

#-----------------#
# Fix perms again #
#-----------------#
RUN sudo chmod +x /home/seluser/zalenium.sh \
  && sudo chmod +x /usr/bin/entry.sh

# IMPORTANT: Using the string form `CMD "entry.sh"` without
# brackets [] causes Docker to run your process
# And using `bash` which doesn’t handle signals properly
ENTRYPOINT ["entry.sh"]