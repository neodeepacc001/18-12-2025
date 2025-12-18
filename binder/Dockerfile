# Base image with Jupyter stack
FROM jupyter/base-notebook:python-3.9

# Switch to root for package installation
USER root

# Install system dependencies for desktop environment
RUN apt-get update && apt-get install -y \
    # X11 and desktop environment
    xvfb \
    x11vnc \
    fluxbox \
    xterm \
    # Web browser (Chromium for headless use)
    chromium-browser \
    chromium-chromedriver \
    # Utilities
    wget \
    net-tools \
    procps \
    # Development tools
    gcc \
    g++ \
    make \
    && rm -rf /var/lib/apt/lists/*

# Install noVNC for web-based VNC client
RUN git clone https://github.com/novnc/noVNC.git /opt/novnc \
    && git clone https://github.com/novnc/websockify /opt/novnc/utils/websockify \
    && ln -s /opt/novnc/vnc.html /opt/novnc/index.html

# Install Python dependencies for GUI applications
RUN pip install --no-cache-dir \
    pyvirtualdisplay \
    Pillow \
    numpy \
    matplotlib \
    selenium \
    notebook \
    jupyterlab

# Set up working directory and permissions
WORKDIR /home/$NB_USER
RUN chown -R $NB_UID:$NB_GID /home/$NB_USER /opt/novnc

# Switch back to notebook user
USER $NB_USER

# Copy configuration files
COPY --chown=$NB_UID:$NB_GID environment.yml /home/$NB_USER/
COPY --chown=$NB_UID:$NB_GID start /home/$NB_USER/

# Expose ports: 8080 for Jupyter, 6080 for noVNC
EXPOSE 8080 6080

# Health check
HEALTHCHECK CMD ps aux | grep '[x]11vnc' || exit 1

# Set the start script as entrypoint
ENTRYPOINT ["/home/jovyan/start"]
