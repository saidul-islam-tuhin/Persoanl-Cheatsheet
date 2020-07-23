sudo apt-get install nginx

sudo systemctl status nginx # FOr check that nginx is running or not

cd /etc/nginx/sites-available/

sudo ln -s /etc/nginx/sites-available/

Link: https://www.digitalocean.com/community/tutorials/how-to-set-up-django-with-postgres-nginx-and-gunicorn-on-ubuntu-16-04

# Bash script for update
```sh
source env/bin/activate
pip install -r requirements/prod.txt

python manage.py clean_pyc
python manage.py migrate
python manage.py collectstatic --noinput
sudo systemctl daemon-reload
sudo systemctl restart gunicorn
sudo service nginx restart
deactivate
echo "*** End ***"
```
Executable: chmod +x update.sh
For run: ./update.sh
