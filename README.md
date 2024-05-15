# Python Odoo debug images
This repo contains Dockerfile to build your own image for debugging Odoo. 

- Is is tested on VSCode for MacOs and Linux. However should run on Windows too.
- Each folder contains image for the corresponding Odoo version.
- You can build images for other Odoo versions using existing one as example.
- Don't hesitate to share your updates and enhancements by opening a PR!

Windows users need to do these changes (tested with 14.0):

```
launch.json
                        "localRoot": "${HOME}/odoo",

settings.json
    "hostOdooRoot" : "${HOME}/odoo",

tasks.json
            "localPath": "C:/Users/YOUR_USERNAME/odoo"

        "customOptions": "--add-host=host.docker.internal:host-gateway", // without ' '

      "dockerRun": {
        "env": {
            "POSTGRES_DB": "postgres"
        },
```

**Copyright Cetmix OU 2022-now. Using this repo assumes you understand what you are doing. Use it on your own risk**
