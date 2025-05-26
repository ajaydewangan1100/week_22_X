### AWS EC2 related

1. CMD to change permission (in windows) : `icacls "path_to_file" /grant Administrators:F`
   (Run CMD to change permission of `.pem` file, which is stored on our local system)

CMD to put under user data (on launch template creation time) -
(first do -> `cd /home/ubuntu/.nvm/versions/node/` to check node version, then put same version under export PATH)

    ```
    #!/bin/bash
    export PATH=$PATH:/home/ubuntu/.nvm/versions/node/<verion_folder>/bin/
    echo "hi there before"
    echo "hi there after"
    npm install -g pm2
    cd /home/ubuntu/<Project_folder>
    pm2 start index.js
    pm2 save
    pm2 startup
    ```

(pm2 is a process manager, we can run a application under EC2 using our local machine BASH, but when we exit from BASH then the application will stop, PM2 is the library which is able to run the process in background)
