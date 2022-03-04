FROM ubuntu:20.04 AS downloader
RUN apt-get update && apt-get install -y wget unrar

WORKDIR /tmp
RUN wget http://www.atarimania.com/roms/Roms.rar && \
    # extract files
    unrar e Roms.rar Roms/

FROM pytorch/pytorch:1.7.0-cuda11.0-cudnn8-runtime

# Create non-root user
ARG USERNAME=me
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Create the user
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    #
    # [Optional] Add sudo support. Omit if you don't need to install software after connecting.
    && apt-get update \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

# ********************************************************
# * Anything else you want to do like clean up goes here *
# ********************************************************

# [Optional] Set the default user. Omit if you want to keep the default as root.
USER $USERNAME

# conda directory permission set
RUN sudo chown -R ${USER_UID}:${USER_GID} /opt/conda

# Install additional dependencies
RUN sudo apt-get update && sudo apt-get install -y python-opengl xvfb libglib2.0-0 ffmpeg

COPY requirements.txt /tmp/
RUN pip install -r /tmp/requirements.txt

# Install atari_py ROMs
COPY --from=downloader /tmp/Roms /tmp/Roms/
RUN python -m atari_py.import_roms /tmp/Roms
