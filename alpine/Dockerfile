FROM jenkins/jenkins:2.226-alpine

LABEL author="Maxim G"
LABEL version="1.0"
LABEL description="Jenkins with Android SDK"

ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /home
USER root
################
# Install gradle
################
ENV GRADLE_HOME /opt/gradle
ENV GRADLE_VERSION 6.2.2

ARG GRADLE_DOWNLOAD_SHA256=0f6ba231b986276d8221d7a870b4d98e0df76e6daf1f42e7c0baec5032fb7d17
RUN set -o errexit -o nounset \
    && echo "Downloading Gradle" \
    && wget -qO gradle.zip "https://services.gradle.org/distributions/gradle-${GRADLE_VERSION}-bin.zip" \
    \
    && echo "Checking download hash" \
    && echo "${GRADLE_DOWNLOAD_SHA256} *gradle.zip" | sha256sum -c - \
    \
    && echo "Installing Gradle" \
    && unzip gradle.zip \
    && rm gradle.zip \
    && mkdir -p /opt \
    && mv "gradle-${GRADLE_VERSION}" "${GRADLE_HOME}/" \
    && ln -s "${GRADLE_HOME}/bin/gradle" /usr/bin/gradle 
###########################
# Intall tools for Android
###########################
# https://developer.android.com/studio/index.html#command-tools
ENV SDK_TOOLS "4333796"
ENV ANDROID_HOME "/opt/sdk"
ENV PATH=$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/tools/bin:$ANDROID_HOME/platform-tools

# Install required dependencies
RUN apk add --no-cache bash git unzip wget && \
    rm -rf /tmp/* && \
    rm -rf /var/cache/apk/*

# Download and extract Android Tools
RUN wget -q https://dl.google.com/android/repository/sdk-tools-linux-${SDK_TOOLS}.zip -O /tmp/tools.zip && \
    mkdir -p ${ANDROID_HOME} && \
    unzip -qq /tmp/tools.zip -d ${ANDROID_HOME} && \
    rm -v /tmp/tools.zip

# Install SDK Packages
RUN mkdir -p ~/.android/ && touch ~/.android/repositories.cfg \
    && yes | ${ANDROID_HOME}/tools/bin/sdkmanager "--licenses" \
    && ${ANDROID_HOME}/tools/bin/sdkmanager \
    "platform-tools" \
    "extras;android;m2repository" \
    "extras;google;m2repository" \
    "extras;google;instantapps" \    
    && ${ANDROID_HOME}/tools/bin/sdkmanager "--update"

# Install SDK Packages
RUN echo "Installing platforms" \
    && ${ANDROID_HOME}/tools/bin/sdkmanager \
    "platforms;android-28" \
    "platforms;android-27" \
    "platforms;android-26" \
    "platforms;android-25" \
    "platforms;android-24" \
    "platforms;android-23" \
    && echo "Installing buidtools" \
    && ${ANDROID_HOME}/tools/bin/sdkmanager \
    "build-tools;29.0.2" \
    "build-tools;29.0.3" \
    && echo "Installing Google APIs" \
    && ${ANDROID_HOME}/tools/bin/sdkmanager \
    "extras;google;google_play_services" \
    "extras;m2repository;com;android;support;constraint;constraint-layout;1.0.2" \
    "extras;m2repository;com;android;support;constraint;constraint-layout;1.0.1" \
    && ${ANDROID_HOME}/tools/bin/sdkmanager \
    "add-ons;addon-google_apis-google-24" \
    "add-ons;addon-google_apis-google-23" \
    "add-ons;addon-google_apis-google-22" \
    "add-ons;addon-google_apis-google-21" \
    "add-ons;addon-google_apis-google-19" \
    "add-ons;addon-google_apis-google-18" \
    # Option is valid only when a nested virtualization is enabled
    && echo "Installing emulator " \
    && yes | "$ANDROID_HOME"/tools/bin/sdkmanager "emulator"

USER jenkins
RUN /usr/local/bin/install-plugins.sh git android-emulator