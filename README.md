
# Drop

Drop is a modern, open-source solution for sharing local files through your browser. Drop extends file sharing beyond your local network, making it easy to connect and transfer files with anyone, anywhere.

File transfers are performed via secure P2P connections. The server is only used for initial connection setup, ensuring your data remains private and direct between devices.

Features:
- Automatic device detection on local networks
- Easy connection to external devices using a unique code
- Fast, secure, and private file sharing

## Technologies Used
- React / shadcn / tailwindcss
- PeerJS
- Node.js

## Project Structure
The project is divided into two main parts:
- `gdclient`: The frontend web client
- `gdserver`: The backend server for managing rooms and peer connections

The server should run behind a reverse proxy and relies on the X-Forwarded-For header to determine the originating IP address.

<details>
  <summary>Example docker-compose.yml</summary>
  
  This example assumes you have a running traefik instance.
  ```yaml
  services:
    client:
      build: ./Globaldrop/gdclient
      restart: "unless-stopped"
      volumes:
        - "/etc/timezone:/etc/timezone:ro"
        - "/etc/localtime:/etc/localtime:ro"
      labels:
        - "traefik.enable=true"
        - "traefik.http.routers.gdclient.rule=Host(`globaldrop.me`)"
        - "traefik.http.services.gdclient.loadbalancer.server.port=80"
      networks:
        - proxy
    server:
      build: ./Globaldrop/gdserver
      restart: "unless-stopped"
      volumes:
        - "/etc/timezone:/etc/timezone:ro"
        - "/etc/localtime:/etc/localtime:ro"
      labels:
        - "traefik.enable=true"
        - "traefik.http.routers.gdserver.rule=Host(`globaldrop.me`) && (PathPrefix(`/room`) || PathPrefix(`/peer`))"
        - "traefik.http.services.gdserver.loadbalancer.server.port=9000"
      networks:
        - proxy
      environment:
        - "PORT=9000"
        - "ROOM_ORIGIN=https://globaldrop.me"
  networks:
    proxy:
      external: true
      name: rproxy
  ```
</details>