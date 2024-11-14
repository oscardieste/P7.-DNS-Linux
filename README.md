# P7.-DNS-Linux
DNS Linux

## Configuración del archivo `docker-compose.yml`

```yaml
# (contenido YAML aquí)
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
```
### Archivos de configuración

named.conf.local

``` zone "asircastelao.int" {
    type master;
    file "/etc/bind/db.asircastelao.com";   # Archivo con los registros DNS de la zona
};
```
named.conf.options
```options {
    directory "/var/cache/bind";
    recursion yes;                      # Permitir la resolución recursiva
    allow-query { any; };               # Permitir consultas desde cualquier IP
    dnssec-validation no;               # Permitir realizar apk update sin problemas
    forwarders {
        8.8.8.8;                        # Google DNS
        1.1.1.1;                        # Cloudflare DNS
    };
    listen-on { any; };                 # Escuchar en todas las interfaces
    listen-on-v6 { any; };
};
```
```db.asircastelao.int

$TTL    604800              #TTL (Time To Live) predeterminado en segundos

@       IN      SOA     ns.asircastelao.int. admin.asircastelao.int. (
                        2         # Serial: número de serie del archivo de zona.
                        604800    # Refresh: intervalo de actualización en segundos 
                        86400     # Retry: tiempo de reintento en segundos (24 horas).
                        2419200   # Expire: tiempo de expiración en segundos 
                        604800 )  # Negative Cache TTL: tiempo en segundos para almacenar en caché las respuestas negativas

@       IN      NS      ns.asircastelao.int.
                            # Define el servidor de nombres (NS) para la zona asircastelao.int, en este caso, "ns.asircastelao.int."

ns       IN      A       10.5.15.1
                            # Registro A que asigna la IP 10.5.15.1 al nombre "ns.asircastelao.int", indicando la dirección del servidor de nombres.

test    IN      A       10.5.15.4
                            # Define un subdominio "test.asircastelao.int" y le asigna la IP 10.5.15.4.

alias   IN      CNAME   web.asircastelao.com.
                            # Define un alias (CNAME) "alias.asircastelao.int" que apunta a "web.asircastelao.com".

texto       IN      TXT     "Este es un registro de texto para asircastelao.com"
                            # Registro de texto (TXT) para "texto.asircastelao.int" con la descripción
```
### Comprobación de la Configuración




