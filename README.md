# 🚀 Simple Automated Deployment with Ansible & Docker

This project demonstrates how to automate the deployment of a Dockerized web application using **Ansible**, **Docker**, and **NGINX**.

The playbook installs Docker, configures the Docker service, adds the deployment user to the Docker group, copies the application files to the target server, and deploys the application using **Docker Compose**.

---

## 📌 Project Overview

This project automates the following tasks:

* Install Docker Engine and required plugins
* Enable and start Docker service
* Create Docker group
* Add deployment user to Docker group
* Verify Docker Compose installation
* Copy application files to remote server
* Destroy old containers and volumes
* Deploy the application using Docker Compose
* Serve static web content using NGINX

---

## 🛠️ Technologies Used

* **Ansible**
* **Docker**
* **Docker Compose**
* **NGINX**
* **Linux / Ubuntu**

---

## 📁 Project Structure

```text
simple-automated-deployment/
│
├── app/
│   └── index.html
│
├── app_test.yml
├── docker-compose.yml
├── default.conf
└── README.md
```

---

## ⚙️ Ansible Playbook

The main playbook is `app_test.yml`.

This playbook performs the following steps:

1. Installs Docker packages and plugins
2. Starts and enables Docker service
3. Creates the Docker group
4. Adds the current Ansible user to the Docker group
5. Checks Docker Compose version
6. Copies the application files to the remote server
7. Removes old containers and volumes
8. Deploys the updated application using Docker Compose

---

## 📄 Playbook Content

```yaml
---
- name: automate dockerized web app
  hosts: node
  become: yes
  tasks:
    - name: install docker
      apt:
        name: 
          - docker-ce
          - docker-ce-cli 
          - containerd.io 
          - docker-buildx-plugin
          - docker-compose-plugin
        state: present

    - name: enable and start docker
      service:
        name: docker
        state: started
        enabled: true
      register: docker_status

    - name: docker status 
      debug:
        var: docker_status

    - name: Create docker group
      group:
        name: docker
        state: present

    - name: Add user to docker group
      user:
        name: "{{ ansible_user }}"
        groups: docker
        append: yes

    - name: show docker compose
      command: docker compose version
      register: docker_compose_version

    - name: docker compose version
      debug:
        msg: "{{ docker_compose_version.stdout }}"

    - name: copy app
      copy:
        src: /home/mansi/project
        dest: /home/mansi
        owner: mansi
        mode: u=rw,g=r,o=r

    - name: destroy docker compose
      command: docker compose -f /home/mansi/project/docker-compose.yml down -v 

    - name: run app
      command: docker compose -f /home/mansi/project/docker-compose.yml up -d
```

---

## 🐳 Docker Compose Configuration

```yaml
version: '3'
services:
  nginx:
    container_name: app_nginx
    image: nginx
    ports:
      - "8090:80"
    networks:
      - app_network
    volumes:
      - ./app:/var/www/app
      - ./default.conf:/etc/nginx/conf.d/default.conf

networks:
  app_network:
```

---

## 🌐 NGINX Configuration

```nginx
server {
    listen 80;
    server_name localhost;

    root /var/www/app;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

This configuration serves the static application files from:

```text
/var/www/app
```

---

## 🚀 How to Run

Run the playbook using:

```bash
ansible-playbook -i inventory app_test.yml
```

---

## 🌍 Access the Application

After deployment, access the application in your browser:

```text
http://SERVER_IP:8090
```

Example:

```text
http://192.168.1.10:8090
```

---

## 🎯 Learning Outcomes

This project helped me practice:

* Infrastructure automation
* Configuration management
* Docker container deployment
* NGINX web server configuration
* Linux administration
* Remote deployment with Ansible
* DevOps workflow automation

---

## 👨‍💻 Author

**Muhammad Elmansi**
