# Use the official Ubuntu base image
FROM --platform=linux/x86-64 ubuntu:latest

# Install necessary system packages
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive && \
    apt-get -y install --no-install-recommends \
        wget \
        curl \
        bzip2 \
        ca-certificates \
        gnupg \
        sudo \
        bash-completion \
        git \
        ffmpeg \
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
RUN mkdir -p /workspace && \
    # useradd -m $USERNAME && \
    usermod -aG sudo ${USERNAME} && \
    echo ${USERNAME} ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/${USERNAME} && \
    chmod 0440 /etc/sudoers.d/${USERNAME} && \
    usermod --shell /bin/bash ${USERNAME} && \
    mkdir -p /home/${USERNAME}/.conda && \
    chown -R ${USERNAME}:${USERNAME} /home/${USERNAME}/.conda && \
    chown -R ${USERNAME}:${USERNAME} /workspace && \
    ln -sfn /workspace /home/${USERNAME}/workspace

    # Add conda to PATH and set default shell
ENV PATH="/opt/conda/bin:/home/${USERNAME}/.local/bin:${PATH}"
SHELL ["/bin/bash", "-c"]

USER ${USERNAME}
WORKDIR /home/${USERNAME}

# enable bash completion
RUN conda init bash && conda config --set auto_activate_base false && \
    echo -e "\n################### Docker config. ###################" >> ~/.bashrc && \
    echo -e "source /usr/share/bash-completion/bash_completion" >> ~/.bashrc && \
    # https://superuser.com/questions/555310/bash-save-history-without-exit
    echo -e "export PROMPT_COMMAND='history -a'" >> ~/.bashrc && \
    echo -e "source ~/.bashrc" >> ~/.bash_profile

# VOLUME [ "/workspace", "/home/${USERNAME}/.conda" ]
