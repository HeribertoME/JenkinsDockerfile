# ====================================================================== #
# Android SDK Docker Image
# ====================================================================== #

# Base image
# ---------------------------------------------------------------------- #
FROM jenkins/ssh-agent:latest-jdk17 as ssh-agent

# support multiarch: i386 architecture
# install Java
# install essential tools
ARG JDK_VERSION=17
RUN dpkg --add-architecture i386 && \
    apt-get update && \
    apt-get dist-upgrade -y && \
    apt-get install -y --no-install-recommends libncurses5:i386 libc6:i386 libstdc++6:i386 zlib1g:i386 && \
    apt-get install -y --no-install-recommends openjdk-${JDK_VERSION}-jdk && \
    apt-get install -y --no-install-recommends git wget unzip && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# download and install Gradle
# https://services.gradle.org/distributions/
ARG GRADLE_VERSION=8.0
ARG GRADLE_DIST=bin
RUN cd /opt && \
    wget -q https://services.gradle.org/distributions/gradle-${GRADLE_VERSION}-${GRADLE_DIST}.zip && \
    unzip gradle*.zip && \
    ls -d */ | sed 's/\/*$//g' | xargs -I{} mv {} gradle && \
    rm gradle*.zip

# download and install Kotlin compiler
# https://github.com/JetBrains/kotlin/releases/latest
ARG KOTLIN_VERSION=1.9.10
RUN cd /opt && \
    wget -q https://github.com/JetBrains/kotlin/releases/download/v${KOTLIN_VERSION}/kotlin-compiler-${KOTLIN_VERSION}.zip && \
    unzip *kotlin*.zip && \
    rm *kotlin*.zip

# download and install Android SDK
# https://developer.android.com/studio#command-line-tools-only
ARG ANDROID_SDK_VERSION=10406996
ARG ANDROID_BUILD_TOOLS_VERSION=34.0.0
ENV ANDROID_HOME /usr/local/android-sdk-linux
ENV ANDROID_SDK_ROOT /usr/local/android-sdk-linux
ENV CMDLINE_TOOLS_HOME $ANDROID_HOME/cmdline-tools/latest
ENV PATH /usr/local/bin:$PATH:$CMDLINE_TOOLS_HOME/bin:$ANDROID_HOME/:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools

RUN mkdir -p ${ANDROID_HOME}/cmdline-tools/latest && \
    cd ${ANDROID_HOME} && \
    wget -q https://dl.google.com/android/repository/commandlinetools-linux-${ANDROID_SDK_VERSION}_latest.zip && \
    unzip -qq *tools*linux*.zip -d tmp && \
    mv tmp/cmdline-tools/* cmdline-tools/latest && \
    rm -rf ${ANDROID_HOME}/tmp && \
    rm ${ANDROID_HOME}/*tools*linux*.zip

# set the environment variables
# For amd64
# ENV JAVA_HOME /usr/lib/jvm/java-${JDK_VERSION}-openjdk-amd64
# For arm64
ENV JAVA_HOME /usr/lib/jvm/java-${JDK_VERSION}-openjdk-arm64
ENV GRADLE_HOME /opt/gradle
ENV KOTLIN_HOME /opt/kotlinc
ENV PATH ${PATH}:${GRADLE_HOME}/bin:${KOTLIN_HOME}/bin:${ANDROID_HOME}/cmdline-tools/latest/bin:${ANDROID_HOME}/cmdline-tools/tools/bin

# accept the license agreements of the SDK components and install sdk components
ARG ANDROID_BUILD_TOOLS_VERSION=34.0.0
RUN yes|/usr/local/android-sdk-linux/cmdline-tools/latest/bin/sdkmanager --licenses && \
    /usr/local/android-sdk-linux/cmdline-tools/latest/bin/sdkmanager --update && \
    /usr/local/android-sdk-linux/cmdline-tools/latest/bin/sdkmanager --list && \
    /usr/local/android-sdk-linux/cmdline-tools/latest/bin/sdkmanager "platform-tools" \
                                                      "ndk;25.2.9519653" \
                                                      "extras;google;m2repository" \
                                                      "extras;android;m2repository" \
                                                      "platforms;android-33" \
#                                                      "emulator" \
                                                      "build-tools;$ANDROID_BUILD_TOOLS_VERSION" \
                                                      "add-ons;addon-google_apis-google-24" \
                                                      "add-ons;addon-google_apis-google-23"

# set android sdk environment variables
ENV PATH ${PATH}:${ANDROID_HOME}/platform-tools:${ANDROID_HOME}/emulator
# WORKAROUND: for issue https://issuetracker.google.com/issues/37137213
ENV LD_LIBRARY_PATH ${ANDROID_HOME}/emulator/lib64:${ANDROID_HOME}/emulator/lib64/qt/lib
# patch emulator issue: Running as root without --no-sandbox is not supported. See https://crbug.com/638180.
# https://doc.qt.io/qt-5/qtwebengine-platform-notes.html#sandboxing-support
ENV QTWEBENGINE_DISABLE_SANDBOX 1
