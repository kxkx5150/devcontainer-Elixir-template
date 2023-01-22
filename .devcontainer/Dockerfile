FROM mcr.microsoft.com/vscode/devcontainers/base:ubuntu-22.04

ARG ELIXIR_VERSION="1.14.0"
ARG ERLANG_VERSION="25.0.3-1"


ARG USERNAME=vscode
ENV DEBIAN_FRONTEND noninteractive

ENV DISPLAY :0
ENV LANG=en_US.UTF-8 \
    LANGUAGE=en_US.UTF-8 \
    LC_ALL=C.UTF-8 


RUN apt-get update && apt-get install -y --no-install-recommends build-essential \
    git unzip dosfstools curl wget dpkg gdebi vim sudo ssh tzdata ca-certificates \
    dbus dbus-x11 alsa-utils pulseaudio pulseaudio-utils mesa-utils x11-apps
    # cmake clang clang-format clangd gdb nasm lldb\ 
    # python3 python-is-python3 python3-distutils python3-numpy python3-pip python3-tk \
    # nodejs \
    # libsdl2-dev libsdl2-image-dev libsdl2-mixer-dev libsdl2-net-dev libsdl2-ttf-dev \
    # libglu1-mesa-dev freeglut3-dev mesa-common-dev libglew-dev libglfw3-dev \
    # qtcreator qtbase5-dev qttools5-dev-tools qtbase5-examples qt5-qmake \
    # libgtk-4-bin libgtk-4-common libgtk-4-dev \


# install erlang
RUN wget https://packages.erlang-solutions.com/erlang/debian/pool/esl-erlang_${ERLANG_VERSION}~ubuntu~jammy_amd64.deb
RUN dpkg -i esl-erlang_${ERLANG_VERSION}~ubuntu~jammy_amd64.deb || true
RUN gdebi esl-erlang_${ERLANG_VERSION}~ubuntu~jammy_amd64.deb
RUN apt-get clean -y && rm -rf /var/lib/apt/lists

# install elixir
RUN git clone https://github.com/elixir-lang/elixir --depth 1 --branch v${ELIXIR_VERSION} && \
    cd elixir && \
    make && make install && \
    rm -rf /tmp/elixir-build
RUN su ${USERNAME} -c "mix local.hex --force \
  && mix local.rebar --force"


USER ${USERNAME}
RUN mkdir -p /home/${USERNAME}/workspace
WORKDIR /home/${USERNAME}/workspace

USER root
COPY set_uid.sh /home/
ENTRYPOINT ["/home/set_uid.sh"]
COPY ./.inject_bashrc /root
RUN cat /root/.inject_bashrc >> /home/${USERNAME}/.bashrc
CMD ["/bin/sh", "-c", "echo Container started ; trap \"exit 0\" 15; while sleep 1 & wait $!; do :; done"]

