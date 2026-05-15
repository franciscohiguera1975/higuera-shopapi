sudo usermod -aG www-data root
sudo chown -R root:www-data /opt/higuera-shopapi/staticfiles
sudo chmod -R 755 /opt/higuera-shopapi/staticfiles
sudo chmod -R 755 /opt/higuera-shopapi


[Unit]
Description=Gunicorn daemon for ShopAPI
After=network.target postgresql.service

[Service]
User=root
Group=www-data
WorkingDirectory=/opt/higuera-shopapi
Environment="PATH=/opt/higuera-shopapi/.venv/bin"
EnvironmentFile=/opt/higuera-shopapi/.env
ExecStart=/opt/higuera-shopapi/.venv/bin/gunicorn \
          --workers 3 \
          --bind unix:/run/gunicorn-shopapi.sock \
          --access-logfile /var/log/gunicorn-shopapi-access.log \
          --error-logfile /var/log/gunicorn-shopapi-error.log \
          config.wsgi:application
ExecReload=/bin/kill -s HUP $MAINPID
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target



sudo touch /var/log/gunicorn-shopapi-access.log
sudo touch /var/log/gunicorn-shopapi-error.log
sudo chown root:www-data /var/log/gunicorn-shopapi-*.log