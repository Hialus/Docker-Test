FROM atlassian/bamboo-server:7.2.3

LABEL description="Bamboo pre-configured for Artemis, pre-installed with Swift"

USER root
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update && apt-get install -y --no-install-recommends \
    software-properties-common=0.9* \
    apt-utils=2.0.* \
    maven=3.6.* \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
RUN add-apt-repository -y ppa:linuxuprising/java
RUN echo debconf shared/accepted-oracle-license-v1-3 select true | debconf-set-selections && echo debconf shared/accepted-oracle-license-v1-3 seen true | debconf-set-selections
RUN apt-get install -y --no-install-recommends oracle-java17-installer=17.*
RUN update-alternatives --install /usr/bin/java java /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java 1
RUN update-alternatives --set java /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java
# Add file in /artemis/bin/mvn that uses the correct java version and passes all arguments to mvn
RUN mkdir /artemis && mkdir /artemis/bin && printf '#!/bin/bash\nJAVA_HOME=/usr/lib/jvm/java-17-oracle /usr/bin/mvn "$@"\n' > /artemis/bin/mvn && chmod 777 /artemis/bin/mvn

# Install Swift by copying relevant files
COPY --from=swift:latest /usr /usr

# Install SwiftLint
RUN apt-get install -y --no-install-recommends \
    libcurl4-openssl-dev \
    libxml2-dev
RUN git clone --branch master https://github.com/realm/SwiftLint.git && \
    cd SwiftLint && \
    swift build --configuration release -Xswiftc -static-stdlib && \
    mv $(swift build --configuration release -Xswiftc -static-stdlib --show-bin-path)/swiftlint /usr/bin && \
    cd .. && \
    rm -rf SwiftLint

USER ${BAMBOO_USER}
