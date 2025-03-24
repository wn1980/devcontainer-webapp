# Use the official Ubuntu base image
FROM --platform=linux/x86-64 ubuntu:24.04

# Install necessary system packages
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive && \
    apt-get -y install --no-install-recommends \
        git \
        wget \
        curl \
        bzip2 \
        ca-certificates \
        gnupg \
        sudo \
        bash-completion \
        build-essential \
        ffmpeg \
        alsa-utils \
        libasound2-dev \
        portaudio19-dev
    # && apt-get autoremove -y \
    # && apt-get clean \
    # && rm -rf /var/lib/apt/lists/*

# Download and install Miniconda
ARG VARIANT=3 # Python 3.x, adjust as needed.
ARG MINICONDA_VERSION=latest # or a specific version like py39_4.10.3
ARG MINICONDA_INSTALLER_SCRIPT="Miniconda3-${MINICONDA_VERSION}-Linux-x86_64.sh"
ARG MINICONDA_INSTALLER_URL="https://repo.anaconda.com/miniconda/${MINICONDA_INSTALLER_SCRIPT}"

RUN wget ${MINICONDA_INSTALLER_URL} && \
    bash ${MINICONDA_INSTALLER_SCRIPT} -b -p /opt/conda && \
    rm ${MINICONDA_INSTALLER_SCRIPT} && \
    /opt/conda/bin/conda update conda -y

# Add conda to PATH and set default shell
ENV PATH="/opt/conda/bin:/home/${USERNAME}/.local/bin:${PATH}"
SHELL ["/bin/bash", "-c"]

# install ngrok
RUN curl -O https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-linux-amd64.tgz && \
    tar -xvzf ./ngrok-v3-stable-linux-amd64.tgz -C /usr/local/bin && \
    rm -rf ./ngrok-v3-stable-linux-amd64.tgz && \
    ngrok --version 

# install nodejs
RUN curl -sL https://deb.nodesource.com/setup_22.x -o nodesource_setup.sh && \
    bash nodesource_setup.sh && \
    apt install nodejs -y && \
    rm -rf nodesource_setup.sh && \
    node -v && \
    npm -v && \
    npm install -g @railway/cli && \
    npm install -g n8n && \
    npm install -g pnpm && \
    npm install -g firebase-tools && \
    npm install -g typescript && \
    npm install -g mkcert && \
    npm install -g nodemon

# Create vscode user.
ARG USERNAME=ubuntu
ARG USER_UID=1000
ARG USER_GID=$USER_UID
RUN echo "Creating user: ${USERNAME}" && \
    # useradd -m $USERNAME && \
    usermod -aG sudo ${USERNAME} && \
    echo ${USERNAME} ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/${USERNAME} && \
    chmod 0440 /etc/sudoers.d/${USERNAME} && \
    usermod --shell /bin/bash ${USERNAME} && \
    mkdir -p /home/${USERNAME}/.n8n && \
    chown -R ${USERNAME}:${USERNAME} /home/${USERNAME}/.n8n && \
    mkdir -p /home/${USERNAME}/.conda && \
    chown -R ${USERNAME}:${USERNAME} /home/${USERNAME}/.conda && \
    mkdir -p /home/${USERNAME}/.config && \
    chown -R ${USERNAME}:${USERNAME} /home/${USERNAME}/.config && \
    mkdir -p /workspace && \
    chown -R ${USERNAME}:${USERNAME} /workspace && \
    ln -sfn /workspace /home/${USERNAME}/workspace
    
USER ${USERNAME}
WORKDIR /home/${USERNAME}
    
# enable bash completion
RUN conda init bash && conda config --set auto_activate_base false && \
    echo -e "\n################### Docker config. ###################" >> ~/.bashrc && \
    echo -e "source /usr/share/bash-completion/bash_completion" >> ~/.bashrc && \
    # https://superuser.com/questions/555310/bash-save-history-without-exit
    echo -e "export PROMPT_COMMAND='history -a'" >> ~/.bashrc && \
    echo -e "source ~/.bashrc" >> ~/.bash_profile

# clean
RUN sudo apt autoremove -y && \
    sudo apt clean && \
    sudo rm -rf /var/lib/apt/lists/*
