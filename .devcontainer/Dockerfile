# Inspired by: https://github.com/matsp/docker-flutter
FROM ubuntu:22.04

ENV UID=1000
ENV GID=1000
ENV USER="developer"
ENV JAVA_VERSION="17"
ENV ANDROID_TOOLS_URL="https://dl.google.com/android/repository/commandlinetools-linux-14742923_latest.zip"
ENV ANDROID_VERSION="36"
ENV ANDROID_BUILD_TOOLS_VERSION="36.1.0"
ENV ANDROID_ARCHITECTURE="x86_64"
ENV ANDROID_SDK_ROOT="/home/$USER/android-sdk"
ENV FLUTTER_CHANNEL="stable"
ENV FLUTTER_VERSION="3.38.8"
ENV FLUTTER_URL="https://storage.googleapis.com/flutter_infra_release/releases/$FLUTTER_CHANNEL/linux/flutter_linux_$FLUTTER_VERSION-$FLUTTER_CHANNEL.tar.xz"
ENV FLUTTER_HOME="/home/$USER/flutter"
ENV FLUTTER_WEB_PORT="8090"
ENV FLUTTER_DEBUG_PORT="42000"
ENV FLUTTER_EMULATOR_NAME="flutter_emulator"
ENV PATH="$ANDROID_SDK_ROOT/cmdline-tools/latest/bin:$ANDROID_SDK_ROOT/emulator:$ANDROID_SDK_ROOT/platform-tools:$ANDROID_SDK_ROOT/platforms:$FLUTTER_HOME/bin:$PATH"

# install all dependencies
ENV DEBIAN_FRONTEND="noninteractive"
RUN apt-get update \
  && apt-get install --yes --no-install-recommends openjdk-$JAVA_VERSION-jdk curl unzip sed git bash xz-utils libglvnd0 clang cmake ninja-build pkg-config libgtk-3-dev libstdc++-12-dev ssh xauth x11-xserver-utils libpulse0 libxcomposite1 libgl1-mesa-glx cmake \
  && rm -rf /var/lib/{apt,dpkg,cache,log}

# create user
RUN groupadd --gid $GID $USER \
  && useradd -s /bin/bash --uid $UID --gid $GID -m $USER

USER $USER
WORKDIR /home/$USER

# android sdk
RUN mkdir -p $ANDROID_SDK_ROOT \
  && mkdir -p /home/$USER/.android \
  && touch /home/$USER/.android/repositories.cfg \
  && curl -o android_tools.zip $ANDROID_TOOLS_URL \
  && unzip -qq -d "$ANDROID_SDK_ROOT" android_tools.zip \
  && rm android_tools.zip \
  && mkdir -p $ANDROID_SDK_ROOT/cmdline-tools/latest \
  && mv $ANDROID_SDK_ROOT/cmdline-tools/bin $ANDROID_SDK_ROOT/cmdline-tools/latest \
  && mv $ANDROID_SDK_ROOT/cmdline-tools/lib $ANDROID_SDK_ROOT/cmdline-tools/latest \
  && mv $ANDROID_SDK_ROOT/cmdline-tools/NOTICE.txt $ANDROID_SDK_ROOT/cmdline-tools/latest \
  && mv $ANDROID_SDK_ROOT/cmdline-tools/source.properties $ANDROID_SDK_ROOT/cmdline-tools/latest \
  && yes "y" | sdkmanager "build-tools;$ANDROID_BUILD_TOOLS_VERSION" \
  && yes "y" | sdkmanager "platforms;android-$ANDROID_VERSION" \
  && yes "y" | sdkmanager "platform-tools" \
  && yes "y" | sdkmanager "emulator" \
  && yes "y" | sdkmanager "system-images;android-$ANDROID_VERSION;google_apis_playstore;$ANDROID_ARCHITECTURE"

# flutter
RUN curl -o flutter.tar.xz $FLUTTER_URL \
  && mkdir -p $FLUTTER_HOME \
  && tar xf flutter.tar.xz -C /home/$USER \
  && rm flutter.tar.xz \
  && dart --disable-analytics \
  && flutter config --no-analytics \
  && flutter config --no-enable-linux-desktop \
  && flutter precache \
  && yes "y" | flutter doctor --android-licenses \
  && flutter doctor \
  && flutter update-packages
