FROM jenkins/jenkins:lts

LABEL version="1.0"
LABEL description="Jenkins with Android SDK"

ENV DEBIAN_FRONTEND noninteractive

RUN /usr/local/bin/install-plugins.sh android-lint git matrix-auth credentials-binding workflow-aggregator

ENV JENKINS_USER admin
ENV JENKINS_PASS admin

# Skip initial setup
ENV JAVA_OPTS -Djenkins.install.runSetupWizard=false

COPY executors.groovy /usr/share/jenkins/ref/init.groovy.d/
COPY default-user.groovy /usr/share/jenkins/ref/init.groovy.d/


USER root
##################
# Install gradle
##################
ENV GRADLE_HOME /opt/gradle

ENV GRADLE_VERSION 6.2.2
ARG GRADLE_DOWNLOAD_SHA256=0f6ba231b986276d8221d7a870b4d98e0df76e6daf1f42e7c0baec5032fb7d17
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
    && ln --symbolic "${GRADLE_HOME}/bin/gradle" /usr/bin/gradle 


#####################
# Install Android SDK
#####################
ARG VERSION_SDK_TOOLS="4333796"
# mirror versions from dodroid project for now
ARG ANDROID_API_LEVEL=28
ARG ANDROID_BUILD_TOOLS_LEVEL=29.0.2


ENV ANDROID_HOME /opt/android-sdk-linux
ENV PATH "$PATH:$ANDROID_HOME/tools/bin:$ANDROID_HOME/platform-tools"

RUN apt-get -qqy update \
    && apt-get install -qqy --no-install-recommends \
    openjdk-8-jdk \
    git \
    unzip \
    # TODO: do we really need those utils? when do we need them?
    # libglu1 \
    # libpulse-dev \
    # libasound2 \
    # libc6  \
    # libstdc++6 \
    # libx11-6 \
    # libx11-xcb1 \
    # libxcb1 \
    # libxcomposite1 \
    # libxcursor1 \
    # libxi6  \
    # libxtst6 \
    # libnss3 \
    wget \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN mkdir -p ${ANDROID_HOME} && \
    cd ${ANDROID_HOME} && \
    wget -q  "https://dl.google.com/android/repository/sdk-tools-linux-${VERSION_SDK_TOOLS}.zip" -O android_tools.zip && \
    unzip android_tools.zip && \
    rm android_tools.zip

RUN mkdir -p /root/.android \
    && touch /root/.android/repositories.cfg \
    && sdkmanager --update \
    && yes Y | sdkmanager --licenses \
    && chown -R jenkins ${ANDROID_HOME}
    # && chmod 777 -R ${ANDROID_HOME}

# RUN yes Y | sdkmanager --install \
    # 'platform-tools' \
    # "platforms;android-${ANDROID_API_LEVEL}" \
    # "build-tools;${ANDROID_BUILD_TOOLS_LEVEL}" \
    # 'extras;m2repository;com;android;support;constraint;constraint-layout-solver;1.0.2' \
    # 'extras;m2repository;com;android;support;constraint;constraint-layout;1.0.2' \
    # 'extras;google;m2repository' \
    # 'extras;android;m2repository' \
    # 'extras;google;google_play_services' \
    # && yes Y | sdkmanager --licenses

USER jenkins