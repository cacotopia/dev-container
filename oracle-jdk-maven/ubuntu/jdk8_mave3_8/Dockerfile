ARG ORACLE_JDK_VERSION=jdk-8u202-ubuntu

FROM cacotopia/oracle-jdk:${ORACLE_JDK_VERSION}

LABEL Author="cacotopia@126.com"

ENV MAVEN_VERSION=3.8.6
ENV ROOTHOME=/root

ENV HOME=$ROOTHOME

ENV MAVEN_HOME="/opt/maven" \
    ROOT_MAVEN_CONFIG="${ROOTHOME}/.m2" \
    PATH=$PATH:${MAVEN_HOME}/bin 

#==============
# Install packages
#==============

RUN set -o errexit -o nounset \
    && apt-get update --allow-unauthenticated \
    && apt install --allow-unauthenticated --allow-change-held-packages -y ca-certificates  \
           build-essential gcc g++ \
           tzdata zip unzip gzip vim wget curl bzr \
           git git-lfs mercurial subversion \
           openssh-client zsh bash net-tools tmux sudo wget \
    #
    # Clean up
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/* \
    #
    # Testing VCSes
    && echo "Testing VCSes" \
    && which bzr \
    && which git \
    && which git-lfs \
    && which hg \
    && which svn

# Mark zsh as default shell
RUN chsh -s /usr/bin/zsh

#==============
# Add Group And User
#==============

RUN set -o errexit -o nounset \
    && echo "Adding maven user and group" \
    && groupadd --system --gid 1000 maven \
    && useradd --system --gid maven --uid 1000 --shell /bin/bash --create-home maven \
    && mkdir /home/maven/.m2 \
    && chown --recursive maven:maven /home/maven \
    \
    && echo "Symlinking root Maven cache to maven Maven cache" \
    && ln --symbolic /home/maven/.m2 "${ROOT_MAVEN_CONFIG}"

RUN mkdir $ROOTHOME/.m2

VOLUME ["/home/maven/.m2"]

WORKDIR /home/maven

#==============
# Install Maven
#==============

RUN mkdir -p ${MAVEN_HOME} \
    && cd /tmp \
    && set -o errexit -o nounset \
    && echo "Downloading Maven" \
    && wget --no-check-certificate "https://dlcdn.apache.org/maven/maven-3/${MAVEN_VERSION}/binaries/apache-maven-${MAVEN_VERSION}-bin.tar.gz" \
    # \
    # && echo "Checking download hash" \
    # && echo "${GRADLE_DOWNLOAD_SHA256} *gradle.zip" | sha256sum --check - \
    # \
    && echo "Installing Maven" \
    && tar -xzf "apache-maven-${MAVEN_VERSION}-bin.tar.gz" -C "${MAVEN_HOME}" --strip-components=1 \ 
    && ln -s ${MAVEN_HOME}/bin/mvn /usr/bin/mvn \
    && rm apache-maven-"${MAVEN_VERSION}"-bin.tar.gz \
    && rm -rf /tmp/* \
    && echo "Testing Maven installation" \
    && mvn -version

#==============
# Install ohmyzsh
#==============
# RUN sh -c "$(wget -O- https://github.com/deluan/zsh-in-docker/releases/download/v1.1.1/zsh-in-docker.sh)" -- \
# COPY ./zsh/zsh-in-docker.sh /tmp
# RUN chmod +x /tmp/zsh-in-docker.sh && \
#     /tmp/zsh-in-docker.sh \
#     -a 'SPACESHIP_PROMPT_ADD_NEWLINE="false"' \
#     -a 'SPACESHIP_PROMPT_SEPARATE_LINE="false"' \
#     -p git \
#     -p https://github.com/zsh-users/zsh-autosuggestions \
#     -p https://github.com/zsh-users/zsh-completions \
#     -p https://github.com/zsh-users/zsh-history-substring-search \
#     -p https://github.com/zsh-users/zsh-syntax-highlighting \
#     -p 'history-substring-search' \
#     -a 'bindkey "\$terminfo[kcuu1]" history-substring-search-up' \
#     -a 'bindkey "\$terminfo[kcud1]" history-substring-search-down'

# # Copy local config to p10k zsh
# COPY ./zsh/.p10k.zsh $ROOTHOME/
# COPY ./zsh/.tmux.conf $ROOTHOME/

# ENTRYPOINT [ "/bin/zsh" ]

CMD ["-l"]
# CMD ["mvn"]