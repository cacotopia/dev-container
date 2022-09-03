ARG ORACLE_JDK_VERSION=jdk-8u202

FROM cacotopia/oracle-jdk:${ORACLE_JDK_VERSION}

LABEL Author="cacotopia@126.com"

ENV MAVEN_VERSION=3.8.6
ENV ROOTHOME=/root

ENV HOME=$ROOTHOME

ENV MAVEN_HOME="/usr/local/lib/apache-maven-3.8.6" \
    PATH=$PATH:${MAVEN_HOME}/bin 

# RUN apt-get update --allow-unauthenticated \
#     && apt install --allow-unauthenticated --force-yes -y ca-certificates curl \
#            build-essential gcc g++ \
#            tzdata zip unzip vim wget \
#            git openssh-client zsh bash net-tools tmux sudo wget \
#     #
#     # Clean up
#     && apt-get autoremove -y \
#     && apt-get clean -y \
#     && rm -rf /var/lib/apt/lists/* 

# install maven

RUN mkdir -p ${MAVEN_HOME} \
    && cd /tmp \
    && wget --no-check-certificate "https://dlcdn.apache.org/maven/maven-3/${MAVEN_VERSION}/binaries/apache-maven-${MAVEN_VERSION}-bin.tar.gz" \
    && tar -xzf "apache-maven-${MAVEN_VERSION}-bin.tar.gz" -C "${MAVEN_HOME}" --strip-components=1 \ 
    && ln -s ${MAVEN_HOME}/bin/* /usr/bin/ \
    && rm apache-maven-"${MAVEN_VERSION}"-bin.tar.gz \
    && rm -rf /tmp/*

# RUN sh -c "$(wget -O- https://github.com/deluan/zsh-in-docker/releases/download/v1.1.1/zsh-in-docker.sh)" -- \
COPY ../../zsh/zsh-in-docker.sh /tmp
RUN chmod +x /tmp/zsh-in-docker.sh && \
    /tmp/zsh-in-docker.sh \
    -a 'SPACESHIP_PROMPT_ADD_NEWLINE="false"' \
    -a 'SPACESHIP_PROMPT_SEPARATE_LINE="false"' \
    -p git \
    -p https://github.com/zsh-users/zsh-autosuggestions \
    -p https://github.com/zsh-users/zsh-completions \
    -p https://github.com/zsh-users/zsh-history-substring-search \
    -p https://github.com/zsh-users/zsh-syntax-highlighting \
    -p 'history-substring-search' \
    -a 'bindkey "\$terminfo[kcuu1]" history-substring-search-up' \
    -a 'bindkey "\$terminfo[kcud1]" history-substring-search-down'

# Copy local config to p10k zsh
COPY ../../zsh/.p10k.zsh $ROOTHOME/
COPY ../../zsh/.tmux.conf $ROOTHOME/

RUN mkdir $ROOTHOME/.m2

COPY ../../mvn/settings.xml $ROOTHOME/.m2/

ENTRYPOINT [ "/bin/zsh" ]

CMD ["-l"]