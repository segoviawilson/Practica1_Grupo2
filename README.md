## INTEGRANTES.
| Nombre | Cargo | URL GitHub |
|---|:---:|---:|
| Daniel Alquinga | :technologist: Desarrollador | https://github.com/superdavi/Practica1_Grupo2.git |
| Daniel Baldeon | :technologist: Desarrollador | https://github.com/debpdhs/Practica1_Grupo2 |
| Bryan Miño | :technologist: Desarrollador |https://github.com/bmiomi/tareadocker |
| Wilson Segovia | :technologist: Desarrollador | https://github.com/segoviawilson/Practica1_Grupo2.git|
| Leonardo Tuguminago | :technologist: Desarrollador | https://github.com/Tuguminago/Proyectos.git |

# 1. Sistema de Gestión de Vehículos con Docker

Este proyecto implementa un sistema de gestión de vehículos utilizando Docker, MySQL y phpMyAdmin. El sistema permite administrar propietarios y sus vehículos mediante una base de datos relacional.

## 1.1. Arquitectura del Sistema

- **MySQL 8.3**: Base de datos para almacenar información de propietarios y vehículos
- **PhpMyAdmin 5.2.2**: Interfaz web para administración de la base de datos
- **Docker Network**: Red personalizada para comunicación entre contenedores
- **Volúmenes**: Gestionados por Docker y almacenados en /var/lib/docker/volumes/.

# 2. Configuración e Instalación

### PASO 1: 📂 Estructura de Archivos

```bash
    Proyecto Vehículos
    |
    |____ .env
    |____ README.md
    |____ despliegues.txt
    |____ init.sql
```
### PASO 2: Creación de Red Docker

---

```bash
docker network create --driver bridge netw-vehiculos
docker network ls
```

**Salida Esperada**

<img width="886" height="213" alt="image" src="https://github.com/user-attachments/assets/08eda610-37bd-415f-aed4-d355f445a46b" />

**Explicación:**

- Crea una red personalizada tipo `bridge` llamada `netw-vehiculos`
- Permite comunicación entre contenedores por nombre
- Aislamiento de red del resto del sistema
    
### PASO 3: Despliegue del Contenedor Docker MySQL

```bash
docker run -d \
--name db-mysql-vehiculos \
--network netw-vehiculos \
--env-file .env \
-v mysql_data:/var/lib/mysql \
-v "$PWD"/init.sql:/docker-entrypoint-initdb.d/init.sql \
-p 3306:3306 \
mysql:8.3
```
### Paso 3.1. Salida Esperada

<img width="725" height="443" alt="contenedor mysql" src="https://github.com/user-attachments/assets/407ce7d7-1577-4a08-a9e2-992a3385b065" />

**Explicación:**

- **MySQL Container**: Crea un contenedor con MySQL 8.3, monta un volumen persistente para los datos y ejecuta un script de inicialización
- **Network**: Ambos contenedores utilizan la red personalizada `netw-vehiculos`
- **Volumes**: Persistencia de datos MySQL y script de inicialización
- **Ports**: MySQL en puerto 3306, phpMyAdmin en puerto 3306

### Paso 3.2. Verificar Estado Up del Contendor

```bash
docker ps -a
```

<img width="1224" height="108" alt="listamos contenedor creado de mysql" src="https://github.com/user-attachments/assets/c9d014fa-6ef5-4936-943c-a4581284d3e9" />

### PASO 4: Despliegue del Contenedor Docker PhpMyAdmin

```bash
docker run -d \
--name web_phpmyadmin_vehiculos \
--network netw-vehiculos \
--env-file .env \
-e PMA_HOST=db-mysql-vehiculos \
-p 8080:80 \
phpmyadmin:5.2.2
```

### Paso 4.1. Salida Esperada

<img width="676" height="597" alt="despliegue contendor php" src="https://github.com/user-attachments/assets/01e8072f-3d3d-4de2-8e9e-9a3265084f44" />

**Explicación:**

- **phpMyAdmin Container**: Proporciona interfaz web conectada al contenedor MySQL
- **Network**: Ambos contenedores utilizan la red personalizada `netw-vehiculos`
- **Volumes**: Persistencia de datos MySQL y script de inicialización
- **Ports**: MySQL en puerto 80, phpMyAdmin en puerto 8080
  
### Paso 4.2. Verificar Estado Up del Contendor

```bash
docker ps -a
```

<img width="1215" height="142" alt="listar los dos contendores" src="https://github.com/user-attachments/assets/9ae7e739-fa84-48d4-926e-d5ec933529b8" />

### PASO 5: Ingreso al Portal del Servidor PhpMyAdmin

```bash
http://localhost:8080
```

