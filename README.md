# ACIT 4640 - Lab 5 - Packer

Member:
- Charley Liao
- Jean Venter

---


In this lab, we are to modify the packer configuration file `web-front.pkr.hcl` to create a webserver on `nginx` platform.

## Repository Contents

- `web-front.pkr.hcl`: The packer template
- `files/`: Contains static website (`index.html`) and Nginx conig (`nginx.conf`)
- `scripts/`: Script to install Nginx and apploy configs.

## Configuations
The `web-front.pkr.hcl` had missing blocks to configure the image. Below discribes the boxes and how it works.

### 1. Source Configuration
- **Base Image**: Uses latest Debian AMI
- **Instance Type**: `t3.micro` and tombstone information for EC2 isntance for AWS

For the lab we first changed the source config block.
- Defined the source above

```bash
build {
  name = "web-nginx"
  sources = [
      "source.amazon-ebs.debian"
  ]
```

### 2. Build Process & Provisioners
These block specifics sequennce and provisioners:
1. **Inline Shell**
  - Creates custom web root `/web/html` (req. by `nginx.conf`)
  - Creates staginng directory `/tmp/web` (set-up scripts)
  - Sets `admin` user ownership without `sudo`

Here we changed the provisioners shell block. These commands will be run after the instance is created.

```bash
  provisioner "shell" {
    inline = [
      "echo creating directories",
      "sudo mkdir -p /web/html",
      "sudo chown -R admin:admin /web/html",
      "sudo chmod 775 /web/html",
      "mkdir -p /tmp/web"
    ]
  }
```

2. **File Uploads**
  - `files/index.html` goes into `/web/html/index.html` (Find web root)
  - `files/nginx.conf` goes into `/tmp/web/nginx.conf` (Staging area for script)

Changed the **_Upload HTML_** provisioner block to add the HTML file to our instance.

```bash
  provisioner "file" {
      source = "files/index.html"
      destination = "/web/html/index.html"
  }
```

Added the **_Upload Configuration_** provisioner block to add the nginx file to our instance.

```bash
  provisioner "file" {
      source = "files/nginx.conf"
      destination = "/tmp/web/nginx.conf"
  }
```


3. **Shell Scripts**
  - Run `install-nginx` and `setup-nginx` in the `/scripts` file

Added the privisoner scripts to install and setup nginx.

```bash
  provisioner "shell" {
      scripts = [
          "scripts/install-nginx",
          "scripts/setup-nginx"
      ]
  }
```

### 3. Usage Instructions

1. **Initialize Packer**
```bash
packer init .
```
2. **Validate**

```bash
packer validate web-front.pkr.hcl
```

3. **Build**
```bash
packer build web-front.pkr.hcl
```

### Screenshot

![alt text](Lab_5_Web_Success.png)
