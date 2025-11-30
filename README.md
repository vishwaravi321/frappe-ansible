# Ansible Automation for Frappe/ERPNext Production Deployment

This repository contains a modular, role-based Ansible automation framework for deploying a production-ready Frappe/ERPNext environment. The structure follows Ansible best practices with separate roles for system preparation, database configuration, Supervisor, Frappe Bench, and site initialization.

The automation ensures consistency, repeatability, and minimal manual intervention for any Frappe/ERPNext deployment.

---

## **1. Directory Structure**

```
.
├── apt/
│   └── tasks/main.yml                     # System package installation
│
├── frappe.yml                              # Playbook for Frappe deployment
│
├── group_vars/
│   └── all.yml                             # Global configuration variables
│
├── inventory.txt                           # Optional inventory format
├── inventory.yml                           # Primary Ansible inventory
│
├── mariadb/
│   ├── tasks/main.yml                      # MariaDB installation and config tasks
│   └── templates/50-server.cnf.j2          # MariaDB server configuration template
│
├── node/
│   └── tasks/main.yml                      # Node.js installation tasks
│
├── pip/
│   └── tasks/main.yml                      # Python package installation tasks
│
├── project/
│   └── tasks/main.yml                      # Frappe Bench setup and project structure
│
├── sites/
│   └── tasks/main.yml                      # Site creation and bench site operations
│
├── supervisor/
│   ├── handlers/main.yml                   # Supervisor service handlers
│   ├── tasks/main.yml                      # Supervisor configuration tasks
│   └── templates/restart.conf.j2           # Passwordless supervisorctl config
│
├── timezone/
│   └── tasks/main.yml                      # System timezone configuration
│
├── user/
│   └── tasks/main.yml                      # System user and permission setup
│
└── README.md                               # Project documentation
```

---

## **2. Playbooks Overview**

### **`frappe.yml`**

Main playbook orchestrating roles needed to prepare and deploy a complete Frappe/ERPNext setup.

Typical sequence includes:

1. Set system timezone
2. Install system APT packages
3. Install Node.js
4. Install Python packages via pip
5. Configure system user for bench
6. Install and configure MariaDB
7. Prepare Frappe Bench and project structure
8. Configure Supervisor services
9. Configure sites and apply production settings

---

## **3. Roles Overview**

### **3.1 apt/**

Installs all required system packages like:

* Git
* Curl
* Build-essential
* Redis
* Nginx
* Required libraries for Frappe

---

### **3.2 user/**

Creates and configures:

* The system user running bench (`frappe`, or defined in `all.yml`)
* Home directory
* Permissions & SSH access

---

### **3.3 timezone/**

Configures system timezone for consistency across logs and services.

---

### **3.4 node/**

Installs Node.js (LTS) required for Frappe assets build:

* Node Version Manager (optional)
* Global packages like yarn (if included in your tasks)

---

### **3.5 pip/**

Installs Python packages required globally or for bench:

* pip3
* setuptools
* wheel
* bench

---

### **3.6 mariadb/**

Handles:

* Installation of MariaDB server
* Database initialization
* Applying the `50-server.cnf.j2` configuration template
* Character set tuning for UTF8MB4
* Bind-address, logging and InnoDB optimizations for Frappe

---

### **3.7 project/**

Responsible for:

* Creating the Bench directory structure
* Initializing the bench
* Installing required apps
* Preparing the environment for production

---

### **3.8 sites/**

Handles site-level operations:

* Creating a new Frappe site
* Setting admin password
* Installing ERPNext and other apps
* Running migrations
* Enabling scheduler

---

### **3.9 supervisor/**

Configures:

* Supervisor program definitions for workers, scheduler, socketio
* Restart handlers
* Passwordless supervisorctl using `restart.conf.j2`
* Ensures bench is fully supervised & auto-restarts on failure

---

## **4. Variables**

All environment-specific configuration lives in:

```
group_vars/all.yml
```

---

## **5. Running the Playbook**

### **Inventory Setup**

Define target servers in:

```
inventory.yml
```

- Use sample-inventory.yml as reference.

---

### **Execute Deployment**

Run full provisioning:

```bash
ansible-playbook -i inventory.yml frappe.yml
```

---

## **6. Requirements**

* Ansible 2.10+
* Ubuntu 22.04 LTS or 24.04 LTS
  *Bench installation is handled automatically for both. No manual changes are required.*
* SSH access to the target server
* Sudo privileges on the remote host
* Python (python3) installed on the remote host

---


## **7. Notes**

* Templates ship secure defaults for MariaDB and Supervisor.
* All roles are idempotent and safe for repeated execution.
* This repository aligns with production deployment needs for ERPNext v13–v15.

---
# **Support / Collaboration**

For enhancements, issue reports, or collaboration requests related to this automation, feel free to open a ticket or start a discussion within the repository.
Contributions following the project standards and Ansible best practices are welcome.

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE.md) file for details.