![WhatsApp Image 2025-09-18 at 22 08 55](https://github.com/user-attachments/assets/ac27e52f-fec4-41c4-a693-28e36dffcc98)


### PASO 6: Credenciales de Ingreso


```bash
usuario: usuario
password: clave123
```


### PASO 7: Revisamos la Estructura de Base de Datos


<img width="1275" height="705" alt="revisamos la estructura de la bd" src="https://github.com/user-attachments/assets/dc74e0d5-f413-47cd-9795-c7bc27ee2c70" />

### PASO 8: Scritp init.sql

```sql
-- Crear tabla de propietario
CREATE TABLE propietario (
    id INT AUTO_INCREMENT PRIMARY KEY,
    cedula VARCHAR(15) NOT NULL,
    nombre VARCHAR(100) NOT NULL,
    telefono VARCHAR(20),
    direccion VARCHAR(150)
);

-- Crear tabla de vehiculo
CREATE TABLE vehiculo (
    id INT AUTO_INCREMENT PRIMARY KEY,
    placa VARCHAR(8) NOT NULL,
    marca VARCHAR(50) NOT NULL,
    modelo VARCHAR(50) NOT NULL,
    anio INT NOT NULL,
    propietario_id INT,
    FOREIGN KEY (propietario_id) REFERENCES propietario(id)
);

-- Insertar registros en propietario
INSERT INTO propietario (cedula, nombre, telefono, direccion) VALUES
('1111111111', 'Daniel Alquinga', '0992222222', 'ABC'),
('1222222222', 'Leonardo Tuguminago', '0993333333', 'DEF'),
('1333333333', 'Bryan Miño', '0994444444', 'GHI'),
('1444444444', 'Wilson Segovia', '0995555555', 'JKL'),
('1555555555', 'Daniel Baldeon', '0996666666', 'MNO');

-- Insertar registros en vehiculo
INSERT INTO vehiculo (placa, marca, modelo, anio, propietario_id) VALUES
('PBB-5555', 'Hyundai', 'i10', 2018, 1),
('PBB-2222', 'Mazda', 'Cx3', 2012, 2),
('PBB-3333', 'Chevrolet', 'Aveo', 2017, 3),
('PBB-9999', 'Nissan', 'Sentra', 2019, 4),
('PCC-4444', 'Toyota', '4Runner', 2022, 5);
```

**Explicación:**

- **Tabla propietario**: Almacena información personal de los dueños de vehículos
- **Tabla vehiculo**: Contiene datos de los vehículos con referencia al propietario
- **Relación**: Clave foránea entre vehículo y propietario (1:N)
- **Datos de prueba**: 5 propietarios y 5 vehículos para testing
  

### Configuración Adicional



**Archivo .env**

```env
MYSQL_ROOT_PASSWORD=admin123
MYSQL_DATABASE=dbVehiculos
MYSQL_USER=usuario
MYSQL_PASSWORD=clave123
```

### Comandos Útiles

```bash
# Ver contenedores en ejecución
docker ps

# Ver logs de un contenedor
docker logs db-mysql-vehiculos

# Conectar a MySQL directamente
docker exec -it db-mysql-vehiculos mysql -u root -p

# Detener todos los contenedores
docker stop db-mysql-vehiculos web_phpmyadmin_vehiculos

# Eliminar contenedores
docker rm db-mysql-vehiculos web_phpmyadmin_vehiculos

# Eliminar red
docker network rm netw-vehiculos
```


# 3. Conclusiones

**Logros Alcanzados**

- Implementación Exitosa: Se logró configurar un sistema distribuido utilizando Docker, separando la base de datos (MySQL) de la interfaz de administración (phpMyAdmin) en contenedores independientes.
- Gestión Eficiente de Redes: La creación de la red personalizada netw-vehiculos, permitió la comunicación segura entre contenedores, eliminando la necesidad de exponer servicios innecesarios al host.
- Persistencia de Datos Garantizada: El uso de volúmenes Docker (mysql_data), asegura que la información de propietarios y vehículos se mantenga intacta entre reinicios del sistema.
- Automatización de Inicialización: El script init.sql, automatiza la creación de tablas y datos de prueba, reduciendo errores manuales y garantizando consistencia en diferentes entornos.
- Separación de Configuración: El archivo .env, centraliza las variables sensibles, mejorando la seguridad y facilitando el despliegue en diferentes ambientes.

**Beneficios Obtenidos**

- Portabilidad: El sistema puede ejecutarse en cualquier máquina con Docker instalado
- Escalabilidad: Fácil agregar nuevos servicios o réplicas de contenedores
- Mantenibilidad: Cada servicio se actualiza independientemente
- Aislamiento: Los fallos en un contenedor no afectan a otros servicios
- Reproducibilidad: El entorno se puede recrear exactamente en cualquier momento


# 4. Recomendaciones.

 - Es necesario crear primero la red Docker y luego el contenedor, ya que este inconveniente se presento al momento de ejecutar la tarea. 
 - Se debe validar que la versión de MySQL sea compatible con phpMyAdmin, ya que una incompatibilidad entre ambas versiones podría causar errores al intentar levantar el contenedor.
