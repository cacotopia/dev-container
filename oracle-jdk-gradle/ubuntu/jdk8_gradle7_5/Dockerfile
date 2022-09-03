ARG ORACLE_JDK_VERSION=jdk-8u202-ubuntu

FROM cacotopia/oracle-jdk:${ORACLE_JDK_VERSION}

LABEL Author="cacotopia@126.com"

ENV ROOTHOME=/root

ENV HOME=$ROOTHOME

ENV GRADLE_HOME="/opt/gradle" \
    GRADLE_VERSION="7.5.1" \
    GRADLE_CONFIG="${HOME}/.m2" \
    PATH=$PATH:${MAVEN_HOME}/bin 

ARG GRADLE_DOWNLOAD_SHA256=f6b8596b10cce501591e92f229816aa4046424f3b24d771751b06779d58c8ec4

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
    && echo "Testing VCSes" \
    && which bzr \
    && which git \
    && which git-lfs \
    && which hg \
    && which svn

RUN set -o errexit -o nounset \
    && echo "Adding gradle user and group" \
    && groupadd --system --gid 1000 gradle \
    && useradd --system --gid gradle --uid 1000 --shell /bin/bash --create-home gradle \
    && mkdir /home/gradle/.gradle \
    && chown --recursive gradle:gradle /home/gradle \
    \
    && echo "Symlinking root Gradle cache to gradle Gradle cache" \
    && ln --symbolic /home/gradle/.gradle /root/.gradle

VOLUME /home/gradle/.gradle

WORKDIR /home/gradle

#==============
# Install Gradle
#==============

RUN set -o errexit -o nounset \
    && echo "Downloading Gradle" \
    && wget --no-verbose --output-document=gradle.zip "https://services.gradle.org/distributions/gradle-${GRADLE_VERSION}-bin.zip" \
    \
    && echo "Checking download hash" \
    && echo "${GRADLE_DOWNLOAD_SHA256} *gradle.zip" | sha256sum --check - \
    \
    && echo "Installing Gradle" \
    && unzip gradle.zip \
    && rm gradle.zip \
    && mv "gradle-${GRADLE_VERSION}" "${GRADLE_HOME}/" \
    && ln --symbolic "${GRADLE_HOME}/bin/gradle" /usr/bin/gradle \
    \
    && echo "Testing Gradle installation" \
    && gradle --version

# Mark zsh as default shell
RUN chsh -s /usr/bin/zsh

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

RUN mkdir $ROOTHOME/.m2

# COPY ./mvn/settings.xml $ROOTHOME/.m2/

ENTRYPOINT [ "/bin/zsh" ]

CMD ["gradle"]