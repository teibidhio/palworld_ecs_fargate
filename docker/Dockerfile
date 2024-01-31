FROM rockylinux:8-minimal

# Install glibc and libstdc++ packages
RUN microdnf update -y && \
    microdnf install -y glibc.i686 libstdc++.i686 tar gzip findutils xdg-user-dirs

# Create steam user and steam home directory
RUN mkdir -p /home/steam && \
    echo 'steam:x:1000:1000:steam:/home/steam:/bin/bash' >> /etc/passwd && \
    echo 'steam:x:1000:' >> /etc/group && \
    echo 'steam:!::' >> /etc/shadow  && \
    chown -R steam:steam /home/steam

# Switch to steam user
USER steam

# Install SteamCMD
RUN mkdir -p /home/steam/Steam && \
    mkdir -p /home/steam/.steam/sdk64 && \
    cd /home/steam/Steam && \
    curl -sqL "https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz" | tar zxvf - && \
    /bin/bash ./steamcmd.sh +quit && \
    ln -s /home/steam/Steam/linux64/steamclient.so /home/steam/.steam/sdk64/steamclient.so

# Game specific stuff starts here
# # Download and install the Palworld dedicated server
RUN /home/steam/Steam/steamcmd.sh +login anonymous +app_update 2394010 validate +quit && \
    rm -rf /home/steam/Steam/steamapps/common/PalServer/steamclient.so && \
    cp /home/steam/Steam/steamapps/common/PalServer/linux64/steamclient.so /home/steam/Steam/steamapps/common/PalServer/ && \
    mkdir -p /home/steam/Steam/steamapps/common/PalServer/Pal/Saved/Config/LinuxServer && \
    echo "ServerName=PalWorld" > /home/steam/Steam/steamapps/common/PalServer/Pal/Saved/Config/LinuxServer/PalWorldSettings.ini && \
    echo "ServerDescription=A simple PalWorld" >> /home/steam/Steam/steamapps/common/PalServer/Pal/Saved/Config/LinuxServer/PalWorldSettings.ini && \
    echo "AdminPassword=1lonelyduck" >> /home/steam/Steam/steamapps/common/PalServer/Pal/Saved/Config/LinuxServer/PalWorldSettings.ini && \ 
    echo "ServerPlayerMaximum=8" >> /home/steam/Steam/steamapps/common/PalServer/Pal/Saved/Config/LinuxServer/PalWorldSettings.ini && \ 
    echo "PublicPort=8211" >> /home/steam/Steam/steamapps/common/PalServer/Pal/Saved/Config/LinuxServer/PalWorldSettings.ini && \   
    echo "Difficulty=normal" >> /home/steam/Steam/steamapps/common/PalServer/Pal/Saved/Config/LinuxServer/PalWorldSettings.ini && \ 
    echo "IsMultiplay=true" >> /home/steam/Steam/steamapps/common/PalServer/Pal/Saved/Config/LinuxServer/PalWorldSettings.ini


# Done installing and configuring game, move on to runtime stuff
# Game traffic
EXPOSE 8211/udp
# Public server registration, or something
EXPOSE 27015/udp
# RCON traffic
EXPOSE 25575/tcp

# # Set the working directory to steam home directory
WORKDIR /home/steam

# # Run the Palworld dedicated server using SteamCMD with the provided Steam credentials
ENTRYPOINT ["/home/steam/Steam/steamapps/common/PalServer/PalServer.sh"]

