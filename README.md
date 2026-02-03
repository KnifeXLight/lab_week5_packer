In this lab we need to modify the file 'wk5-Packer-intro-lab-start'.

First we changed the build block.
Here we set the buildname to be web-nginx, and grabbed the source name from the above source block.
```
build {
  name = "web-nginx"
  sources = [
      "source.amazon-ebs.debian"
    # COMPLETE ME Use the source defined above
  ]
```

Next we changed the provisioners shell block. These commands will be run after the instance is created.
Here we made the correct directory and allowed ownership for the base user. And we made a temporary working directory at the end.
```
  provisioner "shell" {
    inline = [
      "echo creating directories",
      "sudo mkdir -p /var/www/html",
      "sudo chown -R admin:admin /var/www/html",
      # COMPLETE ME add inline scripts to create necessary directories and change directory ownership.
      # See nginx.conf file for root directory where files will be served.
      # Files need appropriate ownership for default user
      "mkdir -p /tmp/web"
    ]
  }
```

Next we changed the first provisioner block to add the HTML file to our instance.
```
  provisioner "file" {
      source = "files/index.html"
      destination = "/var/www/html/index.html"
    # COMPLETE ME add the HTML file to your image
  }
```

Then we changed the second provisioner block to add the nginx file to our instance.
```
  provisioner "file" {
      source = "files/nginx.conf"
      destination = "/tmp/nginx.conf"
    # COMPLETE ME add the nginx.conf file to your image
  }
```

Lastly we added an additional script to install and setup nginx.
```
  provisioner "shell" {
      scripts = [
          "scripts/install-nginx",
          "scripts/setup-nginx"
      ]
  }
  # COMPLETE ME add additional provisioners to run shell scripts and complete any other tasks
}
```
