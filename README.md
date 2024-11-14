# P7.-DNS-Linux
DNS Linux

### Archivo YML
services:
  bind9:
    container_name: oscarserver
    image: internetsystemsconsortium/bind9:9.18
    platform: linux/amd64
    ports:
      - 56:53/tcp
      - 56:53/udp
    networks:
      oscar_subnet:
        ipv4_address: 10.5.15.1
    volumes:
      - ./conf:/etc/bind
      - ./zonas:/var/lib/bind
    restart: always
  cliente:
    container_name: oscarcliente
    image: alpine
    platform: linux/amd64
    tty: true
    stdin_open: true
    dns:
      - 10.5.15.1
    networks:
      oscar_subnet:
        ipv4_address: 10.5.15.2
        
networks:
  oscar_subnet:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.15.0/24
          gateway: 10.5.15.254
