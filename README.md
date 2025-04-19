# Proyecto

[https://github.com/cristianmorac/docker-ansible-wordpress.git](https://github.com/cristianmorac/docker-ansible-wordpress.git)

### Creación de contenedores en Docker

- **Carpeta image-docker**
    - Contiene los dockerfile para crear las imágenes de ansible y el servidor que se instalara wordpress
        - servidor ansible se crea con el dockerfile-ansible
        - servidor wordpress se crea con el dockerfile-ubuntu
    - comando para la creación de imágenes
        - `docker build -f dockerfile-ansible -t srv-ansible .`
        - `docker build -f dockerfile-ansible -t srv-wordpress .`
    - comando para visualizar las imágenes creadas `docker images`
- **Crear red para la conexión entre los contenedores**
    - `docker network create --driver bridge --subnet 172.25.0.0/16 mi_red_web`
    - comando para visualizar la red creada `docker network ls`
- **Crear contenedores con volumen en una ruta local**
    - `docker run -it --name [name-container] --network mi_red_web  -v [ruta-local]:[ruta-conainer] [image-docker] bash`
    - ej: `docker run -it --name wordpress --network mi_red_web  -v C:\\Users\\Admin\\OneDrive\\Documentos\\docker\\Proyect-wordpress\\wordpress:/home/ubuntu srv-ansible bash`
- **Conexión remota por ssh en ambos servidores**
    - Activar el servicio sshd de manera manual `/usr/sbin/sshd`
    - Validar que se haya activado `ps aux | grep [s]shd`
    - Validar que el puerto este activo `ss -tuln`
    - Crear claves ssh `ssh-keygen -t rsa`
    - Copiar clave ssh servidor que contiene ansible al servidor que se instalara wordpress `ssh-copy-id [username]@[ip-serverWordpress]`
    - validar conexión por ssh `ssh [username]@[ip-serverWordpress]`
- **Entorno virtual**
    - Crear entorno virtual `python3 -m venv [mi_entorno]`
    - Activar entorno virtual `source env/bin/activate`
- Instalar ansible `pip install ansible`

### Ansible

- **Crear colección** → `ansible-galaxy collection init project.webWordpress`
- **Crear roles**

Ingresar a la carpeta `project/webWordpress` y crear los siguientes roles:

- `ansible-galaxy init roles/apache2`
- `ansible-galaxy init roles/mariadb`
- `ansible-galaxy init roles/wordpress`
- **Configuraciones iniciales**
    - **inventory.ini:** Archivo de configuración de los servidores que se van a realizar los cambios
    - **playbook.yaml:** Archivo que se van a ejecutar los roles y una pre tarea para actualizar el servidor
- Validar conexión ssh desde ansible `ansible -i inventory.ini web -m ping` , si todo es correcto mostrara el siguiente mensaje  `[ip-server] | SUCCESS =>`
- **Configuración de roles**
    - **Rol apache2:**
        - **Handlers**:
            - inicio y reinicio de apache2
        - **tasks**:
            - Instalación de apache2
    - **Rol mariadb**
        - **Handlers:**
            - inicio de mariadb
        - **tasks**:
            - **main.yaml:** Instalación y configuración de mariadb
            - **python.yaml:** instalación de dependencias MySQL
    - **Rol wordpress**
        - **tasks**:
            - Instalación y configuración de wordpress
- **Archivos globales para ejecución**
    - **inventory.ini:** inventario de los servidores y variables globales
    - **vars.yaml:** variables de la base de datos
    - **playbook.yaml:** Se encuentran los roles y tareas que para instalación de wordpress

`ansible-playbook -i inventory.ini playbook.yaml.yaml`