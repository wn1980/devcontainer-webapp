# Use the official Ubuntu base image
FROM --platform=linux/x86-64 ubuntu:latest

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
        portaudio19-dev \
    && apt-get autoremove -y \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Download and install Miniconda
ARG VARIANT=3 # Python 3.x, adjust as needed.
ARG MINICONDA_VERSION=latest # or a specific version like py39_4.10.3
ARG MINICONDA_INSTALLER_SCRIPT="Miniconda3-${MINICONDA_VERSION}-Linux-x86_64.sh"
ARG MINICONDA_INSTALLER_URL="https://repo.anaconda.com/miniconda/${MINICONDA_INSTALLER_SCRIPT}"

RUN wget ${MINICONDA_INSTALLER_URL} && \
    bash ${MINICONDA_INSTALLER_SCRIPT} -b -p /opt/conda && \
    rm ${MINICONDA_INSTALLER_SCRIPT} && \
    /opt/conda/bin/conda update conda -y

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
    mkdir -p /home/${USERNAME}/.conda && \
    chown -R ${USERNAME}:${USERNAME} /home/${USERNAME}/.conda && \
    mkdir -p /home/${USERNAME}/.config && \
    chown -R ${USERNAME}:${USERNAME} /home/${USERNAME}/.config && \
    mkdir -p /workspace && \
    chown -R ${USERNAME}:${USERNAME} /workspace && \
    ln -sfn /workspace /home/${USERNAME}/workspace

    # Add conda to PATH and set default shell
ENV PATH="/opt/conda/bin:/home/${USERNAME}/.local/bin:${PATH}"
SHELL ["/bin/bash", "-c"]

USER ${USERNAME}
WORKDIR /home/${USERNAME}

# install nodejs
# Download and install nvm:
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash && \
    # in lieu of restarting the shell
    \. "$HOME/.nvm/nvm.sh" && \ 
    # Download and install Node.js:
    nvm install 22 && \
    # Verify the Node.js version:
    node -v && \
    # Should print "v22.14.0". 
    nvm current && \
    # Should print "v22.14.0".
    # Verify npm version:
    npm -v && \
    # Should print "10.9.2".
    npm install -g firebase-tools && \
    npm install -g typescript

# install ngrok
# RUN curl -sSL https://ngrok-agent.s3.amazonaws.com/ngrok.asc && \
#     sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null && \
#     echo "deb https://ngrok-agent.s3.amazonaws.com buster main" && \
#     sudo tee /etc/apt/sources.list.d/ngrok.list && \
#     sudo apt update && sudo apt install ngrok \
#     && apt-get autoremove -y \
#     && apt-get clean \
#     && rm -rf /var/lib/apt/lists/*
RUN curl -O https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-linux-amd64.tgz && \
    sudo tar -xvzf ./ngrok-v3-stable-linux-amd64.tgz -C /usr/local/bin && \
    ngrok --version 

# enable bash completion
RUN conda init bash && conda config --set auto_activate_base false && \
    echo -e "\n################### Docker config. ###################" >> ~/.bashrc && \
    echo -e "source /usr/share/bash-completion/bash_completion" >> ~/.bashrc && \
    # https://superuser.com/questions/555310/bash-save-history-without-exit
    echo -e "export PROMPT_COMMAND='history -a'" >> ~/.bashrc && \
    echo -e "source ~/.bashrc" >> ~/.bash_profile

# VOLUME [ "/workspace", "/home/${USERNAME}/.conda" ]
