<h1>Notes to run your applications in server</h1>

- Keep your application online and auto-restart if it crashes
    
    - Using NodeJS package: PM2
        ```bash
        npm i -g pm2
        pm2 start yourapp
        ```
        More detail: http://pm2.keymetrics.io/docs/usage/pm2-doc-single-page/
    
    - Running your app as a service
        
        - Register a service
        ```bash
        sudo vim /etc/systemd/system/YOUR_SERVICE_NAME.service
        ```
        - Paste in the command below
            ```bash
            Description=GIVE_YOUR_SERVICE_A_DESCRIPTION
            
            Wants=network.target
            After=syslog.target network-online.target
            
            [Service]
            Type=simple
            ExecStart=YOUR_COMMAND_HERE
            Restart=on-failure
            RestartSec=10
            KillMode=process
            
            [Install]
            WantedBy=multi-user.target
            ```
         - Reload services
            
            ```bash
            sudo systemctl daemon-reload
            ```
         - Enable the service
            
            ```bash
            sudo systemctl enable YOUR_SERVICE_NAME
            ```
         - Start the service
            
            ```bash
            sudo systemctl start YOUR_SERVICE_NAME
            ```
            
         - Check the status of your service
         
            ```bash
            systemctl status YOUR_SERVICE_NAME
            ```
          
- Running app in background
    
    - With `nohup`
        
        ```bash
        nohup ./your_script.sh & 
        ```
        
    To kill background processes by name
    
    ```bash
    pkill app_name
    ```