FROM ubuntu:20.04

LABEL Author="cacotopia@126.com"

ENV DEBIAN_FRONTEND=noninteractive \
    LANG=en_US.UTF-8 \
    LANGUAGE=en_US:en \
    LC_ALL=en_US.UTF-8 \
    JAVA_VERSION=8 \
    JAVA_UPDATE=202 \
    JAVA_BUILD=08  \
    JAVA_UPDATE_SMALL_VERSION=08 \
    JAVA_SIG=205943a0976c4ed48cb16f1043c5c647  \
    JAVA_BASE_URL=https://download.oracle.com/otn-pub/java \
    JAVA_BASE_MIRRORS_URL=https://repo.huaweicloud.com/java \
    JAVA_HOME=/opt/java \
    PATH=$PATH:${JAVA_HOME}/bin 

RUN \
  apt-get -y update && \
  apt-get -y install software-properties-common && \
  apt-get -y install apt-utils ca-certificates curl wget unzip && \
  apt-get -y install --reinstall locales && \
  rm -rf /root/.cache && \
  apt-get purge -y $(apt-cache search '~c' | awk '{ print $2 }') && \
  apt-get -y autoremove && \
  apt-get -y autoclean && \
  apt-get -y clean all && \
  rm -rf /var/lib/apt/lists/* && \
  rm -rf /var/cache/apt && \
  rm -rf /tmp/*

RUN locale-gen en_US.UTF-8

ENV LANG en_US.UTF-8
ENV LANGUAGE en_US.UTF-8
ENV LC_ALL en_US.UTF-8

#==============
# Install Oracle JDK
#==============

RUN \
    mkdir -p  "${JAVA_HOME}" \
    && cd /tmp \
##    && curl -Ls \
##        --cookie "oraclelicense=accept-securebackup-cookie;" \
##        "${JAVA_BASE_URL}/jdk/${JAVA_VERSION}"+"${JAVA_BUILD}"/"${JAVA_SIG}"/jdk-"${JAVA_VERSION}"_linux-x64_bin.tar.gz \
    && wget --no-check-certificate "${JAVA_BASE_MIRRORS_URL}/jdk/${JAVA_VERSION}u${JAVA_UPDATE}-b${JAVA_UPDATE_SMALL_VERSION}/jdk-${JAVA_VERSION}u${JAVA_UPDATE}-linux-x64.tar.gz" \
    && tar -xzf "jdk-${JAVA_VERSION}u${JAVA_UPDATE}-linux-x64.tar.gz" -C "${JAVA_HOME}" --strip-components=1 \
    && ln -s ${JAVA_HOME}/bin/* /usr/bin/ \
    && rm -rf "${JAVA_HOME}/"*src.zip \
    && wget --no-check-certificate -c --header "Cookie: oraclelicense=accept-securebackup-cookie" "${JAVA_BASE_URL}/jce/${JAVA_VERSION}/jce_policy-${JAVA_VERSION}.zip" \
    && unzip -jo -d "${JAVA_HOME}/jre/lib/security" "jce_policy-${JAVA_VERSION}.zip" \
    && rm "${JAVA_HOME}/jre/lib/security/README.txt" \
    && rm -rf /tmp/* \
    && update-alternatives --install "/usr/bin/java" "java" "${JAVA_HOME}/bin/java" 1 \
    && update-alternatives --install "/usr/bin/javac" "javac" "${JAVA_HOME}/bin/javac" 1 \
    && update-alternatives --set java "${JAVA_HOME}/bin/java" \
    && update-alternatives --set javac "${JAVA_HOME}/bin/javac"

#==============
# Show version
#==============
RUN java -version \
    && javac -version