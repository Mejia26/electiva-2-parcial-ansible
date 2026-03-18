# electiva-2-parcial-ansible

# 🧪 Práctica: Ansible + Docker (Simulación de Servidores)

## 📌 Descripción

En esta práctica se implementó un entorno de automatización utilizando **Docker** para simular servidores Linux y **Ansible** para configurarlos de forma automática.

Se desplegaron dos servidores web (`node1` y `node2`) donde se instaló **nginx** y se publicó una página HTML personalizada utilizando un **playbook de Ansible**.

---

## 🎯 Objetivos

- Simular múltiples servidores usando contenedores Docker
- Configurar servidores remotamente con Ansible
- Automatizar instalación de software (nginx)
- Desplegar contenido web automáticamente
- Comprender el uso de inventarios y playbooks

---

## 🧰 Tecnologías utilizadas

- Docker
- Docker Compose
- Ansible
- Ubuntu 24.04 (contenedores)
- WSL (Windows Subsystem for Linux)

---

## 📁 Estructura del proyecto
├── Dockerfile
├── docker-compose.yml
├── inventory.ini
├── playbook.yml
└── site/
└── index.html



---

## ⚙️ Configuración de los contenedores

### Dockerfile

Se crea una imagen basada en Ubuntu con SSH habilitado y un usuario llamado `ansible`.

---

### docker-compose.yml

```yaml
services:
  node1:
    build: .
    container_name: node1
    ports:
      - "2222:22"
      - "8081:80"

  node2:
    build: .
    container_name: node2
    ports:
      - "2223:22"
      - "8082:80"
```

🗃️ Inventario de Ansible

Archivo inventory.ini:

[webservers]
node1 ansible_host=127.0.0.1 ansible_port=2222 ansible_user=ansible ansible_password=ansible ansible_ssh_common_args='-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null'
node2 ansible_host=127.0.0.1 ansible_port=2223 ansible_user=ansible ansible_password=ansible ansible_ssh_common_args='-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null'

[webservers:vars]
ansible_connection=ssh
ansible_become=true
ansible_become_method=sudo
ansible_become_password=ansible


⚙️ Playbook de Ansible

Archivo playbook.yml:

- name: Configurar servidores web
  hosts: webservers
  become: true

  tasks:

    - name: Instalar nginx
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Copiar página web
      copy:
        src: site/index.html
        dest: /var/www/html/index.html

    - name: Iniciar nginx
      service:
        name: nginx
        state: started
        enabled: true


🚀 Ejecución del proyecto
1. Levantar los contenedores
docker compose up --build -d

3. Verificar que estén corriendo
docker ps

5. Ejecutar el playbook
ansible-playbook -i inventory.ini playbook.yml

4. Verificar en el navegador o curl
curl http://localhost:8081
curl http://localhost:8082

👨‍💻 Autor
Guido Mejia Liranzo 2024-0197
