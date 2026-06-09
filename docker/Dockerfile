ARG IMAGE_TAG="debian-12-v4"
FROM jlesage/baseimage-gui:${IMAGE_TAG}
ENV APP_NAME="Signal Messenger"

# HEEL BELANGRIJK: Schakel expliciet naar root voor de installatie
USER root

# Stap 1: Installeer de basisbenodigdheden
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    wget \
    gpg \
    ca-certificates \
    apt-transport-https \
    libgbm1 \
    procps

# Stap 2: Download en bewaar de gpg-sleutel van Signal
RUN wget -O- https://updates.signal.org/desktop/apt/keys.asc | gpg --dearmor -o /usr/share/keyrings/signal-desktop-keyring.gpg

# Stap 3: Voeg de Signal repository toe
RUN echo 'deb [arch=amd64 signed-by=/usr/share/keyrings/signal-desktop-keyring.gpg] https://updates.signal.org/desktop/apt xenial main' > /etc/apt/sources.list.d/signal-xenial.list

# Stap 4: Update de pakketlijst (als dit faalt, is er een repository/netwerkprobleem)
RUN apt-get update

# Stap 5: Installeer Signal Desktop (zonder --no-install-recommends voor alle GUI-deps)
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y signal-desktop

# Stap 6: Ruim de build-tools netjes op
RUN apt-get purge -y --auto-remove wget gpg && rm -rf /var/lib/apt/lists/*
 
# Post-requirements voor de Chrome-sandbox van Signal
RUN chown root:root /opt/Signal/chrome-sandbox && chmod 4755 /opt/Signal/chrome-sandbox

# Download en installeer het applicatie-icoon
RUN \
    APP_ICON_URL=https://upload.wikimedia.org/wikipedia/commons/thumb/4/41/Signal_ultramarine_icon.svg/600px-Signal_ultramarine_icon.svg.png && \
    install_app_icon.sh "$APP_ICON_URL"
    
# Volumes definitie
VOLUME ["/config"]

# Entrypoint script
COPY ./startapp.sh /startapp.sh
RUN chmod +x /startapp.sh

# Zet de naam van de applicatie binnen de GUI-omgeving
RUN set-cont-env APP_NAME "Signal Messenger"
